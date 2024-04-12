# UI API
Sailthru's API for the visual aspects of the website.

I use this API a fair bit for userscripts, so I'm documenting my use and it's capabilities to aid in development of future tools. These endpoints are available from anywhere within Sailthru, allowing for sharing of data between parts of the system. This also means that custom addons can also share any information between any parts of the system. 

Usually this is used for GET requests to retrieve information, but can likely be used to POST as well. No testing has been done here.

## Endpoints

### `GET https://my.sailthru.com/uiapi/campaign/{campaignId}`

**Description:**  
This endpoint retrieves data for a specific campaign in Sailthru. It is used to fetch details such as seed emails associated with the campaign.

**Requirements:**

-   **Campaign ID:** The unique identifier for the campaign. This is required to fetch the specific campaign data.

**Usage:**  
The endpoint is used to fetch campaign data. The data is fetched using a `fetch` request. The data return can be used to add custom functionality and displays. 

**Response Structure:**
| Field                       | Type          | Description                                                               |
|-----------------------------|---------------|---------------------------------------------------------------------------|
| blast_id                    | string        | The unique identifier for the campaign.                                   |
| name                        | string        | The public name of the campaign.                                          |
| suppress_list               | array         | An array of suppression lists associated with the campaign.               |
| from_name                   | string        | The name displayed as the sender in the email.                            |
| from_email                  | string        | The email address displayed as the sender.                                |
| replyto                     | string        | The email address to which replies are sent.                              |
| subject                     | string        | The subject line of the email.                                            |
| content_html                | string        | The HTML content of the email, typically minified.                        |
| content_text                | string   | The plain text content of the email, if available.                        |
| is_google_analytics         | boolean       | Indicates if Google Analytics tracking is enabled.                        |
| is_link_tracking            | boolean       | Indicates if link tracking is enabled.                                    |
| report_email                | string        | The email address to which campaign reports are sent.                     |
| list                        | string        | The name of the list to which the campaign is sent.                       |
| list_id                     | string        | The unique identifier of the list to which the campaign is sent.          |
| mobile_app_id               | string/null   | The identifier for the mobile app, if applicable.                         |
| schedule_type               | string        | The scheduling type for the campaign (e.g., "specific", "local_timezone", or "personalized"). |
| email_hour_range            | integer       | The range of hours for sending the email, used if the schedule_type is "personalized". |
| local_day                   | date string   | The local date for sending the email, used if the schedule_type is "local_timezone" (YYYY-MM-DD). |
| local_hour                  | string        | The local hour for sending the email, used if the schedule_type is "local_timezone" (00:00). |
| status                      | string        | The status of the campaign (e.g., "draft", "scheduled", "sent").          |
| modify_time                 | string        | The last modification time of the campaign, in GMT ("DDD, DD MMM YYYY HH:MM:SS GMT"). |
| modify_user                 | string        | The email address of the last user to modify the campaign.                |
| is_stateless                | boolean       | Unknown purpose (related to campaign state).                              |
| new_workflow                | boolean       | Unknown purpose.                                                          |
| is_selective_seeding_enabled| boolean       | Unknown purpose (related to seeding options).                             |
| template                    | string        | The name of the template used for the campaign.                           |
| generate_mode               | string        | Unknown purpose (related to content generation).                          |
| mode                        | string        | The mode of the campaign (e.g., "email", potentially "sms" or "push" for other channels). |
| setup                       | string        | Any code from the advanced setup tab.                                     |
| last_step_viewed            | integer       | The last step the user viewed in the editor.                              |
| step                        | integer       | The current step the user is on in the editor.                            |
| timezone                    | string        | The timezone of the logged-in user.                                       |
| real_scheduletime           | string        | The real schedule time of the campaign, in GMT ("DDD, DD MMM YYYY HH:MM:SS GMT"). |

---

### `GET https://my.sailthru.com/uiapi/lifecycle/`

**Description:**  
This endpoint retrieves data about all LOs. 

**Usage:**  
The endpoint is used to fetch template data. The data is fetched using a `fetch` request. The data return can be used to add custom functionality and displays, such as adding better usage data views.

