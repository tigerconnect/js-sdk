# TigerConnect JS SDK

## Quick Example

```js
var client = new TigerConnectClient()

client.signIn('user@mail.com', 's3cr3t').then(function (session) {
  console.log('Signed in as', session.user.displayName)
  
  client.messages.sendToUser(
    'someone@mail.com',
    'hello!',
    { senderOrganizationId: 'some-org-id' }
  ).then(function (message) {
    console.log('sent', message.body, 'to', message.recipient.displayName)
  })
  
  client.on('message', function (message) {
    console.log(
      'message event',
      message.sender.displayName,
      'to',
      message.recipient.displayName,
      ':',
      message.body
    )
  })
})
```


## Client

All communication with TigerConnect is performed with a single `TigerConnectClient ` instnace. The client can hold a single authenticated user, and will execute all commands on this user's behalf.

## Models

Before diving in into the different methods, it's important to know the model types and relationships:

### `User`

#### Properties

| Property        | Type      | Description                                    |
|-----------------|-----------|------------------------------------------------|
| `id`            | `string`  | ID                                             |
| `displayName`   | `string`  | User's name to display                         |
| `firstName`     | `?string` | First name                                     |
| `lastName`      | `?string` | Last name                                      |
| `status`        | `?string` | User's current status message                  |
| `avatarUrl`     | `?string` | Full URL of avatar picture                     |
| `presence`      | `string`  | Values: `UNKNOWN`, `AVAILABLE`, `UNAVAILABLE`  |


### `Organization`


| Property        | Type                  | Description                                                                    |
|-----------------|-----------------------|--------------------------------------------------------------------------------|
| `id`            | `string`              | ID                                                                             |
| `name`          | `string`              | Organization name                                                              |
| `memberCount`   | `int`                 | Number of members                                                              |
| `conversations` | `Array<Conversation>` | List of active conversations in the org for the logged in user                 |
| `unreadCount`   | `int`                 | A sum of all unread message counts in all conversations in the organization    |

### `Message`

| Property                   | Type                               | Description                                                                |
|----------------------------|------------------------------------|----------------------------------------------------------------------------|
| `id`                       | `string`                           | ID                                                                         |
| `body`                     | `string`                           | Message body                                                               |
| `sender`                   | `User`                             | User who sent the message                                                  |
| `senderId`                 | `string`                           | ID of `sender`                                                             |
| `recipient`                | `?User`                            | In 1 on 1 messages - a User who is the recipient                           |
| `recipientId`              | `?string`                          | ID of `recipient`                                                          |
| `group`                    | `?Group`                           | In group messages - the Group which the message was sent to                |
| `groupId`                  | `?string`                          | ID of `group`                                                              |
| `counterParty`             | `User|Group`                       | Either the `recipient` or the `group` value, depends on the message type   |
| `counterPartyId`           | `string`                           | ID of `counterParty`                                                       |
| `counterPartyType`         | `string`                           | Values: `user`, `group`                                                    |
| `conversation`             | `Conversation`                     | The conversation which the message is on                                   |
| `recipientOrganization`    | `Organization`                     | Organization of the recipient                                              |
| `recipientOrganizationId`  | `string`                           | ID of `recipientOrganization`                                              |
| `senderOrganization`       | `Organization`                     | Organization of the sender                                                 |
| `senderOrganizationId`     | `string`                           | ID of `senderOrganization `                                                |
| `ttl`                      | `int`                              | Time to live in seconds                                                    |
| `sortNumber`               | `int`                              | A sequential number for ordering messages by time                          |
| `createdAt`                | `string`                           | ISO date string                                                            |
| `attachments`              | `Array<Attachment>`                | Array of attachments on a message                                          |
| `data`                     | `Array<Object>`                    | Array of metadata objects on a message                                     |
| `senderStatus`             | `string`                           | Relevant only when the sender is the current user. Values: `NEW`, `SENDING`, `SENT`, `FAILURE`. If not the current user, value is `NA` |
| `recipientStatus`          | `string`                           | Relevant only in 1 on 1 messages. Values: `NEW`, `DELIVERED`, `TO_BE_READ`, `READ`. In group messages value is `NA`, use `statusesPerRecipient` for all members statuses |
| `statusesPerRecipient`     | `Array<MessageStatusPerRecipient>` | Array of all recipients and their read/delivery status of this message |

### `MessageStatusPerRecipient`

| Property          | Type      | Description                                    |
|-------------------|-----------|------------------------------------------------|
| `id`              | `string`  | ID                                             |
| `user`            | `User`    | A user responsible for the status              |
| `userId`          | `string`  | ID of `user`                                   |
| `message`         | `Message` | Message containing the status                  |
| `messageId`       | `string`  | ID of `message`                                |
| `recipientStatus` | `string`  | `NEW`, `DELIVERED`, `TO_BE_READ`, `READ`       |


### `Conversation`

| Property            | Type              | Description                                                                       |
|---------------------|-------------------|-----------------------------------------------------------------------------------|
| `id`                | `string`          | ID                                                                                |
| `messages`          | `Array<Message>`  | List of messages                                                                  |
| `unreadMessages`    | `Array<Message>`  | List of unread messages                                                           |
| `unreadCount`       | `int`             | A count of all unread messages in the list                                        |
| `lastMessage`       | `Message`         | The last message in the conversation (useful when showing a list of conversation) |
| `counterParty`      | `User|Group`      | Either the `recipient` or the `group` value, depends on the conversation type     |
| `counterPartyId`    | `string`          | ID of `counterParty`                                                              |
| `counterPartyType`  | `string`          | Values: `user`, `group`                                                           |
| `organization`      | `Organization`    | Organization where this conversation takes place                                  |



# TigerConnectClient

Create an instance of `TigerConnectClient`:

```js
var client = new TigerConnectClient(config)
```

## Authentication

### `client.signIn`

Signs a user in with userId and password on a new device.

Sets all future API requests to be on behalf of the user.

```js
client.signIn(
  userId: string,
  password: string,
  { udid: string }
):Promise.<Session,Error>
```

#### Example

```js
client.signIn(
  'someone@email.com',
  's3cr3t',
  { udid: '3bc656cc-025b-49d1-aacf-ddf1b22a3a4b' }
).then(function (session) {
  console.log('Signed in as', session.user.displayName)
  console.log(session.auth.key, session.auth.secret)
}, function (err) {
  console.log('err')
})
```


### `client.authenticate`

Signs a user in with key and secret.

```js
client.authenticate(
  key: string,
  secret: string
):Promise.<Session,Error>
```

#### Example

```js
client.authenticate('c96049e39f883e2', '2ee8bff8cb41').then(function (user) {
  console.log('Signed in as', user.displayName)
})
```

### `client.signOut`

Signs a user out and clears all data.

```js
client.signOut():Promise.<Null,Error>
```

#### Example

```js
client.signOut().then(function () {
  console.log('Signed out successfully')
})
```

## Events


## Users


## Messages


## Conversations

## Organizations

## Metadata

## Search

