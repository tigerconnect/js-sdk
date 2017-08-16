# Change Log

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
