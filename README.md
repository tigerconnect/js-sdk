# TigerConnect JS SDK

## Quick Example

```js
var client = new TigerConnectClient()

client.signIn('user@mail.com', 's3cr3t', { udid: 'unique-device-id' }).then(function (session) {
  console.log('Signed in as', session.user.displayName)
  
  client.messages.sendToUser(
    'someone@mail.com',
    'hello!',
    { senderOrganizationId: 'some-org-id' }
  ).then(function (message) {
    console.log('sent', message.body, 'to', message.recipient.displayName)
  })

  client.events.connect()
  
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

| Property        | Type      | Description                                    |
|-----------------|-----------|------------------------------------------------|
| `id`            | `string`  | ID                                             |
| `displayName`   | `string`  | User's display name                            |
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


### `Group`

| Property          | Type                  | Description                                                                    |
|-------------------|-----------------------|--------------------------------------------------------------------------------|
| `id`              | `string`              | ID                                                                             |
| `name`            | `string`              | Group name                                                                     |
| `displayName`     | `string`              | Same as `name`                                                                 |
| `description`     | `?string`             | Group description                                                              |
| `members`         | `Array<User>`         | List of users in this group.                                                   |
| `memberIds`       | `Array<string>`       | IDs of `members`                                                               |
| `memberCount`     | `int`                 | Number of members (`ROOM` type doesn't have `members`)                         |
| `avatarUrl`       | `?string`             | Full URL of group picture                                                      |
| `groupType`       | `?string`             | `GROUP`, `ROOM`, `DISTRIBUTION_LIST`                                           |
| `organization`    | `Organization`        | Organization where this conversation takes place                               |
| `organizationId`  | `string`              | ID of `organization`                                                           |
| `createdTime`     | `string`              | ISO date and time of creation                                                  |



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
| `createdAt`                | `string`                           | ISO date and time of creation                                              |
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
| `organizationId`    | `string`          | ID of `organization`                                                              |



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

### `client.getCurrentUser`

Retrieves current authenticated user.


```js
client.getCurrentUser():User
```

#### Example

```js
console.log('Signed in as ', client.getCurrentUser().displayName)
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

### `client.events.connect`

After a successful sign in, this will connect the client to the server's event stream.

When event stream opens for the first time for a user, all message hisory downloads.

### `client.events.disconnect`

Disconnect from event stream. Called automatically on sign out.

### Listening to messages

After connecting to event stream, the `message` events fires on each new message. If the user is connected on multiple devices, this event will fire also on sent messages from other devices.

```js
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
```


## Users

### `client.users.find`

Retrieves a user from server by ID, email or phone number.

```js
client.users.find(id: string):Promise.<User,Error>
```

#### Example

```js
client.users.find('some-user-id').then(function () {
  console.log('Signed out successfully')
}, function (err) {
  if (err.code == 'not-found') {
    console.log('User not found')
  }
})
```


## Messages

### `client.messages.sendToUser`

Sends a message to a user (1 on 1 message)

```js
client.messages.sendToUser(
  userId: string|User, // can also be a phone number or an email address
  body: string,
  {
    senderOrganizationId: string,
    recipientOrganizationId: ?string // default = senderOrganizationId
  }
):Promise.<Message,Error>
```

#### Example

```js
client.messages.sendToUser(
  'some-user-id',
  'hello!',
  { senderOrganizationId: 'some-org-id' }
).then(function (message) {
  console.log('sent', message.body, 'to', message.recipient.displayName)
}, function (err) {
  console.log('Error sending message')
})
```

### `client.messages.sendToGroup`

Sends a message to a group

```js
client.messages.sendToGroup(
  groupId: string|Group,
  body: string,
  {
    organizationId: ?string
  }
):Promise.<Message,Error>
```

#### Example

```js
client.messages.sendToGroup(
  'some-group-id',
  'hello!',
  { organizationId: 'some-org-id' }
).then(function (message) {
  console.log('sent', message.body, 'to', message.recipient.displayName)
}, function (err) {
  console.log('Error sending message')
})
```

### `client.messages.sendToNewGroup`

Creates a new group with specified members and current user, and sends a message.

```js
client.messages.sendToNewGroup(
  userIds: Array<string|User>,
  body: string,
  {
    organizationId: string,
    groupName: ?string,
    groupMetadata: ?Object
  }
):Promise.<Message,Error>
```

#### Example

```js
client.messages.sendToNewGroup(
  [
    'some-user-id-1',
    'some-user-id-2',
    'some-user-id-3',
  ]
  'hello!',
  {
    organizationId: 'some-org-id',
    groupName: 'The greatest group'
  }
).then(function (message) {
  var group = message.group
  console.log('created new group:', group.name, 'with members', group.members.map(function (user) { return user.displayName }).join(', '))
  console.log('sent', message.body, 'to group', group.name)
}, function (err) {
  console.log('Error sending message')
})
```


## Groups

### `client.groups.create`
### `client.groups.update`
### `client.groups.find`
### `client.groups.findAll`
### `client.groups.destroy`
### `client.groups.addMembers`
### `client.groups.addMember`
### `client.groups.removeMembers`
### `client.groups.removeMember`


## Conversations

### `client.conversations.findAll`


## Organizations

### `client.organizations.findAll`
### `client.organizations.find`


## Metadata

### `client.metadata.fullUpdate`
### `client.metadata.update`
### `client.metadata.find`
### `client.metadata.findMulti`


## Search

### `client.search.search`
