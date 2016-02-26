# TigerConnect JS SDK

This repository contains binary distributions of JS SDK released by [TigerConnect](http://tigertext.com/tigerconnect).

If you have any questions, comments, or issues related to this repository then please contact the team by emailing [tigerconnect@tigertext.com](mailto:tigerconnect@tigertext.com).

<!-- MarkdownTOC depth=2 autolink=true bracket=round -->

- [Overview](#overview)
  - [Installation](#installation)
  - [Quick Example](#quick-example)
  - [Client](#client)
  - [Models](#models)
- [TigerConnect.Client](#tigerconnectclient)
  - [Authentication](#authentication)
  - [Events](#events)
  - [Users](#users)
  - [Messages](#messages)
  - [Groups](#groups)
  - [Conversations](#conversations)
  - [Organizations](#organizations)
  - [Search](#search)
  - [Metadata](#metadata)
  - [Contact](#contact)
  - [License](#license)

<!-- /MarkdownTOC -->

## Overview

The TigerConnect JS SDK provides a simple way to enhance your web applications with intelligent, secure messaging from TigerConnect.

In order to use the TigerConnect JS SDK you must be a registered developer. All aspects of this setup are covered in detail in the [TigerConnect Documentation](https://tigerconnect.readme.io/).

The SDK supports both web and node.js.

## Installation

### Web

#### Using [Bower](http://bower.io)

```sh
bower install --save tigerconnect
```

```html
<script src="bower_components/tigerconnect/tigerconnect-sdk-web.min.js"></script>
```

Or, manually download [tigerconnect-sdk-web.min.js](https://raw.githubusercontent.com/tigerconnect/js-sdk-release/master/tigerconnect-sdk-web.min.js) and include it in a `script` tag:

```html
<script src="path/to/tigerconnect-sdk-web.min.js"></script>
```

### Node

```sh
npm install --save tigerconnect
```

```js
var TigerConnect = require('tigerconnect')
var client = new TigerConnect.Client(config)
```

## Quick Example

```js
var client = new TigerConnect.Client({ defaultOrganizationId: 'some-org-id' })

client.signIn('user@mail.com', 's3cr3t', { udid: 'unique-device-id' }).then(function (session) {
  onSignedIn(session)
})

function onSignedIn(session) {
  console.log('Signed in as', session.user.displayName)
  
  client.messages.sendToUser(
    'someone@mail.com',
    'hello!'
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
}
```

See code example in [the examples folder](examples).


## Client

All communication with TigerConnect is performed with a single `TigerConnect.Client ` instnace. The client can hold a single authenticated user, and will execute all commands on this user's behalf.

Create a client:

```js
var client = new TigerConnect.Client()
```


## Models

Before diving in into the different methods, it's important to know the model types and relationships:

### `User`

A user account

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

An organization is a scope where messages are being sent. A user can be a member of multiple organizations, but messages that are being sent in organization #1 won't be visible at organization #2.

| Property        | Type                  | Description                                                                    |
|-----------------|-----------------------|--------------------------------------------------------------------------------|
| `id`            | `string`              | ID                                                                             |
| `name`          | `string`              | Organization name                                                              |
| `memberCount`   | `int`                 | Number of members                                                              |
| `conversations` | `Array<Conversation>` | List of active conversations in the org for the logged in user                 |
| `unreadCount`   | `int`                 | A sum of all unread message counts in all conversations in the organization    |


### `Group`

A group of users in a specific organization. Points to a single conversation.

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
| `conversation`    | `Conversation`        | The conversation of the group. Can be only one.                                |
| `organizationId`  | `string`              | ID of `organization`                                                           |
| `createdAt`       | `string`              | ISO date and time of creation                                                  |



### `Message`

An incoming or outgoing message, can be a one on one or in a group conversation.

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
| `counterParty`             | `User/Group`                       | Either the `recipient` or the `group` value, depends on the message type   |
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

Message status (read/delivered recipts) per user. Used in group conversations where each user has a different status on each message.

| Property          | Type      | Description                                    |
|-------------------|-----------|------------------------------------------------|
| `id`              | `string`  | ID                                             |
| `user`            | `User`    | A user responsible for the status              |
| `userId`          | `string`  | ID of `user`                                   |
| `message`         | `Message` | Message containing the status                  |
| `messageId`       | `string`  | ID of `message`                                |
| `recipientStatus` | `string`  | `NEW`, `DELIVERED`, `TO_BE_READ`, `READ`       |


### `Conversation`

A collection of messages between current user and a counter party - a user or a group, within an organization.

| Property            | Type              | Description                                                                       |
|---------------------|-------------------|-----------------------------------------------------------------------------------|
| `id`                | `string`          | ID                                                                                |
| `messages`          | `Array<Message>`  | List of messages                                                                  |
| `unreadMessages`    | `Array<Message>`  | List of unread messages                                                           |
| `unreadCount`       | `int`             | A count of all unread messages in the list                                        |
| `lastMessage`       | `Message`         | The last message in the conversation (useful when showing a list of conversation) |
| `counterParty`      | `User/Group`      | Either the `recipient` or the `group` value, depends on the conversation type     |
| `counterPartyId`    | `string`          | ID of `counterParty`                                                              |
| `counterPartyType`  | `string`          | Values: `user`, `group`                                                           |
| `organization`      | `Organization`    | Organization where this conversation takes place                                  |
| `organizationId`    | `string`          | ID of `organization`                                                              |



# TigerConnect.Client

Create an instance of `TigerConnect.Client`:

```js
var client = new TigerConnect.Client(config)
```

##### Client optional Configuration and default values

```js
var client = new TigerConnect.Client({
  defaultOrganizationId: null, // use the default org to send all messages in a specific organization unless specified otherwise
  eventsFeatures: 'is-typing',
  logLevel: 'error', // debug/info/warn/error/fatal
})
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

### Listening to conversation changes

Whenever a conversation changes, the `conversation:change` is fired. The change can be anything, and isn't specified in the event. Examples for changes: new message, message recalled, `lastMessage` changed, message status changed, `unreadCount` changed etc.

```js
client.on('conversation:change', function (conversation) {
  console.log(
    'conversation with',
    conversation.counterParty.displayName,
    'changed'
  )
})
```


## Users

### `client.users.find`

Retrieves a `User` by ID, email or phone number.

```js
client.users.find(id: string):Promise.<User,Error>
```

#### Example

```js
client.users.find('some-user-id').then(function (user) {
  console.log('found user', user.displayName)
}, function (err) {
  if (err.code == 'not-found') console.log('User not found')
  else console.log('Error')
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
    organizationId: string,

    // if sender organizationId is different than recipient's
    senderOrganizationId: string, // defaults to organizationId
    recipientOrganizationId: ?string // defaults to senderOrganizationId
  }
):Promise.<Message,Error>
```

#### Examples

##### Specifying `defaultOrganizationId` on client

```js
var client = new TigerConnect.Client({ defaultOrganizationId: 'some-org-id' })

client.messages.sendToUser(
  'some-user-id',
  'hello!',
).then(function (message) {
  console.log('sent', message.body, 'to', message.recipient.displayName)
}, function (err) {
  console.log('Error sending message')
})
```

##### Specifying `organizationId` in the method

```js
client.messages.sendToUser(
  'some-user-id',
  'hello!',
  { organizationId: 'some-org-id' }
).then(function (message) {
  console.log('sent', message.body, 'to', message.recipient.displayName)
}, function (err) {
  console.log('Error sending message')
})
```

### `client.messages.sendToGroup`

Sends a message to a group. If `groupId` is sent as an actual `Group` instance, `organizationId` is not required, as a group already carries an `organizationId`.

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
  console.log(
    'created new group:',
    group.name,
    'with members',
    group.members.map(function (user) { return user.displayName }).join(', ')
  )
  console.log('sent', message.body, 'to group', group.name)
}, function (err) {
  console.log('Error sending message')
})
```

### `client.messages.recall`

Lets current user recall their own message.

```js
client.messages.recall(
  groupId: string|Group,
  body: string,
  {
    organizationId: ?string
  }
):Promise.<void,Error>
```

#### Example

```js
client.messages.recall('some-message-id').then(function () {
  console.log('message recalled!')
}, function (err) {
  if (err.code == 'permission-denied') console.log('Current user cannot recall this message')
  else console.log('Error recalling message')
})
```


## Groups

### `client.groups.create`

Creates a new group with specified members and current user.

```js
client.groups.create({
  organizationId: string,
  name: string,
  memberIds: Array<User|string>,
  replayHistory = true,
  metadata: ?Object
}):Promise.<Group,Error>
```

#### Example

```js
client.groups.create({
  organizationId: 'some-org-id',
  name: 'The greatest group'
  memberIds: [
    'some-user-id-1',
    'some-user-id-2',
    'some-user-id-3',
  ]
}).then(function (group) {
  console.log(
    'created new group:', group.name,
    'with members', group.members.map(function (user) { return user.displayName }).join(', ')
  )
}, function (err) {
  console.log('Error creating group')
})
```

### `client.groups.update`

Updates an existing group. Always appending the current user to the group `memberIds` even when edited.

```js
client.groups.update({
  name: string,
  memberIds: Array<User|string>,
  replayHistory: bool = true,
  metadata: ?Object
}):Promise.<Group,Error>
```

#### Example

```js
client.groups.create({
  organizationId: 'some-org-id',
  name: 'A group',
  memberIds: ['some-user-id-1', 'some-user-id-2']
}).then(function (group) {

	client.groups.update({
	  name: 'A new name for the group',
	  memberIds: ['some-user-id-1', 'some-user-id-2', 'some-user-id-3']
	}).then(function (group) {
	  console.log(
	    'group updated:', group.name,
	    'members: ', group.members.map(function (user) { return user.displayName }).join(', ')
	  )
	})
}, function (err) {
  console.log('Error creating group')
})
```

### `client.groups.find`

Retrieves a `Group` by ID.

```js
client.groups.find(id: string):Promise.<Group,Error>
```

#### Example

```js
client.groups.find('some-group-id').then(function (group) {
  console.log('found group', group.name)
}, function (err) {
  if (err.code === 'not-found') console.log('Group not found')
  else console.log('Error')
})
```

### `client.groups.findAll`

Retrieves all `Group`s of current user. Uses the search API behind the scenes.

`includeMembers: true` will also fetch all the users from the server

`includeMetadata: true` will also fetch all the metadata for the groups

```js
client.groups.findAll({ includeMetadata: bool, includeMembers: bool }):Promise.<Group[],Error>
```

#### Example

```js
client.groups.findAll({ includeMembers: true }).then(function (allGroups) {
  console.log('found', allGroups.length, 'groups')
  allGroups.forEach(function (group) {
    console.log(group.name, group.members.map(function (user) { return user.displayName }).join(', '))
  })
})
```

### `client.groups.destroy`

Deleted a group

```js
client.groups.destroy(groupId: string|Group):Promise.<void,Error>
```

#### Example

```js
client.groups.destroy('some-group-id').then(function () {
  console.log('deleted group')
})
```

### Group Member Management

Add/remove member(s) from a group

```js
client.groups.addMember(groupId: string|Group, memberIds: string|User)
client.groups.addMembers(groupId: string|Group, memberIds: string[]|User[])
client.groups.removeMember(groupId: string|Group, memberIds: string|User)
client.groups.removeMembers(groupId: string|Group, memberIds: string[]|User[])
```

#### Example

```js
client.groups.addMembers('some-group-id', ['some-user-5', 'some-user-6']).then(function () {
  console.log('added!')
})

client.groups.removeMembers('some-group-id', ['some-user-7', 'some-user-8']).then(function () {
  console.log('removed!')
})
```


## Conversations

### `client.conversations.findAll`

Retrieves all active `Conversation`s of current user.

```js
client.conversations.findAll():Promise.<Conversation[],Error>
```

#### Example

```js
client.conversations.findAll().then(function (conversations) {
  console.log('found', conversations.length, 'conversations')
  conversations.forEach(function (conversation) {
  	 if (conversation.counterPartyType === 'user') {
      console.log(
        conversation.counterParty.displayName,
      )
  	 }
  	 else if (conversation.counterPartyType == 'group') {
      console.log(
        conversation.counterParty.displayName,
        'group, members:',
        conversation.counterParty.members.map(function (user) { return user.displayName }).join(', ')
      )
  	 }
  })
})
```


## Organizations

### `client.organizations.findAll`

Retrieves all `Organization`s of current user

```js
client.organizations.findAll():Promise.<Organization[],Error>
```

#### Example

```js
client.organizations.findAll().then(function (organizations) {
  console.log(
    'found', organizations.length, 'organizations:',
    organizations.map(function (organization) { return organization.name }).join(', ')
  )
})
```

### `client.organizations.find`

Retrieves an `Organization` by ID.

```js
client.organizations.find(id: string):Promise.<Organization,Error>
```

#### Example

```js
client.organizations.find('some-organization-id').then(function (organization) {
  console.log('found organization', organization.name)
}, function (err) {
  if (err.code === 'not-found') console.log('Organization not found')
  else console.log('Error')
})
```

## Search

### `client.search.query`

Retrieves search results by a query

```js
client.search.query({
  types = [],
  query = {},
  continuation = null,
  includeDisabled = false,
  excludeTokens = [],
  resultsFormat = 'entities', // or 'ids'
  organizationId = null,
  sort = [],
  includeMetadata = false
}):Promise.<Organization,Error>
```

#### Example

```js
client.search.query({
  types: ['user', 'group'],
  query: { displayName: 'al' },
  organizationId: 'some-org-id'
}).then(function (results) {
  console.log('found', results.length, 'results')
  results.forEach(function (result, i) {
    console.log(i, result.entityType, result.entity.displayName)
  })
})
```

`includeMetadata: true` will also load all metadata for all entities, assigning them into each `result.metadata`


## Metadata

### `client.metadata.find `

Retrieves metadata of an entity (supported entities - `Group`/`User`)

```js
client.metadata.find(id: string|Group|User):Promise.<Object,Error>
```

#### Example

```js
client.metadata.find('some-group-id').then(function (metadata) {
  console.log('metadata for some-group-id is ', metadata)
}, function (err) {
  if (err.code === 'not-found') console.log('Entity not found')
  else console.log('Error')
})
```

### `client.metadata.findMulti`

Retrieves metadata of an multiple entities (supported entities - `Group`/`User`). Gets a list of IDs (or entities) and returns a map where keys are IDs and values are the metadata object.

```js
client.metadata.findMulti(id: string[]|Group[]|User[]):Promise.<Object,Error>
```

#### Example

```js
client.metadata.findMulti(['some-group-id', 'some-group-id-2', 'some-user-id']).then(function (map) {
  console.log('metadata for some-group-id: ', map['some-group-id'])
  console.log('metadata for some-group-id-2: ', map['some-group-id-2'])
  console.log('metadata for some-user-id: ', map['some-user-id'])
})
```

### `client.metadata.update`

Adds/updates metadata on an entity (partial update)

```js
client.metadata.update(id: string|Group|User):Promise.<void,Error>
```

#### Example

```js
client.metadata.update('some-group-id', { 'new_key': 'new_value' }).then(function (entireData) {
  console.log('metadata for some-group-id updated and is now: ', entireData)
}, function (err) {
  if (err.code === 'not-found') console.log('Entity not found')
  else console.log('Error')
})
```


### `client.metadata.fullUpdate`

Overwrites the entire metadata of an entity

```js
client.metadata.fullUpdate(id: string|Group|User):Promise.<void,Error>
```

#### Example

```js
client.metadata.fullUpdate('some-group-id', { 'new_key': 'new_value' }).then(function (newData) {
  console.log('metadata for some-group-id set to', newData)
}, function (err) {
  if (err.code === 'not-found') console.log('Entity not found')
  else console.log('Error')
})
```


## Contact

If you have any questions please reach out to the TigerConnect team any time by emailing [tigerconnect@tigertext.com](mailto:tigerconnect@tigertext.com).

## License

TigerConnect is licensed under our [Developer Terms of Use](https://developer.tigertext.com/developer-terms-of-use/).
