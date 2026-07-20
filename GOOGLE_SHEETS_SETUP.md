# Google Sheets Integration Setup

## Instructions to Connect the Contact Form to Google Sheets

### Step 1: Create a Google Sheet

1. Go to [Google Sheets](https://sheets.google.com) and create a new spreadsheet
2. Name it "Dr. Li Bai - Contact Messages"
3. Add the following headers in Row 1:
   - `Timestamp`
   - `Name`
   - `Email`
   - `Message`

### Step 2: Create a Google Apps Script

1. In your Google Sheet, click on **Extensions** > **Apps Script**
2. Delete any code in the editor and paste the following:

```javascript
function doPost(e) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  
  // Get form data
  const name = e.parameter.name;
  const email = e.parameter.email;
  const message = e.parameter.message;
  const timestamp = new Date();
  
  // Append to sheet
  sheet.appendRow([timestamp, name, email, message]);
  
  // Return JSON response
  return ContentService.createTextOutput(JSON.stringify({'status': 'success', 'count': sheet.getLastRow() - 1}))
    .setMimeType(ContentService.MimeType.JSON);
}

// Handle GET requests
function doGet(e) {
  const action = e.parameter.action;
  
  if (action === 'getCount') {
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    const count = sheet.getLastRow() - 1; // Subtract header row
    return ContentService.createTextOutput(JSON.stringify({'count': count}))
      .setMimeType(ContentService.MimeType.JSON);
  }
  
  return ContentService.createTextOutput(JSON.stringify({'status': 'ready'}))
    .setMimeType(ContentService.MimeType.JSON);
}
```

3. Click the **Save** icon (floppy disk)
4. Rename the project to "Contact Form Handler"

### Step 3: Deploy as Web App

1. Click **Deploy** > **New deployment**
2. Click the gear icon ⚙️ next to "Select type"
3. Choose **Web app**
4. Configure:
   - **Description**: "Contact Form Handler"
   - **Execute as**: "Me"
   - **Who has access**: "Anyone"
5. Click **Deploy**
6. Copy the **Web app URL**

### Step 4: Update the Website

1. Open `index.html` in a text editor
2. Find the JavaScript section with `GOOGLE_SCRIPT_URL`
3. Replace the URL with your copied Web app URL
4. Save the file

### Step 5: Test

1. Open the website in a browser
2. The message counter should display the current count from the sheet
3. Fill out the contact form
4. The counter should update and the data should appear in your Google Sheet

## Features

- **Live Message Counter**: Shows the number of messages received from the Google Sheet
- **Automatic Updates**: Counter refreshes on page load
- **Optimistic UI**: Counter increments immediately after successful submission
- **Local Fallback**: Uses localStorage if Google Sheets is unavailable
