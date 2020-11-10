# Change Log

## [12.5.0beta3] - 2020-11-10

### General Changes

- Updates to `client.calls` service

### Fixes

- Add `client.calls` logic necessary for compatibility with [Web Calling Demo](https://github.com/tigerconnect/web-calling-demo)

## [12.5.0beta2] - 2020-11-10

### General Changes

- New `client.calls` service for VoIP Calling. Demo app available [here](https://github.com/tigerconnect/web-calling-demo). Documentation to be added soon.
- New function [messages.downloadAttachmentBlobAndUrl](https://tigerconnect.github.io/js-sdk/api/messages/downloadAttachmentBlobAndUrl) to efficiently retrieve both the attachment blob and its URL.

### Fixes

- Fix build issue that was causing the web bundle to not load.
- Fix an issue where `conversations.fetchTimeline()` and `conversations.selectConversation()` were causing the timeline of previously downloaded conversations to be cleared unnecessarily.
- Miscellaneous optimizations and improvements.

## [11.7.0] - 2020-09-18

### General Changes

- Improved efficiency when creating groups with metadata using the SDK.
- Improved responsiveness of `conversations.findAll()` when CStore is on and the user has a large roster with more than 200 items.
- `messages.findAttachment()`: If an attachment cannot be located, return `null` instead of throwing an error.

### Fixes

- Fix an issue where a group that was created with metadata did not surface the metadata right away.

## [10.8.0] - 2020-06-18

### General Changes

- The `User` model now has a `removedFromOrg` field, that if set to `true`, indicates that the user has been removed from all organizations.
- Removed users whose names are not known will be given a `displayName` of `Removed User`.

### Fixes

- Fix an issue where `conversation.messages` would not be updated after a message was recalled or expired. As a sidenote: for most use-cases, we recommend using `conversation.timeline` instead, since its contents will be sorted, up-to-date on recipient status flags, and will better reflect the content fetched during `client.conversations.fetchTimeline()` calls.
- Fix an issue where `conversation.lastMessage` was sometimes not set to the most up-to-date value.
- Improve performance of `client.conversations.clearMessageStatuses`, which is especially noticeable when viewing conversations for groups that have a very large number of messages.
- Various other bugfixes and performance improvements.

## [10.3.0] - 2020-05-13

### General Changes

- The generic `client.messages.forward()` function has been deprecated in favor of `forwardToUser()`, `forwardToGroup()`, etc. and will throw an error if an attempt is made to use it
- The generic `client.messages.send()` function has been deprecated in favor of `sendToUser()`, `sendToGroup()`, etc. and will throw an error if an attempt is made to use it
- Various bugfixes and performance improvements

### Fixes

- Fix issue where sending a message immediately after receiving other messages could cause all subsequent messages to be missing for up to 7 minutes
- In `conversations.fetchTimeline()` and `conversations.selectConversation()`:
  - Fix issue where the conversation content was being fetched more than what was strictly necessary
  - NOTE: If you would like to display a loading indicator in a particular direction if more content is available, please read the [explanation of the itemsEstimate continuation field in the introduction of the fetchTimeline docs](https://tigerconnect.github.io/js-sdk/api/conversations/fetchTimeline) for guidance on how to implement that correctly.

## [8.4.1] - 2020-01-31

### General Changes

- [typing:change](https://tigerconnect.github.io/js-sdk/events/typing-change)
  - Change contract to include `contextId` and `contextType` parameters

### Fixes

- In `conversations.fetchTimeline()` and `conversations.selectConversation()`:
  - Fix issue where the user joins a group and then receives more than a certain number unread bangs and messages, when they next select the group, sometimes not all messages will be fetched, if the first fetched item happens to be a bang.
  - Fix issue where the user enters a forum or a group with `replayHistory = true`, reads all of the messages, leaves, and then rejoins, then only the very top (lowest `sort_number`) of the conversation will be fetched.
- `conversations.markAsRead()`:
  - Messages will now consistently get marked as Read.
- `conversations.find()`:
  - Allow this to work without a preceding `conversations.findAll()`
- `messages.sendToUser()`:
  - Fix an issue where sending a message to a new user resulted in an incorrect `conversation.lastMessage`

## [8.0.2] - 2019-12-04

### General Changes

- The contract for `typingStatus.startTyping()` has changed, and it is now required to pass `organizationId` as part of the 2nd `options` parameter. See https://tigerconnect.github.io/js-sdk/api/typingStatus/startTyping for details.
- The contract for `typingStatus.stopTyping()` has changed, and it is now required to pass `organizationId` as part of the 2nd `options` parameter. See https://tigerconnect.github.io/js-sdk/api/typingStatus/stopTyping for details.

### Fixes

- Fix a build issue that was causing incorrect minified code to be generated for the browser release, which was causing a `<random variable name> is not defined` error when instantiating the client.

### Known Issues

- In `conversations.fetchTimeline()` and `conversations.selectConversation()`:
  - If the user joins a group and then receives more than a certain number unread bangs and messages, when they next select the group, sometimes not all messages will be fetched, if the first fetched item happens to be a bang.
  - If the user enters a forum or a group with `replayHistory = true`, reads all of the messages, leaves, and then rejoins, then only the very top (lowest `sort_number`) of the conversation will be fetched.
  - If the user is at the very top of the conversation, and the last fetch of the timeline continuation happened to end on the very first item of the conversation, `itemsEstimate` might not be `0`, which can trigger a one-time fetch in that direction to make sure that there aren't any remaining items. This case should be relatively rare compared to past SDK releases.
- `conversations.markAsRead()`:
  - In some cases, messages in certain conversations might not get marked as read. When this happens, messages might appear Read for a few seconds and then switch back after syncing the real values from the server. This is a platform issue and might potentially be fixed without need for an SDK release.

## [8.0.0] - 2019-12-02

### General Changes

- `conversations.selectConversation()`:
  - The contract has been changed to return `Promise<Object>`, where Object contains these two fields:
    - `addedMessages`: Number of items that were added to the Timeline
    - `previousFirstUnreadMessage`: A message model representing the `firstUnreadMessage` before all messages in this conversation are marked as 'Read'.
- If a message attachment without a name is downloaded, a default `name` of `"attachment"` will be provided.
- If an attempt is made to upload metadata with non-string values, an error will be thrown. This is an improvement to the previous behavior of allowing the operation to happen, but having non-string values silently discarded by the platform.

### `conversation:change` event removal

- The `client.on('conversation:change', ...)` event has been removed.
- The `conversation.on('change', ...)` event has also been removed.
- Please use the following pattern instead: `client.models.Conversation.on('afterInject', (resource, conversation) => { /* code that uses `conversation` */ })`

### Fixes

- In `conversations.fetchTimeline()` and `conversations.selectConversation()`:
  - Significant performance improvements have been made. In most cases, unnecessary fetches of timeline when at the end of the conversation have been removed, in which case `itemsEstimate` will already be set to `0`.
  - Fix issue where messages sent by the current user sometimes do not appear in `conversation.timeline`.
- In `conversations.markAsRead()`:
  - Improvements have been made to the speed at which messages will appear Read.
- In the `Conversation` model:
  - Fix issue where `firstUnreadMessage` will have an incorrect value in some situations.
  - Fix issue where `isUnread` will have an incorrect value in some situations.
  - Fix issue where `highestSortNumber` will have an incorrect value in some situations.
  - Fix issue where `unreadMessageCount` will have an incorrect value in some situations.
- In the `Organization` model:
  - Fix issue where `lastIncomingMessageSortNumber` had an incorrect value in some situations.
- `events.connect()`:
  - Significant improvements have been made to stability of the EventSource connection.
- Fix issue where `message.shouldEnsureRecipientStatus` would sometimes have an incorrect value of `false` after receiving an SSE event or downloading the timeline, which could cause message statuses to not be fetched when calling `ensureRecipientStatus()`.

### Known Issues

- In `conversations.fetchTimeline()` and `conversations.selectConversation()`:
  - If the user is at the very top of the conversation, and the last fetch of the timeline continuation happened to end on the very first item of the conversation, `itemsEstimate` might not be `0`, which can trigger a one-time fetch in that direction to make sure that there aren't any remaining items. This case should be relatively rare compared to past SDK releases.
- `conversations.markAsRead()`:
  - In some cases, messages in certain conversations might not get marked as read. When this happens, messages might appear Read for a few seconds and then switch back after syncing the real values from the server. This is a platform issue and might potentially be fixed without need for an SDK release.

## [7.7.0] - 2019-10-24

### General Changes

- `events.connect()`:
  - The contract has been changed to not return a `Promise`.
  - The connection should be recovered more quickly after the computer goes offline, and will no longer emit `console.log` messages when this happens.

### Polyfills

- If you are using automatically-installed polyfills:
  - The number of polyfills has been reduced to the minimum set that is used by the SDK.
  - Feel free to review the [Polyfills section of the Installation docs](https://tigerconnect.github.io/js-sdk/quickstart/js/installation#polyfills) for details on the exact polyfills which are automatically included.
- If you are using manually-specified polyfills:
  - The SDK will now throw an error if the right polyfills have not been provided.
  - Please review the [Polyfills section of the Installation docs](https://tigerconnect.github.io/js-sdk/quickstart/js/installation#polyfills) for details on the exact polyfills which should be included.

### Fixes

- Fix issue where `conversations.markAsDelivered()` call may fail with 400 status.
- `conversations.fetchTimeline()`:
  - Fix timing issue where new messages did not appear in `conversation.timeline` until the second time the function is called.
- `conversations.selectConversation()`:
  - The `minItemsToFetch` parameter now works as the documentation describes.
  - Fix timing issue where new messages did not appear in `conversation.timeline` until the second time the function is called.
- In the `Organization` model:
  - `organization.conversations` will sometimes be sorted incorrectly, due to the conversation having an incorrect `highestSortNumber` value.
  - Messages sent to a broadcast list may cause an invalid `lastMessage` object to be provided in any 1-on-1 conversations with users who are in that broadcast list.
  - The `lastMessage` field will sometimes be missing or contain a message which is not the last message in that conversation.
- Fix issue where after a user joins a private group, full conversation history was shown from before the user had joined.
- Fix several issues around messages sent while the user has Do-Not-Disturb Auto-forwarding enabled

### Known Issues

The following issues are present in this SDK release and are planned to be fixed in an upcoming SDK or Platform release:

- In `conversations.fetchTimeline()` and `conversations.selectConversation()`:
  - Messages sent by the current user sometimes do not appear in `conversation.timeline`.
- In the `Conversation` model:
  - `firstUnreadMessage` will have an incorrect value in some situations
  - `isUnread` will have an incorrect value in some situations
  - `highestSortNumber` will have an incorrect value in some situations
  - `unreadMessageCount` will have an incorrect value in some situations

### Documentation Updates

- Add [Do Not Disturb](https://tigerconnect.github.io/js-sdk/quickstart/js/do-not-disturb) page which explains the different kinds of Do-Not-Disturb functionality that are available.

## [7.3.1] - 2019-09-30

### Overview

- Extensive SDK documentation is now available at <https://tigerconnect.github.io/js-sdk/>, which includes Quickstart guides for Android SDK, iOS SDK, and JS SDK, along with further reference material for the JS SDK.
- It is recommended to review this documentation, since many JS SDK calls have changed since the last SDK release. Many of these changes are a result of the items listed in the "Performance Improvements" section below.
- The release repository has been moved to <https://github.com/tigerconnect/js-sdk>.
- Support for the following features has been implemented but not yet fully documented. Please contact your TigerConnect support representative for further information and pricing plans for these features.
  - Roles and Role Tags
  - Role Escalations
  - EHR Connect

### Performance Improvements

- The JS SDK will no longer download every message when executing `events.connect()`, which will signficantly improve application load performance. Please read <https://tigerconnect.github.io/js-sdk/quickstart/js/getting-inbox> for further details.
- Message expiration is now implemented more performantly, and will no longer cause lag to user input (such as typing messages) in IE 11.
- Many other performance improvements have been implemented throughout the SDK based on feedback from customers who utilize the TigerConnect Messenger website at <https://login.tigerconnect.com/app/messenger/index.html>.

### General Changes

- **Breaking change**: It is now required to pass `partnerName` as a config setting during `new TigerConnect.Client()` calls. It's recommended to pass `version` as well. Please contact your TigerConnect support representative to determine the correct value of `partnerName` for your SDK integration.
- **Breaking change**: The `.off()` method has been removed from anything which has an `.on()` method; use `.removeListener()` instead of `.off()`
- The SDK will now automatically sign out when the browser window is closed.
- `client.messages.send()` and `client.messages.forward()` will now use firstName instead of displayName of initial group members when creating new groups without a predefined group name.
- The `includeMetadata` option has been removed from `client.search.query()`. Metadata will now always be returned.
- The `client.user.find()` command has been modified to accept an optional `organizationId` parameter. It is recommended to always supply that parameter.

### Offline Notifications

- The `client.notifications` API implements offline notifications, and is intended to be used as an optional feature while the user is logged out due to inactivity.
- While connected, this API emits events containing anonymized message content and unread counts which are suitable for displaying in operating system notifications or browser notifications.
- This API is used internally by the TigerConnect Desktop App.
- Documentation has not yet been written for this API. Please contact your TigerConnect support representative if you are interested in using it.

### Known Issues

The following issues are present in this SDK release and are planned to be fixed in an upcoming SDK or Platform release:

* `conversations.fetchTimeline()`:
  * The `markAsDelivered` parameter is ignored, and the fetched messages will always be marked as Delivered. Please contact your TigerConnect support representative if you are interested in being able to fetch messages without automatically marking them as Delivered.
  * There may be a timing issue where new messages do not appear in `conversation.timeline` until the second time the function is called.
* `conversations.selectConversation()`:
  * The `markAsDelivered` parameter is ignored, and the fetched messages will always be marked as Delivered. Please contact your TigerConnect support representative if you are interested in being able to fetch messages without automatically marking them as Delivered.
  * The `minItemsToFetch` parameter does not yet work as the documentation describes, and will effectively always be `20`. This will be fixed in the next SDK release.
  * There may be a timing issue where new messages do not appear in `conversation.timeline` until the second time the function is called.
* `conversations.markAsDelivered()`:
  * This call may fail with 400 status. The issue will be fixed in a near-future SDK release.
* In the `Conversation` model:
  * `firstUnreadMessage` will have an incorrect value in some situations
  * `isUnread` will have an incorrect value in some situations
  * `highestSortNumber` is sometimes incorrect when adding a user to an existing group
* In the `Organization` model:
  * `organization.conversations` will sometimes be sorted incorrectly, due to the conversation having an incorrect `highestSortNumber` value.
  * Messages sent to a broadcast list may cause an invalid `lastMessage` object to be provided in any 1-on-1 conversations with users who are in that broadcast list.
  * The `lastMessage` field will sometimes be missing or contain a message which is not the last message in that conversation.
* When a user joins a private group, full conversation history is shown from before the user has joined, but it should only show the messages sent while the user is part of the group.
* Messages sent while the user has Do-Not-Disturb Auto-forwarding enabled:
  * Will not be sent to the auto-forward recipient
  * Will not cause an auto-reply message to be sent back to the original sender of the message
  * May cause an error to be logged to the JS console
* More polyfills are installed than are actually required by the JS SDK; this will be reduced in the next SDK release

## [3.0.1] - 2017-08-15

- Fix some dependencies that are used in the nodejs bundle of this module.
- Improve the speed of running `npm install` on this module.

## [3.0.0] - 2017-08-15

- **Breaking change**: Run `browserUnload` event hooks during window `unload` handler instead of `beforeunload` handler so that UI code can trap `beforeunload` to prompt the user with an alert if it needs to.
- `client.messages.previewMessage()` now preserves the value of `statusesPerRecipient` from the original message.
- A maximum of 50 messages will be ACK'd each second to ensure even load distribution to individual server nodes.
- A maximum of 2 connections will be allowed to send ACK payloads, as an additional precaution to ensure that messages can be sent quickly without tying up too many network connections to the server.
- `client.messages.downloadAttachment()` may now be called more than once to download the same attachment again.

## [2.0.5] - 2017-07-26

- Debounce `client.typingStatus.startTyping()` and `client.typingStatus.stopTyping()` calls to the server with a 2-second window.
- This should address an issue with messages not being sent because the browser is stuck queueing many `startTyping` calls.

## [2.0.4] - 2017-07-19

- Suppress type error when using `undefined` as an argument to `client.typingStatus.startTyping()` and `client.typingStatus.stopTyping()`.

## [2.0.3] - 2017-07-18

- Add `displayName` field to `organization` object. This will transform 'TigerText Customer' into 'Contacts'.
- Document `client.messages.retrySend()` API endpoint for safely re-attempting to send a failed message without duplication.

## [2.0.2] - 2017-07-11

- Work around Selenium on Firefox issue which would cause finding selectors to be blocked.

## [2.0.1] - 2017-06-28

- Fix issue where some failed messages were not able to be retried.
- Decrease the likelihood of messages to fail during peak hours.

## [2.0.0] - 2017-06-16

- **Breaking change**: Rename `groupType` enum value 'ROOM' to 'FORUM'.
  - This applies to the `groupType` property of `client.groups` members.
  - Change the `types` parameter of `client.search.query` to accept 'forum' instead of 'room'.
- Improve reliability of message delivery after the browser has been idle for a long time, and make it less verbose about re-establishing connections on IE 11.
- Change default value for `clearTypingStatusTimeout` config option from 15 seconds to 3 seconds for improved responsiveness.

## [1.0.3] - 2017-06-06

- `forward()`: When used on a high-priority message without an explicit priority in the function parameters, make the forwarded message high-priority.

## [1.0.2] - 2017-06-02

- Fix issue where user information like `emails` was not being populated by default.
- Move the github repo from tigerconnect/js-sdk-release to tigertext/js-sdk-release.

## [1.0.0] - 2017-05-31

- Improve EventSource compatibility with IE 11 so that downloading messages works faster in that browser.
- Add `resend()` function to support resending a message.
- Add `previewMessage()` and `endPreviewMessage()` to support creating a temporary copy of a message for preview purposes.
- signIn: Take new optional parameter `refreshUser`. If false, do not immediately attempt another AJAX call to refresh the user info.

## [0.2.1] - 2017-04-14

- Fix issue with conversations that have more than 200 messages.

## [0.2.0] - 2017-04-14

- Improve support for downloading messages on Safari by preferring to use native EventSource rather than a polyfill.
- Greatly reduce latency when downloading messages after login.
- Improve support for automatic retry of failed messages.
- Allow failed unsent messages to be recalled, which deletes them out of the conversation locally.
- New `conversations.unreadPriorityCount` variable tracks the number of unread priority messages.

## [0.1.19] - 2016-10-31

- Internal fixes.

## [0.1.18] - 2016-10-04

### Added

- `messages.findAttachment(messageId, attachmentID)` - loads extrea attachment information such as name.

## [0.1.17] - 2016-09-19

- Internal fixes.

## [0.1.16] - 2016-09-08

- Internal fixes.

## [0.1.15] - 2016-08-04

### Added

- Download attachments on the browser with `client.messages.downloadAttachment`

## [0.1.14] - 2016-08-01

### Added

- OAuth authentication config

### Fixed

- `messages:offline:stop` is now called only after all offline conversations fired `conversation:change`
- `conversation:change` is fired properly after a message status is changed


## [0.1.13] - 2016-07-25

### Fixed

- Fixed `messages.downloadAttachmentUrl` for local attachments


## [0.1.12] - 2016-07-06

- Support for IE10


## [0.1.11] - 2016-06-20

- Stability and bug fixes


## [0.1.10] - 2016-05-31

- Group avatars


## [0.1.8] - 2016-04-14

### Added
- Message priority
- Message ttl/delete on read
- Message forwarding
- Conversation muting
- Presence events

### Fixed
- Turn off typing notification when sending a message


## [0.1.7] - 2016-03-25
### Added
- Typing status methods and events
- Group membership change notifications in a group conversation (who was added/removed a group conversation)


## [0.1.6] - 2016-03-17
### Added
- Attachments for messages
- Message Metadata


## [0.1.4] - 2016-03-10
### Fixed
- Fixed an issue where current user object didn't have all the fields after login.
