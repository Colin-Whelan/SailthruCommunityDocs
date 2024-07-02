
# AB Test API Call
This is a backup of the AB Test API Call documentation. The original has been removed from the official Sailthru documentation.

## AB Testing Campaigns in Sailthru

Sailthru’s UI (`my.sailthru.com`) uses a combination of the `/blast` endpoint - used for one-off campaign creation/modification - as well as the `/abtest` endpoint. As the workflow below illustrates, the `/blast` endpoint is primarily used to handle the creation/modification of standard campaign attributes (`subject`, `content_html`) whereas the `/abtest` endpoint is the administration tool to create/assign test variants and details.

### Client Workflow

1. **Client creates optional template** that will eventually get used for AB testing. HTML content can be manually passed at a later time if "build from template" is not desired.
2. **Client sends a POST call to ABTest** with the `abtest_type` parameter set to either "split" or "final" and the `copy_template` parameter set to the template name. This creates all defaults for the blast type A and B or A, B, and Final, and returns the associated `blast_ids` for A and B and the `abtest_id`. Alternatively, the client could POST without the `copy_template` parameter set to create blank blasts.
3. **Client sends a POST call to ABTest** with the `abtest_id` set to add another blast component (i.e., C, D, E).
4. **Client can modify blast parameters** (i.e., `subject`, `content`, `from_email`) via the Blast API call and abtest parameters (i.e., `abtest_percent`) via the ABTest API call.
5. **Client can schedule all blasts** (except finals of non-auto winner test) via the ABTest API call using the `status` parameter.
6. **Client can create and schedule the final blast** of a non-auto winner final with the Blast API call with `copy_blast` set to the winning segment, the `status` set to scheduled, and the `scheduled_time` set to a valid time.

## ABTest API Call

### POST (Create)

If no `copy_template` is passed in, the blasts are created blank. If no `status` is passed in, the status is set to draft. As with blasts, abtest blasts are not validated in draft mode.


#### Required Parameters

| Field         | Description                                   | Validation                       |
|---------------|-----------------------------------------------|----------------------------------|
| `abtest_type` | The type of ab test to create (`'split'` or `'final'`) |                                  |
| `abtest_fields` | The fields being varied in the abtest (must be an array of valid blast fields, i.e., `[content_html, subject]`) |                                  |

#### Optional Parameters

| Field             | Description                                   | Validation                       | Notes                                |
|-------------------|-----------------------------------------------|----------------------------------|--------------------------------------|
| `abtest_percent`  | New percentages for all the abtest components | all test components must have a percent `{10 < x < 90}` and all test percentages must sum to 100 | example: test with A/B/Final components only `{"A" : 10, "B" : 15, "Final" : 75}` |
| `schedule_time`   | The schedule time of all abtest components    | Must be a valid future time and prior to final blast if final exists | Unless status is set to scheduled, blasts with schedule_times will be drafts |
| `copy_template`   | The template name that will be used as the master version of the ab test blasts | string and valid template name | |
| `status`          | Schedules or unschedules all abtest blasts    | `draft` or `scheduled`           | This is used to schedule final blast |
| `list`            | The audience of the test                      | string and valid list name       | |
| `suppress_list`   | The suppressed audience of the test           | string and valid list name       | Can be a comma separated string of lists |
| `name`            | The campaign name                             | string (no real validation yet)  | The segment letter will not be appended to the end |
| `abtest_winner_metric` | The metric to determine which segment of the ab test should be sent to the final leg | Must be a valid metric type `{click, open}` | |

#### Returns

```json
{
  "abtest_id": "id",
  "abtest_type": "<type>",
  "abtest_fields": ["fields"],
  "copy_template": "<template name>",
  "list": "<list name>",
  "suppress_list": "<list names>",
  "blasts": [
    {
      "blast_id": "id",
      "abtest_segment": "A",
      "abtest_percent": 0.5,
      "schedule_time": "time",
      "status": "draft"
    },
    {
      "blast_id": "id",
      "abtest_segment": "B",
      "abtest_percent": 0.5,
      "schedule_time": "time",
      "status": "draft"
    }
  ]
}
```

### POST (Modify)

Can only modify abtest parameters of a blast with this method. All information about all blasts in the AB test must be passed in. Any additional test components that need to be created can be done at this time. The new abtest blast component will be created as a copy of blast component A. However, an abtest cannot have more than 5 blast components.

If the `abtest_fields` parameter is modified and fields are removed from the list, all blasts will have that field reset to the blast A value.

Example: add a component C to a test with A, B, and Final already created. All undeclared parameters are copied from blast A to blast C.
```json
{
  "abtest_id": "_id",
  "abtest_percent": {
    "A": 10,
    "B": 10,
    "C": 10,
    "Final": 70
  }
}
```


#### Required Parameters

| Parameter   | Description                                   |
|-------------|-----------------------------------------------|
| `abtest_id` | The id of the abtest collection of blasts.    |

#### Optional Parameters

| Parameter               | Description                                                                                          |
|-------------------------|------------------------------------------------------------------------------------------------------|
| `abtest_fields`         | New list of abtest_fields being tested.                                                              |
| `abtest_percent`        | New percentages for all the abtest components.                                                       |
| `schedule_time`         | The schedule time of all abtest components.                                                          |
| `copy_template`         | The template name that will be used as the master version of the ab test blasts.                     |
| `status`                | Schedules or unschedules all abtest blasts.                                                          |
| `list`                  | The audience of the test.                                                                            |
| `suppress_list`         | The suppressed audience of the test.                                                                 |
| `name`                  | The campaign name.                                                                                   |
| `abtest_winner_metric`  | The metric to determine which segment of the ab test should be sent to the final leg.                |


```json
{
  "abtest_id": "id",
  "abtest_type": "<type>",
  "abtest_fields": ["fields"],
  "copy_template": "<template name>",
  "list": "<list name>",
  "suppress_list": "<list names>",
  "blasts": [
    {
      "blast_id": "id",
      "abtest_segment": "A",
      "abtest_percent": 0.5,
      "schedule_time": "time",
      "status": "draft"
    },
    {
      "blast_id": "id",
      "abtest_segment": "B",
      "abtest_percent": 0.5,
      "schedule_time": "time",
      "status": "draft"
    }
  ]
}
```

### GET

If the `abtest_id` parameter is set, all blasts associated with that abtest will be returned.

#### Required Parameters

-   **`abtest_id`**: The id of the abtest collection of blasts that the new component will belong to.

#### Returns

Similar to the POST return structure, with the blasts associated with the specified `abtest_id`.

### DELETE

If the `abtest_id` parameter is set, all blasts in that abtest will be deleted. If there are sending/sent members of the abtest, do not allow delete.

#### Required Parameters

-   **`abtest_id`**: The abtest blasts to delete.

#### Notes

-   Deleting a single blast is only done through a blast API call.
-   Use "Blast" call to delete a single blast.

## Blast API Call Details to Note

### POST

All fields that are not abtest-specific will only be allowed to be modified if they are in the `abtest_fields` array of fields to be abtested.

A final segment of a non-autowinner ab test can be scheduled only if all other segments are sending or sent.

### GET

Returns abtest parameters if they are set on the blast.

### DELETE

Can only delete a non-sent member of an abtest or a final member that has no unsent members. For a split test, the percentage will be redistributed evenly amongst other member tests. For a final test, the percentage will be added to the final member.

When a member test is deleted, the segment names will need to be updated as well so that a test has continuous segment names.