| Field                       | Type          | Description                                                               |
|-----------------------------|---------------|---------------------------------------------------------------------------|
| context | unknown | Unknown |
| finalUrl | string | The url of the direct URL to view the repsonse data. Also available in responseText |
| lengthComputable | boolean | unkown |
| loaded | int | unknown |
| readyState | int | unknown |
| response | array of JSON | The full data of the LOs |
| responseHeaders | string | Normal response headers |
| responseText | array of JSON | As far as I can tell, this is the same as 'response' |
| responseXML | array of JSON | As far as I can tell, this is the same as 'response' |
| status | int | status code like 200, 404, etc. |
| statusText | string | status like "OK", "ERROR" etc. |
| total | int | unknown |

The return values can be automatically filtered to return LO data that are connected to a specific template: `https://my.sailthru.com/uiapi/lifecycle/?template_id=${id}` 

**response  array**  
Here is some more details about the response JSON objects. This shows in reverse order, so the entry point will be last in the list. 

| Field                       | Type          | Description                                                               |
|-----------------------------|---------------|---------------------------------------------------------------------------|
| id | string | The unique ID of the LO - used in the URL |
| name | string | The human readable name |
| release | int | unknown |
| reentry.isAllowed | boolean | Whether users can re-enter the LO |
| reentry.ifPresent | boolean | Whether users are allowed to re-enter while already in it | 
| reentry.afterDelay | string? or *null* | The delay before users can re-enter the LO |
| steps.{id} | JSON | JSON object for each step in the LO | 
| steps.{id}.subtype | string | The step type like "sendEmail", "multiEventVarEq", "customEvent", etc. |
| steps.{id}.taskAttributes | JSON | JSON object additional details like "templateId" for "sendEmail", or the field name for "multiEventVarEq", etc. |
| steps.{id}.children | array | Array of JSON objects of the immediate children aka next steps. | 
| steps.{id}.children[i].id | string | The id of the child step |
| steps.{id}.children[i].nextid | string | The id of any grandchild steps |
| steps.{id}.type | string | The type of step action like "action", "check", "entry" |
| createTime | int | Timestamp of the create time |
| lastEditedTime | int | Timestamp of the create time |
| status | string | The current status of the LO. "active" or "inactive" |
| lastEditedUser | string | The email address of the user to last edit the LO | 

---

### `GET https://my.sailthru.com/uiapi/templates`
Returns a list of all the templates. 

**Full response**  
Assuming no field params are added, here are the details of the full template responses.
| Field                       | Type          | Description                                                               |
|-----------------------------|---------------|---------------------------------------------------------------------------|
| template_id | int | The id of the template | 
| name | string | The human readable name |
| labels | array | An array of labels (strings) |
| tags | array | An array of tags (strings) |
| subject | string | The subject line |
| modify_time | string | The last modified time as relative time if recent("Yesterday, 3:47 pm") or GMT ("DDD, DD MMM YYYY HH:MM:SS GMT") if older |
| mode | string | The type of messaging. "email" + "visual_email" - probably "sms" and "push" as well. | 
| data_feed_url | null |
| is_disabled | boolean | Whether the template is disabled or not. |
| teams | unknown | unknown | 
| is_basic | boolean | unknown |
| sample | unknown | unknown | 
| teams_string | string | unknown |

The return values can be automatically filtered to return only needed fields by adding this url param + fields in comma deliminated list: `https://my.sailthru.com/uiapi/templates?returnFields=name,subject` 

---

### `GET https://my.sailthru.com/uiapi/querybuilder/query/` 
Used in the Audience builder. Not explored much. 

Used in an addon like this: `https://my.sailthru.com/uiapi/querybuilder/query/?term={letter}`
This will return all the fields connected to profiles that start with the selected letter. I used this to create a full list of fields visible in the UI builder so you don't have to know the field names to find it.

**Response**
The response is a simple array of strings which are the fields that match. Fields that are part of JSON will show as the JSON path, eg. "address.street" or "destinationTrackingPreferences.Google Analytics"

---

### `GET https://my.sailthru.com/uiapi/campaigns` 
Returns info about the campaigns

**Full response**  

| Field                       | Type          | Description                                                               |
|-----------------------------|---------------|---------------------------------------------------------------------------|
| items | array | JSON of matches |
| counts.drafts | int | Number of drafts |
| counts.scheduled | int | Number of scheduled |
| counts.sending | int | Number of sending |
| counts.sent | int | Number of sent |
| counts.recurring | int | Number of recurring |
| filtered count | int | unknown |
| warnings[i].error_number | int | Error number |
| warnings[i].error_message | string | Error message | 
| warnings[i].error_file | string | url of the file that errored (points to php page?) |
| warnings[i].error_line | int | line number of the issue |

