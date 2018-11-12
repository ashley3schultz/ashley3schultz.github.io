---
layout: post
title:      "For the love of spreadsheets - Script Tip #1"
date:       2018-11-12 01:07:33 +0000
permalink:  for_the_love_of_spreadsheets_-_script_tip_1
---

## Import Google calendar events

### The Use
I wanted to create my own app. Why did I want my own custom app? Well because I want to categorize events differently. I wanted to be able to see just my work related event, or just my personal appointments and lastly I wanted a tab that only show my available times instead of the other way around. 

### The Script

```
function importEvents(){
 
 const calendar = '_UT_CALENDAR_ID_HERE_' + '@group.calendar.google.com'
 
 var today = new Date()
 var endDate = new Date(today.getFullYear(), today.getMonth() + 3, today.getDate())
  
  var ss = SpreadsheetApp.openById( '_PUT_SPEADSHEET_ID_HERE_' ),
      sheet = ss.getSheetByName( '_PUT_SPEADSHEET_NAME_HERE_' ),
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
          time: new Date(event.getStartTime()).getTime(), // sort by this
          details: [
            event.getTitle(),
            event.getStartTime(),
            event.getEndTime(),
            event.getLocation(),
            calName
          ]
        };
      })
    );
  }

  eventslog.sort(function(a, b) { return a.time - b.time; });

  rows = eventslog.map(function(entry) { return entry.details; });

  range = sheet.getRange(2,1, rows.length, 5);
  range.setValues(rows);
}
```

This blog was written while drinking "Wild Tonic Blueberry & Basil Kombucha”. 
This is one of my favorite brands in the Kombucha world.

