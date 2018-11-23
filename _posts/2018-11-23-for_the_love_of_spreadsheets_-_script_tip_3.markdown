---
layout: post
title:      "For the love of spreadsheets - Script Tip #3"
date:       2018-11-23 18:37:56 +0000
permalink:  for_the_love_of_spreadsheets_-_script_tip_3
---

## Send Automated Emails from sheet

### The Use
First off, the use for this one it quite obvious as there are endless possibilities to sending lists of emails. I have used this bit of script countless times and I actually have it running multiple times a day, saving me countless hours. 
As promised, I mentioned that I use the last script tip (pulling drive data into a sheet) and this one together which creates some powerful automation. 
I have automated multiple WordPress blogs for companies and organizations using these two scripts in combination. 
First, I put images or files into a folder. I strategically name my files based on what I want the title of the blog should be and then the script grabs the file data, and send an email to with the attached file to the website using a WordPress plugin called Postie. For this instance I have columns listed in this order to correlate with the script, however, it can be customized to be whatever you would like it to be. Use the drive file data as you please in the body of the mail. You could use this by itself without using the drive data as well. If you update the sheet columns, you will need to update the variables in the script to match, just remember to start counting from 0.

0: File Name	
1: File ID	
2: File Description
3: Email
4: Subject	
5: Date
6: Message

Using Postie, you can add categories to your post as well. That information can be found in the Postie Documentation.

### The Script

```
function emailer() {  
  var ss = SpreadsheetApp.getActive(); 
  var sheet = ss.getSheetByName('_PUT_SPEADSHEET_NAME_HERE_'); 
  var startRow = 2;  // First row
  var endRow = sheet.getRange(1,7).getValue(); // Number of entries
  var dataRange = sheet.getRange(startRow, 1, endRow, 5) // Fetch values for each row in the Range.
  var data = dataRange.getValues();
  for (var i = 0; i < data.length; ++i) {
	
    var row = data[i];
		var file = DriveApp.getFileById(row[1])
    var description = row[2];
    var emailAddress = row[3];
		var subject= row[4];
		var postdate = row[5];
    var message = row[6] + '\n Sincerily, \n' + "_PUT_MY_NAME_HERE_";
   
    MailApp.sendEmail(emailAddress, subject, message, {
    attachments: [file],
    name: '_PUT_MY_NAME_HERE_'
  });
    file.setTrashed(true)
 }
  sheet.getRange('F2:F100').activate();
  sheet.getActiveRangeList().clear({contentsOnly: true, skipFilteredRows: true});
}
```