There are additional params to actually return filtered data:
| Field                       | Type          | Description                                                               |
|-----------------------------|---------------|---------------------------------------------------------------------------|
| search | string | Search term. Default search has poor matching. |
| skip | int | Omits the first {int} number of responses from the array. |
| limit | int | The maximum number of campaigns to return at once. Max = 100? | 
| sort | string | Field to sort by, adding minus (-) to the start for descending, eg. "-modify_time" = most recent modify date. |
| status | string | The status of the send. "drafts", "scheduled", "sending", "sent", or "recurring" |

**Sample full URL:** `https://my.sailthru.com/uiapi/campaigns?search=test&skip=1&limit=100&sort=-modify_time&status=drafts`

---

### POST `https://my.sailthru.com/uiapi/bee/auth`
This is the one instance of using POST to part of the UI API. This is used to authenticate with the BEE Visual Email Builder, which is required when accessing parts of the BEE system such as the image storage.

#### Headers
**Cookie:** document.cookie -> uses the document cookies to authenticate

#### Return data
| Field                       | Type          | Description                                                               |
|-----------------------------|---------------|---------------------------------------------------------------------------|
| data.access_token | (I forget the type) | Required for accessing BEE systems

### GET `https://bee-cloudstorage.getbee.io/cs/cloudstorage/`
After authenticating with BEE, the access token is used to access cloudstorage for this space.
#### Headers
**Authorization:** 'Bearer ' + {accessToken}

#### Return data

| Field                       | Type          | Description                                                               |
|-----------------------------|---------------|---------------------------------------------------------------------------|
| status | string | Status message, eg. "success" |
| data | JSON | Full JSON data of images in cloudstorage |
| data.items | array | Array of folder/image names |
| data.items[i].name | string | Name of the folder/image. Will have file extension if it's an image |
| data.items[i].path | string | The relative path of the folder/image |
| data.items[i].last-modified | string | Timestamp as a string |
| data.items[i].permissions | string | Permissions for the current user, eg. "rw" |
| data.items[i].extra | JSON | Unknown |
| data.items[i].mime-type | string | Item type, eg. "application/directory" or "image/png" | 
| data.items[i].size | int | Size in Bytes | 
| data.items[i].public-url | string | Public URL to use in templates (only shows when item is image) |
| data.items[i].thumbnail | string | URL of low res version of thumbnail previews (only shows when item is image) |
| data.items[i].item-count | string | Count of items in the folder.  (only shows when item is folder) |

---

### GET `https://my.sailthru.com/uiapi/promotions/`
Returns details about the promotions

#### Return data
Returns a JSON array of items.

| Field                       | Type          | Description                                                               |
|-----------------------------|---------------|---------------------------------------------------------------------------|
| items | array | Array of items, one for each promo |
| items[i].id | int | System ID of the promotion | 
| items[i].client_id | int | The current space ID (Number in space selection in top right) | 
| items[i].name | string | Human name of the promotion | 
| items[i].vars | JSON | JSON object for the optional Promo Fields | 
| items[i].vars.offer | string | Offer text - available in Zephyr with promotion().vars.offer | 
| items[i].vars.description | string | Description text - available in Zephyr with promotion().vars.description | 
| items[i].vars.terms_and_conditions | string | Terms and Conditions text - available in Zephyr with promotion().vars.terms_and_conditions | 
| items[i].report_email | string | The email address to send reminders to | 
| items[i].unassigned | int | The number of unused codes | 
| items[i].create_date | int | Timestamp of the promotion creation date | 
| items[i].modify_date | int | Timestamp of the last promotion edit | 
| items[i].create_user | string | Email address of the user who created the promotion |
| items[i].modify_user | string | Email address of the user to last edit of promotion | 
| items[i].refill_reminder | int | Number of promotions remaining before sending refill reminder to `report_email` | 
| items[i].refill_reminder_sent | boolean| Whether 'Refill Reminder' is enabled or not | 

Adding the promotion ID to the request URL  will return the details for just that promotion:

`GET https://my.sailthru.com/uiapi/promotions/{id}`

