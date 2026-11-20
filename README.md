# Genesys Cloud CX – Voice Security Firewall and Spam Call Blocking

## Project Overview

This project demonstrates a Genesys Cloud CX inbound voice flow integrated with a reusable Common Module to validate callers and manage spam-caller status using a Genesys Cloud Data Table and a Data Action.

The screenshots document the Architect configuration, caller-input validation, Data Table lookup, API-based row update, and inbound menu routing.

> **Portfolio note:** This repository is a visual project walkthrough based on configuration screenshots. It does not contain a deployable Genesys Cloud flow export or a complete implementation package.

## Objectives

- Look up an inbound caller's number in a Genesys Cloud Data Table.
- Retrieve the caller's spam-related status fields.
- Generate a numeric challenge and collect caller input.
- Compare the caller's input with the generated challenge.
- Limit the validation process to a configured maximum of three attempts.
- Demonstrate a Data Action that updates a Data Table row.
- Reuse the validation logic from an inbound Architect flow.
- Provide inbound menu options that route callers to different ACD queues.

## Solution Components

| Component | Purpose shown in the screenshots |
|---|---|
| Inbound Call Flow | Plays the greeting, normalizes the caller number, stores it as participant data, and invokes the reusable task. |
| Common Module Flow | Contains the caller lookup and challenge/validation logic. |
| Data Table Lookup | Looks up the caller using `customer_number` and returns `spam_caller_id` and `suspect_spam_caller_id`. |
| Architect Data Actions | Calls the configured `2414_UPDATE_DATA_TABLE` action to update a Data Table row. |
| Genesys Cloud Data Table | Stores the customer number and spam-related Boolean fields. |
| Postman | Shows an example `PUT` request to update a Data Table row through the Genesys Cloud Platform API. |

## High-Level Call Flow

1. An inbound caller reaches the Architect inbound flow.
2. The flow plays the configured greeting.
3. The caller number is normalized by removing the `tel:` prefix and is stored for use by the reusable task.
4. The inbound flow invokes the `SPAM_CHECK` reusable task, which calls the Common Module.
5. The Common Module looks up the caller number in the Data Table.
6. If a matching row is found, the flow follows the configured decision logic using the returned spam-related fields.
7. The challenge branch generates a numeric value, prompts the caller, and collects input.
8. The entered value is compared with the generated value. The displayed loop is configured for a maximum of three attempts.
9. The configured success and failure branches play their respective audio and perform the associated actions.
10. The inbound menu provides transfer options to the configured ACD queues.

**Important:** The exact production behavior depends on the full Architect configuration, Data Action contract, queue settings, and published flow versions. The screenshots are illustrative evidence of the configuration, not proof of a live production deployment.

## Data Table Structure

The screenshots show the following columns:

| Column | Type | Description |
|---|---|---|
| `customer_number` | String | Caller number used as the lookup key. |
| `spam_caller_id` | Boolean | Spam caller status. |
| `suspect_spam_caller_id` | Boolean | Suspected spam caller status. |

The Common Module Lookup input is mapped from `Common.customer_number`. The displayed lookup outputs are mapped to task variables for the two Boolean fields.

## API Update Example

The Postman screenshot demonstrates a `PUT` request to the Genesys Cloud Platform API endpoint for updating a Data Table row:

`PUT /api/v2/flows/datatables/{datatableId}/rows/{rowId}`

The example request body uses the row key and the two Boolean fields:

```json
{
  "key": "<CUSTOMER_NUMBER>",
  "spam_caller_id": true,
  "suspect_spam_caller_id": false
}
```

Replace the placeholders with values from your own test environment. Do not commit access tokens, client secrets, customer phone numbers, real customer records, or environment-specific identifiers.

## Repository Contents

- `Screenshots/` – screenshots of the Architect flow, Common Module, Data Table, audio prompt, and Postman request.

## Technologies and Tools

- Genesys Cloud CX
- Genesys Cloud Architect
- Architect Common Modules / reusable tasks
- Genesys Cloud Data Tables
- Genesys Cloud Data Actions
- Genesys Cloud Platform API
- Postman
- JSON

## Screenshots

### Architect and Common Module

1. [Data Table Lookup Configuration](Screenshots/01-Data-Table-Lookup-Configuration.png)
2. [Common Module Update Data](Screenshots/02-Common-Module-Update-Data.png)
3. [OTP Generation and Three-Attempt Loop](Screenshots/03-OTP-Generation-and-Three-Attempt-Loop.png)
4. [OTP Validation and Call Data Action](Screenshots/04-OTP-Validation-and-Call-Data-Action.png)
5. [Common Module Flow Overview](Screenshots/05-Common-Module-Flow-Overview.png)

### Data and API

6. [Data Table Example Row](Screenshots/06-Data-Table-Example-Row.png)
7. [Audio Prompt Configuration](Screenshots/07-Audio-Prompt-Configuration.png)
8. [Postman Data Table Update Request](Screenshots/12-Postman-Data-Table-Update-Request.png)

### Inbound Call Flow

9. [Inbound Flow Starting Task](Screenshots/08-Inbound-Flow-Starting-Task.png)
10. [Menu Option One – Transfer](Screenshots/09-Menu-Option-One-Transfer.png)
11. [Menu Option Two – Transfer](Screenshots/10-Menu-Option-Two-Transfer.png)
12. [Calling the Common Module](Screenshots/11-Calling-the-Common-Module.png)

## Security and Privacy

Before making this repository public:

- Redact customer phone numbers and any personal information from screenshots.
- Remove organisation-specific names, division labels, tenant IDs, Data Table IDs, and other internal identifiers if they are confidential.
- Never upload OAuth access tokens, client secrets, passwords, API keys, or production credentials.
- Use dummy values in API examples.
- Confirm that you are permitted to share the screenshots and configuration details.

## Disclaimer

This is an independently prepared portfolio presentation of Genesys Cloud CX configuration concepts. Any organisation, tenant, queue, customer, or environment names visible in source screenshots should be treated as confidential unless you have permission to disclose them. This repository is not affiliated with or endorsed by Genesys.
