# Change Log

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
