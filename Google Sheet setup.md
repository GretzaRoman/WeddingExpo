# Send sign-ups to your Google Sheet

The sign-up page can't write to a spreadsheet by itself. You need a small Google
script that sits between them. This takes about 5 minutes, once.

## 1. Add the header row

Open your sheet:
https://docs.google.com/spreadsheets/d/14vmsWCDIEAqVvBait2ZNBt8ObZhOBqjkdN08lNmMW4Q/edit

In row 1, put these six headers, one per column (A through F):

    Timestamp | Name | Address | Phone | Email | Owns or rents

## 2. Create the script

In that same sheet: **Extensions -> Apps Script**.

Delete whatever is in the editor and paste this in:

```javascript
function doPost(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheets()[0];
  var d = JSON.parse(e.postData.contents);
  sheet.appendRow([
    new Date(),
    d.name || '',
    d.address || '',
    d.phone || '',
    d.email || '',
    d.tenure || ''
  ]);
  return ContentService.createTextOutput('ok');
}
```

Click the save icon.

## 3. Publish it

Click **Deploy -> New deployment**.

- Click the gear next to "Select type" and choose **Web app**
- Description: anything, e.g. "Wedding expo signups"
- Execute as: **Me**
- Who has access: **Anyone**  (this matters — without it the page can't post)
- Click **Deploy**

Google will ask you to authorize it. Approve the permissions. If you see a
warning screen, click **Advanced** and then **Go to (project name)**.

When it finishes, copy the **Web app URL**. It looks like:

    https://script.google.com/macros/s/AKfy...long string.../exec

## 4. Paste the URL into the page

Open the sign-up page, open **Tweaks**, and paste that URL into
**sheetEndpoint**. Save it as the default.

That's it. Test it by filling out the form yourself — a new row should appear in
the sheet within a few seconds.

## Notes

- Entries only reach the sheet when someone submits online. If the expo booth
  has no internet, the row won't be recorded.
- If you redeploy the script later, Google gives you a new URL. Paste the new
  one into the tweak.
- The page also keeps the most recent sign-up in the browser so the next-steps
  page can greet them by name. That's separate from the sheet.
