\# Automated Invoice Payment Reminder and Tracking System — P-0



\## 1. Objective



Develop an end-to-end automated invoice management workflow integrating Google Sheets and Gmail. The system must identify pending invoices, send reminders based on invoice type, monitor payment confirmations, maintain transaction IDs, and send a second reminder after 24 hours.



\*\*Google Sheets URL:\*\*

https://docs.google.com/spreadsheets/d/1S9JN3F1sPFXBO_2BXQvPJv_Nlb7zlJLMZ0Hvy83yqns/edit?gid=0#gid=0



\*\*Spreadsheet name:\*\* P-0



\## 2. Schedule and Initialization



\- Run the primary workflow every day at 9:00 AM.

\- Connect to Google Drive, Google Sheets, and Gmail using authorized accounts.

\- Open the spreadsheet named \*\*P-0\*\* and retrieve all invoice records.

\- Execute the first reminder, transaction ID management, payment confirmation monitoring, and second reminder processes according to the conditions defined below.

\- Use the configured timezone consistently for scheduling and logging.



\## 3. Required Spreadsheet Columns



Read the following fields:



\- Sr

\- Invoice ID

\- Type

\- Name

\- Amount

\- Account No

\- Bank Name

\- Status

\- Transaction ID

\- Reminder Count

\- Escalated

\- First Reminder Timestamp



If the timestamp column does not exist, use a reliable email log to track the first reminder's sending time. Do not modify existing spreadsheet formatting.



\## 4. V0 — First Payment Reminder



\### Invoice filtering and recipient routing



Process only rows where `Status = Pending`.



\- If Type is `Maintenance`, send the reminder to the configured A1 email address.

\- If Type is `Project`, send the reminder to the configured A2 email address.

\- Skip unsupported types, missing recipients, and all non-pending invoices.



Send emails only to explicitly authorized recipients.



\### Email template



\*\*Subject:\*\* Payment Reminder – Invoice \[Invoice ID] Due



\*\*Body:\*\*



Dear \[Name],



This is a friendly reminder that payment for Invoice ID \*\*\[Invoice ID]\*\*, amounting to \*\*₹\[Amount]\*\*, is currently pending.



Please find the payment details below:



\- Invoice ID: \[Invoice ID]

\- Invoice Type: \[Type]

\- Pending Amount: ₹\[Amount]

\- Account Number: \[Account No]

\- Bank Name: \[Bank Name]

\- Transaction ID: \[Transaction ID, if available]



Kindly complete the payment at the earliest to avoid any inconvenience. If you have already completed the payment, please reply to this email with your payment confirmation.



Thank you for your cooperation.



Regards,  

Payments Team



\### After successful sending



1\. Set Reminder Count to `1`.

2\. Record the first-reminder timestamp.

3\. Log the Invoice ID, Name, Amount, recipient, and timestamp.

4\. Preserve all unrelated cell values and formatting.



Do not update the reminder count if the email fails to send.



\## 5. V1 — Payment Confirmation and Status Update



Monitor the connected Gmail inbox for replies to reminder emails.



1\. Match the reply to the relevant invoice using the email thread, Invoice ID, and available reference details.

2\. Verify that the reply comes from an authorized accountant or payment-confirmation source.

3\. Determine whether the reply clearly confirms successful payment.

4\. If payment completion is confirmed and the invoice Status is `Pending`, update the corresponding Status cell in \*\*P-0\*\* to `Completed`.

5\. Log the Invoice ID, confirmation timestamp, and update result.



If the reply is ambiguous or does not confirm successful payment, leave the Status unchanged.



Modify only the required Status cell. Do not change its formatting or any unrelated spreadsheet data.



Run reply monitoring independently or frequently enough to process incoming confirmations between the daily reminder runs.



\## 6. V2 — Transaction ID Management



Maintain verified transaction references for the invoices in \*\*P-0\*\*.



1\. Locate the invoice using Invoice ID.

2\. Check whether a Transaction ID is available from an authorized and reliable source.

3\. If a valid ID is available, update the corresponding Transaction ID cell.

4\. If no verified ID exists, leave the field unchanged or blank.

5\. Never generate a random ID or reuse another invoice's transaction reference.

6\. Do not overwrite an existing valid ID unless a verified correction is available.



Update only the Transaction ID cell, preserve formatting, and log the operation.



\## 7. V3 — Second Reminder After 24 Hours



Run daily at 9:00 AM after the first-reminder and payment-confirmation checks.



