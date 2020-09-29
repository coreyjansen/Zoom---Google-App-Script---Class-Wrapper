<!-- ⚠️ This README has been generated from the file(s) "blueprint.md" ⚠️-->Welcome to Zoom Wrapper Libary. This is version 0.1.0!

[![-----------------------------------------------------](https://raw.githubusercontent.com/andreasbm/readme/master/assets/lines/colored.png)](#zoom)

## ➤ Zoom

Zoom Class for Zoom user and Zoom Webinar Class

### Properties

| Property | Default  |
| -------- | -------- |
| `apiKey` | "apiKey" |
| `secret` | "secret" |

### Methods

| Method    | Type                                                                                     | Description    |
| --------- | ---------------------------------------------------------------------------------------- | -------------- |
| `callAPI` | `(url: string, method?: string, payload?: string, muteHttpExceptions?: boolean): string` | Calls Zoom API |

[![-----------------------------------------------------](https://raw.githubusercontent.com/andreasbm/readme/master/assets/lines/colored.png)](#zoomuser)

## ➤ ZoomUser

### Properties

| Property    | Modifiers | Type     | Default     | Description                          |
| ----------- | --------- | -------- | ----------- | ------------------------------------ |
| `apiKey`    |           |          | "apiKey"    |                                      |
| `secret`    |           |          | "secret"    |                                      |
| `details`   | readonly  |          |             | Get details of user if id is defined |
| `email`     |           | `string` | "email"     |                                      |
| `firstName` |           | `string` | "firstName" |                                      |
| `group`     |           | `string` | "group"     |                                      |
| `id`        |           | `string` | "id"        |                                      |
| `lastName`  |           | `string` | "lastName"  |                                      |
| `status`    |           | `string` | "status"    |                                      |
| `type`      |           | `number` | "type"      |                                      |

### Methods

| Method                 | Type                                                                                     | Description                                                            |
| ---------------------- | ---------------------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| `addToGroup`           | `(): any`                                                                                | Add user to a group                                                    |
| `assignWebinarLicense` | `(): any`                                                                                | Used to assign webinar license with 500 capacity to user               |
| `callAPI`              | `(url: string, method?: string, payload?: string, muteHttpExceptions?: boolean): string` | Calls Zoom API                                                         |
| `changePassword`       | `(password: string): string`                                                             | Change password to string provided                                     |
| `createUser`           | `(): object`                                                                             | Create Zoom User                                                       |
| `findUserDetails`      | `(): boolean`                                                                            | Populate the user details as long as either the email or id is defined |
| `getAllUsers`          | `(): any`                                                                                | Get all users returned in user variable                                |
| `updateLicense`        | `(type?: int): any`                                                                      | Change license to the type                                             |

[![-----------------------------------------------------](https://raw.githubusercontent.com/andreasbm/readme/master/assets/lines/colored.png)](#zoomwebinar)

## ➤ ZoomWebinar

Class for the Webinars extends Zoom

### Properties

| Property              | Modifiers | Default  | Description                                                                                                  |
| --------------------- | --------- | -------- | ------------------------------------------------------------------------------------------------------------ |
| `apiKey`              |           | "apiKey" |                                                                                                              |
| `secret`              |           | "secret" |                                                                                                              |
| `currentAttendees`    | readonly  |          | Gets the number of current attendees to a live webinar that are unique.<br />Returns the number of Attendees |
| `currentParticipants` | readonly  |          | Gets the current participants of the webinar                                                                 |
| `details`             | readonly  |          | Returns details for webinar                                                                                  |
| `panelists`           | readonly  |          | Gets list of all panelists                                                                                   |
| `participants`        | readonly  |          | Gets participants of webinar that has already ran                                                            |
| `recording`           | readonly  |          | Get recording and recording password                                                                         |
| `registrants`         | readonly  |          | Gets object with the registrants                                                                             |
| `webinarID`           |           |          |                                                                                                              |

### Methods

| Method                    | Type                                                                                     | Description                                                                                           |
| ------------------------- | ---------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `addPanelist`             | `(advisorEmail: string): string`                                                         | Adds advisor as a panelist. If multiple emails are listed separated by commas split them and add each |
| `callAPI`                 | `(url: string, method?: string, payload?: string, muteHttpExceptions?: boolean): string` | Calls Zoom API                                                                                        |
| `filteredAttendees`       | `(ignoreList: object): number \| false`                                                  | Gets the participants and then removes any that contain a domain found in the ignoreList              |
| `removeRecordingPassword` | `(): any`                                                                                | Remove password from recording                                                                        |
| `updateContactInfo`       | `(contactName: string, contactEmail: string): string`                                    | Updates contact information of Webinar                                                                |
| `updateSurveyURL`         | `(postSurveyURL: string): string`                                                        | Updates the post webinar survey url for the speicifc webinar                                          |




### Example Code

```var webinar = Zoom.createZoomWebinar(zoomApiKey,zoomSecret, zoomID);
zoomRegistrants = webinar.registrants;```
