---
layout: post
title:      "Sending SMS through Google Sheets"
date:       2019-01-10 23:21:14 +0000
permalink:  sending_sms_through_google_sheets
---


I have written Script Tips before, but I'm going to cover the entire process of automated something this week so you get a feel for the entire scope and everything working together.

I love automating... just the thought of spending a little time once and never again... It thrills me to know my program minions are out there working for me day and night. 

The Challenge:
I have a group of volunteers that rotate shifts for events. I need to schedule them, then remind them via email and text 10 days prior to mark their calendars and then remind them again the Wednesday prior to the event.

**1. Create a predictable reoccurring schedule**
I started with regulating the schedule into a predictable pattern. This way they can predict the future and know far in advance if they need to trade a shift and so on.  
This is simple enough as I have 9 volunteer so they rotate every other month and take a certain Sunday (ie: Odd Months - 1st Sunday) the 9th person takes all 5th Sundays. 

**2. But people forget... so I need a calendar everyone can access.**
Google Calendar to the rescue. I love using google calendar for groups because most people have it and understand how it works. Plus it's friendly for Apple and Android users. 

**3. Set up a communication hub**
I implemented slack about a year ago for the entire event group so this was an obvious solution. I just created a new Slack channel where they can communicate and trade shifts. 

**4. Create and format a google sheet**

1. Create a google sheet called "Vol" and add all contact details.
Name | Email | Phone

2. Add a second sheet called "Cal" and add columns 
Event | Date | Name | Email | Subject | Body | Phone | SMS

I customized my email body and email subject with a concatenation formula to great them by name and customize the message/sms based on how far out their shift is (ie: "Mark your calendar..." or "Your shift is this week..."). I also created different bodies for emails and text as I wanted to style the email body with HTML and the text with shorter content.

Up to this point is really been organization child's play so we'll get to the google-goodies.

**4. Write scripts to fetch events from the google calendar**
This will fetch data from the calendar and pull it into the first two columns of the "Cal" sheet in our Google Sheet

```
function fetchEvents(){
 
 const calendar = '__YOUR_CALENDAR_ID__' + '@group.calendar.google.com'
 const sheetId = '__YOUR_GOOGLE_SHEET_ID__'
 const sheetName = 'Cal'
 
 var today = new Date()
 var endDate = new Date(today.getFullYear(), today.getMonth(), today.getDate() + 14)
  
  var ss = SpreadsheetApp.openById( sheetId ),
      sheet = ss.getSheetByName( sheetName ),
      cals = [calendar], c, cal, calName,
      start = today,
      end = endDate,
      events, i, details,
      eventslog = [], e,
      rows = [], range;

  for (c = 0; c < cals.length; c += 1) {
    cal = CalendarApp.getCalendarById(cals[c]);
    calName = cal.getTitle();
    events = cal.getEvents(start, end);
    
    eventslog = eventslog.concat(
      events.map(function(event) {
        return {
          time: new Date(event.getStartTime()).getTime(),
          details: [
            event.getTitle(),
            event.getStartTime()
          ]
        };
      })
    );
  }

  eventslog.sort(function(a, b) { return a.time - b.time; });
  rows = eventslog.map(function(entry) { return entry.details; });
  range = sheet.getRange(2,1, rows.length, 2);
  range.setValues(rows);
	
  sendMessages()
  
  sheet.getRange('A2:B50').activate();
  sheet.getActiveRangeList().clear({contentsOnly: true, skipFilteredRows: true});
}
```

**5. Write scripts to send messages**
If you notice that little line close to the bottom of the last script, we are calling another function `sendMessages()` which will be the part that iterates over each line and sends emails and text messages.

```
function sendMessages() {   
  
  var ss = SpreadsheetApp.getActive()
  var sheet = ss.getSheetByName('Cal')
  var startRow = 2
  var endRow = 4 // DETERMINE HOW MANY ROWS TO PROCESS
  var dataRange = sheet.getRange(startRow, 4, endRow, 8) 
  var data = dataRange.getValues()
  
  for (var i = 0; i < data.length; ++i) {   
    var row = data[i]
    var emailAddress = row[0]
	  var subject = row[1]
    var message = row[2]
    var phone = row[3]
    var smsMsg = row[4]
    
    if (emailAddress){
      sendEmail(emailAddress, subject, message)
    }
    
    if (phone){
      sendSms(phone, smsMsg)
    }
  }
}
```

**5. Write scripts to send emails**
As you can see above we separated the sending emails and sending texts into other functions and enclosed them in an “if statement” so that if no email was provided, it will just skip that part. 
We simply called `sendEmail(emailAddress, subject, message)` passing in three arguments required to send the email. 

```
function sendEmail(emailAddress, subject, message) {
  MailApp.sendEmail({
    to: emailAddress,
    subject: subject,
    htmlBody: message,
    name: '__SENDER_NAME_HERE__'
  })
}
```

**5. Figure out how to send SMS via Google Sheet**
So this one is more difficult but there are add-ons and software out there that already do this and some even provide APIs to works with. I went with Twilio which charges money for each text you send but it's only $0.075 (not even a penny per text) and for the amount that I am going to use this, it will cost me not even $5 per year which is well worth it. Since they have an API they are super programmer friendly and they even have code for you to start with. You could also send email to text using the email function but since people change their carriers periodically it seems like more maintenance than I want. 
Similarly to the email function we called, we call `sendSms(phone, smsMsg)` passing in two arguments which are the phone number and the message body.

```
function sendSms(to, body) {
  
  var accountSid = '__YOUR_TWILIO_SID__'
  var authToken = '__YOUR TWILIO_TOKEN__'
  var twilioNumber = '__YOUR TWILIO_PHONE_NUMBER__'
  
  var messages_url = "https://api.twilio.com/2010-04-01/Accounts/" + accountSid + "/Messages.json"
  
  var payload = {
    "To": to,
    "Body" : body,
    "From" : twilioNumber
  }

  var options = {
    "method" : "post",
    "payload" : payload
  }

  options.headers = { 
    "Authorization" : "Basic " + Utilities.base64Encode(accountSid + ":" + authToken)
  }

  UrlFetchApp.fetch(messages_url, options)
}
```

**5. Set a trigger to run your script**
Finally, after testing the script using my own phone number and emails, I set up the trigger to run automatically on a weekly to meet my needs. 

Go forth my minion, do my bidding...
