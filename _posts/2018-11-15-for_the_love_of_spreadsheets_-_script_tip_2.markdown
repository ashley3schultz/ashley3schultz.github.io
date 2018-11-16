---
layout: post
title:      "For the love of spreadsheets - Script Tip #2"
date:       2018-11-16 02:50:51 +0000
permalink:  for_the_love_of_spreadsheets_-_script_tip_2
---

## Import Drive file data

### The Use
This little tip is one of my favorites because I can just drop something into a Google drive folder and the scrip will grab the file name, id and description and put that data into my sheet. 

*sneak preview, I use this in conjunction with Script Tip #3 and it saves me countless hours.*

You specify the folder you want to see the files in so if I wanted to list a bunch of images with their links I can simply add them to my corresponding folder and the script pull that data in. You can run this script manually or automatically by setting the project trigger for the script. Project triggers are such a handy little feature in Google script. Plus if your code fails to run successfully for any reason, you will receive an email telling you just that. 

This code snippet clears the previous data before importing the new data. If I have 100 rows in my sheet and the new data only has 60 rows, the last 40 rows would not be over written, this is why I clear the code first and then begin importing. I'm not worried about losing the data because I always want it to reflect the current files in the folder. 

Without dragging this out any longer...

### The Script

```
function lister(){
  var ss = SpreadsheetApp.getActive();
  var sheet = ss.getSheetByName('_PUT_SPEADSHEET_NAME_HERE_');
  sheet.getRange('A2:C100').activate();
  sheet.getActiveRangeList().clear({contentsOnly: true, skipFilteredRows: true});
  var folder = DriveApp.getFolderById('_PUT_FOLDER_ID_HERE_');
  var list = [];
  list.push(['Name','ID','Details']);
  var files = folder.getFiles();
  while (files.hasNext()){
    file = files.next();
    var row = []
    row.push(file.getName(),file.getId(), file.getDescription())
    list.push(row);
  }
  sheet.getRange(1,1,list.length,list[0].length).setValues(list);
}
```

More to come and This one in conjunction with the next tip is like a perfectly brewed cup of tea...

