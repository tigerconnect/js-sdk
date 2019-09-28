# Change Log

## [7.3.0] - 2019-09-30

### Overview

- Extensive SDK documentation is now available at <https://tigerconnect.github.io/js-sdk/>, which includes Quickstart guides for Android SDK, iOS SDK, and JS SDK, along with further reference material for the JS SDK.
- It is recommended to review this documentation, since many JS SDK calls have changed since the last SDK release. Many of these changes are a result of the items listed in the "Performance Improvements" section below.
- The release repository has been moved to <https://github.com/tigerconnect/js-sdk>.
- Support for the following features has been implemented but not yet fully documented. Please contact your TigerConnect support representative for further information and pricing plans for these features.
  - Roles and Role Tags
  - Role Escalations
  - EHR Connect
  - Offline Notifications - see "Offline Notifications" section below

### Performance Improvements

- The JS SDK will no longer download every message when executing `events.connect()`, which will signficantly improve application load performance. Please read <https://tigerconnect.github.io/js-sdk/quickstart/js/getting-inbox> for further details.
- Message expiration is now implemented more performantly, and will no longer cause lag to user input (such as typing messages) in IE 11.
- Many other performance improvements have been implemented throughout the SDK based on feedback from customers who utilize the TigerConnect Messenger website at <https://login.tigerconnect.com/app/messenger/index.html>.

### General Changes

- **Breaking change**: It is now required to pass `partnerName` as a config setting during `new TigerConnect.Client()` calls. Please contact your representative to determine the correct value to use for your organization.
- **Breaking change**: The `.off()` method has been removed from anything which has an `.on()` method; use `.removeListener()` instead of `.off()`
- The SDK will now automatically sign out when the browser window is closed.
- `client.messages.send()` and `client.messages.forward()` will now use firstName instead of displayName of initial group members when creating new groups without a predefined group name.
- The `includeMetadata` option has been removed from `client.search.query()`. Metadata will now always be returned.
- The `client.user.find()` command has been modified to accept an optional `organizationId` parameter. It is recommended to always supply that parameter.

### Offline Notifications

- The `client.notifications` API implements offline notifications, and is intended to be used as an optional feature while the user is logged out due to inactivity.
- While connected, this API emits events containing anonymized message content and unread counts which are suitable for displaying in operating system notifications or browser notifications.
- This API is used internally by the TigerConnect Desktop App.

### Known Issues

The following issues are present in this SDK release and are planned to be fixed in an upcoming SDK or Platform release:

* `conversations.fetchTimeline()`:
  * The `markAsDelivered` parameter does not yet work as the documentation describes, and will be `false` by default instead of `true`.
* `conversations.selectConversation()`:
  * The `markAsDelivered` parameter does not yet work as the documentation describes, and will effectively always be `false`.
  * The `minItemsToFetch` parameter does not yet work as the documentation describes, and will effectively always be `20`.
* In the `Conversation` model:
  * `firstUnreadMessage` will have an incorrect value in some situations
  * `isUnread` will have an incorrect value in some situations
  * `highestSortNumber` is sometimes incorrect when adding a user to an existing group
* In the `Organization` model:
  * `organization.conversations` will sometimes be sorted incorrectly, due to the conversation having an incorrect `highestSortNumber` value.
  * Messages sent to a broadcast list may cause an invalid `last_message` object to be provided in any 1-on-1 conversations with users who are in that broadcast list.
  * The `last_message` field will sometimes be missing or contain a message which is not the last message in that conversation.
* When a user joins a private group, full conversation history is shown from before the user has joined, but it should only show the messages sent while the user is part of the group.
* Messages sent while the user has Do-Not-Disturb Auto-forwarding enabled:
  * Will not be sent to the auto-forward recipient
  * Will not cause an auto-reply message to be sent back to the original sender of the message
  * May cause an error to be logged to the JS console

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
