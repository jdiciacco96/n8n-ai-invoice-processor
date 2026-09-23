```

## How it works

1. **Watch for a new invoice**: The Google Drive Trigger polls a selected folder every minute and starts when a new file is created.
2. **Download and read the file**: The workflow downloads the new file as binary data and extracts text from the PDF.
3. **Extract invoice details**: The Information Extractor uses Google Gemini 2.5 Flash to convert unstructured invoice text into a structured record.
4. **Save the record**: The workflow appends the extracted data to a Google Sheets invoice database.
5. **Draft a notification**: OpenAI GPT-4o mini creates a subject line and email body for the billing team.
6. **Send the notification**: Gmail sends the generated invoice notice to the configured recipient.

## Extracted fields

The AI extraction step is configured to capture the following information from each invoice:

| Field | Description |
| --- | --- |
| Invoice Number | Unique invoice identifier |
| Client Name | Name of the invoiced client |
| Client Email | Client email address |
| Client Address | Client mailing address |
| Client Phone | Client phone number |
| Total Amount | Invoice total |
| Invoice Date | Date the invoice was issued |
| Due Date | Invoice payment due date |

## Tech stack

| Technology | Purpose |
| --- | --- |
| [n8n](https://n8n.io/) | Workflow orchestration |
| Google Drive | Invoice storage and new-file trigger |
| Google Gemini 2.5 Flash | Structured invoice-data extraction |
| Google Sheets | Invoice database |
| OpenAI GPT-4o mini | Billing email generation |
| Gmail | Notification delivery |

## Setup

### 1. Import the workflow

In n8n, select **Import from File** and choose the exported `Invoice Processing.json` workflow file.

### 2. Connect credentials

Create or select credentials for:

- Google Drive
- Google Gemini
- Google Sheets
- OpenAI
- Gmail

### 3. Configure Google Drive

Set the Google Drive Trigger to the folder where invoice PDFs will be uploaded. The Download Binary node should use the ID supplied by the trigger.

### 4. Configure Google Sheets

Select the spreadsheet and worksheet that will serve as the invoice database. Create these header columns before running the workflow:

```text
Invoice Number | Client Name | Client Email | Client Address | Client Phone | Total Amount | Invoice Date | Due Date
```

### 5. Configure the email recipient

In the Gmail node, replace the placeholder recipient with the billing inbox or team email address that should receive invoice notifications.

### 6. Test and activate

Upload a sample invoice PDF to the monitored Google Drive folder. Confirm that a row is added to Google Sheets and that the billing email arrives, then activate the workflow.

## Notes

- The workflow is currently configured for PDF invoices.
- The extraction step requests all eight fields; if a value cannot be found in an invoice, the AI model may omit it. Add validation or a review step before production use if missing fields must be handled.
- Keep API keys, OAuth credentials, Google Drive folder IDs, spreadsheet IDs, and email addresses out of version control.
- Use a dedicated Google Sheet and test inbox while validating the workflow.