\### Eligibility conditions



Send a second reminder only when all the following conditions are satisfied:



\- Status = `Pending`

\- Reminder Count = `1`

\- Transaction ID is not empty

\- Escalated is blank or `No`

\- At least 24 hours have elapsed since the first reminder was successfully sent

\- The recipient is explicitly authorized

\- A second reminder has not already been successfully sent



Skip every invoice that fails any condition.



\### Second-reminder email



\*\*Subject:\*\* SECOND REMINDER – Payment Pending – Invoice \[Invoice ID]



\*\*Format:\*\* HTML, with the reminder heading, overdue notice, and escalation warning highlighted in red.



\*\*Email body:\*\*



\*\*SECOND PAYMENT REMINDER\*\*



Dear \[Name],



This is a second reminder regarding your pending payment.



Our records indicate that payment for the following invoice remains pending despite our previous reminder.



\*\*Invoice Details\*\*



\- Invoice ID: \[Invoice ID]

\- Amount Due: ₹\[Amount]

\- Transaction ID: \[Transaction ID]

\- Payment Type: \[Type]



\*\*Bank Details\*\*



\- Account Number: \[Account No]

\- Bank Name: \[Bank Name]



\*\*Your payment has remained pending for more than 24 hours after our first reminder.\*\*



Kindly complete the payment immediately.



\*\*Failure to complete the payment without delay may result in escalation to the concerned authority.\*\*



If payment has already been completed, please reply to this email with your payment confirmation.



Regards,  

Finance Team



\### After successful sending



1\. Update Reminder Count from `1` to `2`.

2\. Record the second-reminder timestamp.

3\. Log Invoice ID, Name, Amount, recipient, and timestamp.

4\. Modify only the designated reminder-count and timestamp cells, preserving existing formatting and unrelated values.



If sending fails, retain the existing reminder count and record the error. Never send the second reminder again once Reminder Count is `2`.



\## 8. Combined Decision Logic



For each invoice in \*\*P-0\*\*, execute the following sequence:



1\. \*\*Check Status:\*\* If the invoice is not Pending, skip reminder sending.

2\. \*\*Send the first reminder:\*\* If Pending and not previously reminded, route Maintenance invoices to A1 and Project invoices to A2.

3\. \*\*Record the first reminder:\*\* After successful sending, set Reminder Count to `1` and record the sending timestamp.

4\. \*\*Maintain transaction data:\*\* Store a Transaction ID only when verified information is available.

5\. \*\*Process replies:\*\* When an authorized reply clearly confirms payment completion, update Status to `Completed`.

6\. \*\*Evaluate the second reminder:\*\* If the invoice remains Pending, Reminder Count is `1`, Transaction ID exists, Escalated is blank or No, and 24 hours have elapsed, send the red-highlighted second reminder.

7\. \*\*Update the counter:\*\* After successful sending, set Reminder Count to `2`.

8\. \*\*Skip ineligible invoices:\*\* Do not send reminders to completed, paid, unsupported, or otherwise ineligible records.



\## 9. Logging and Error Handling



Maintain an audit log containing:



\- Invoice ID

\- Name

\- Amount

\- Recipient

\- Reminder stage

\- Email timestamp

\- Transaction ID update details

\- Payment confirmation timestamp

\- Previous and updated Status

\- Previous and updated Reminder Count

\- Success or failure result

\- Error details, when applicable



Process each invoice independently, prevent duplicate emails during retries, and reconcile spreadsheet updates if an email is sent but the corresponding sheet update fails.



\## 10. Spreadsheet Integrity Requirements



All spreadsheet updates must target the correct invoice row using Invoice ID.



\- Status updates must modify only the Status cell.

\- Transaction ID updates must modify only the Transaction ID cell.

\- Reminder Count updates must modify only the Reminder Count cell.

\- Timestamp updates must modify only the designated timestamp cell.

\- Preserve existing formatting, formulas, and all unrelated data.

\- Never overwrite unrelated invoice rows.



\## 11. Expected Outcome



The \*\*P-0\*\* workflow should automate first reminders, payment confirmation tracking, transaction ID maintenance, and second reminders after 24 hours. It must route emails according to invoice type, prevent duplicate reminders, update only the intended spreadsheet cells, and maintain a complete audit trail of email activity and payment status changes.



Use authorized Google Sheets and Gmail connections, and never fabricate invoice details, recipient addresses, transaction IDs, or payment confirmations.

