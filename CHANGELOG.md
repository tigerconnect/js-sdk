# Change Log

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
