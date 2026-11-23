# Setup and data contract

This page has the details behind the three steps in the [README](../README.md).

## Requirements

- A current release of Power BI Desktop.
- The extracted Cowork export zip, in one folder (local or SharePoint). The usage file can use either the legacy `cowork*` name or the current `PersonCoworkActivityMetrics*` name.
- Optional: a `people*` file, including the current `PeopleMetaData*` export name, if you want organizational attributes. The report can refresh without this file.
- Optional: **identification**, only if you want to drill into individual users. The report works fully with anonymized IDs. See [Identification in Copilot Analytics](https://learn.microsoft.com/en-us/viva/insights/identification-copilot-analytics).

## How files are picked

- The `DataFolder` parameter takes a folder path or SharePoint folder URL. Paste it **without quotes**; stray quotes are trimmed anyway.
- In that folder, the report loads the newest usage file whose name starts with `cowork` or `PersonCoworkActivityMetrics`. If present, it also loads the newest file whose name starts with `people`, including `PeopleMetaData`. Matching ignores case.
- Supported formats are `.csv`, `.xlsx`, `.xlsm`, and `.xls`. For Excel files, the report reads the first sheet.
- Trailing text in file names is fine, for example `Cowork_Weekly_Oct16_2026_1900Hours.csv`.

## Usage file: weekly activity

The usage file is required. Its name can start with `cowork` or `PersonCoworkActivityMetrics`.

This is the Consumption Dashboard export from **Explore Cowork** > **Export data** > **Export by week**. Each row is one person, week, task category, and task type.

| Column | Type | Notes |
|---|---|---|
| `PersonId` | Text | Stable person identifier. Anonymized unless identification is on. |
| `MetricDate` | Date | Week start |
| `TaskCategory` | Text | For example `Analysis & research` or `Email workflows` |
| `TaskType` | Text | `UserInitiated`, `Scheduled`, or `Unknown` |
| `Total credits used` | Decimal | Not negative |
| `Total Cowork tasks` | Whole number | Not negative |
| `PeopleHistoricalId` | Text | The key used to join to the people file |

## Optional `people*` file: organizational attributes

One row per `PeopleHistoricalId`. If the file is absent, the report retains every usage ID and displays `Unknown` for organization and function. `IsCopilotLicensed` remains blank.

| Column | Type | Notes |
|---|---|---|
| `PeopleHistoricalId` | Text | Required when a people file is supplied; unique and not blank |
| `Organization` | Text | Optional; used for the Organization view |
| `Function` or `FunctionType` | Text | Optional; `Function` is preferred when both exist |
| `IsCopilotLicensed` | Boolean | Optional; `true`, `false`, or blank |

Blank organization or function values show as `Unknown`. Tasks with no value assumption, such as `Unclassified`, are left out of value modeling.

## Validation checklist

- After refresh, check that the total users, tasks, and credits on Overview match your export.
- Clear all slicers before you reconcile totals.
- If you supplied a people file, confirm that most users map to a function. A large `Unknown` group can mean the `PeopleHistoricalId` values don't match between the two files.

## Publish and refresh

- After you publish, set credentials for the data source.
- A local folder needs an on-premises data gateway for scheduled refresh. A SharePoint folder doesn't.
- To update the report, drop a newer `cowork*` or `PersonCoworkActivityMetrics*` file into the folder and refresh. Add a newer `people*` file only when organizational attributes are needed. The newest matching files are used automatically.

## Troubleshooting

| Symptom | Fix |
|---|---|
| "No file starting with 'cowork'" | Check the folder path and confirm it contains a `cowork*` or `PersonCoworkActivityMetrics*` usage file. Remove any quotes from the path. |
| People file reports a missing column | A supplied people file must include `PeopleHistoricalId`. All other people attributes are optional. |
| Most users fall under `Unknown` | The `PeopleHistoricalId` values in the two files don't match. |
| Privacy or firewall prompt | Set the same privacy level for both sources. |
| Blank visuals | Clear slicers, and check that the column headers match the tables above. |
| Refresh fails in the Power BI service | Use a SharePoint folder, or configure a gateway for a local folder. |
