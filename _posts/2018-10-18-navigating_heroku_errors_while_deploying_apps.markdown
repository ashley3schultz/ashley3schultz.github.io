---
layout: post
title:      "Navigating Heroku Errors While Deploying Apps"
date:       2018-10-18 21:28:34 +0000
permalink:  navigating_heroku_errors_while_deploying_apps
---


Heroku can be a really quick way to deploy your app. In just a few short commands from the terminal you can have a live website and moreover it's free. You can deploy up to five apps with a free account. The app will enter a sleep phase after 30 minutes of being inactive which means they will take longer to load. If that is a concern you can upgrade to a paid account. 

There is a lot of information about how to deploy an app to heroku, However, there isn't as much data for when you run into errors. I ran into a lot of errors while deploying 3 applications to heroku and so I wanted to write about navigating through them however I will include the steps to deploy an app as well. 

Here are a few steps to get your app deployed

**## 1. Convert your rails app to use a postgresql database.**

**gemfile.rb**
```
group :development do 
    gem ‘sqlite3’
end 

group :production do 
    gem ‘pg’
end 
```

*remove ‘sqlite3 ’ if someplace else*

**##2. Create a Procfile - This tells heroku how to start your app.**

**Procfile**
`Web: bundle exec rails server –p $PORT`

**##3. Make sure to commit changes**
`git add .`
`git commit -m "Make db changes for heroku & add procfile"`
`git push`

**##4. Run the following commands in the terminal**
`heroku create yourappname`
`git push heroku master`

**##5. Migrate database on heroku, if you don't have a database then skip this step**
`heroku run rake db:migrate`
`heroku run rake db:seed` if applicable 

**##Finally open the app to see if it is up and running and everything looks good.**
----------------------------------------------------------------------------------

**##ERRORS**
If you app fails to deploy, you'll see this near the bottom of the script.

`Push rejected, failed to compile Ruby app`

This mean some errors occurred during the deploy process. 
Here are some of the errors I encountered and how I fixed them.

First scroll back through the script that just ran to deploy your app and you may see yellow or red text that points to a specific issue. You may also see that your app successfully deployed but it still isn't working.

**##Two lock files Error**
This will keep your app from deploying successfully. If you have two lock file like package.lock and yarn.lock, you will need to delete the package.lock file. Make sure to commit your changes before running: 
`git push heroku master`
`heroku run rake db:migrate`
`heroku run rake db:seed` if applicable 

**##AJAX not working**
if your jquery doesn’t work in your deployed app, go into the following file: 
**app/views/layouts/application.html.erb**
inside your head tags near the top of the file there is a script tag which points to jquery src
`<script src="https://code.jquery.com/jquery-2.1.1.min.js"></script>`
Make sure it is an http**s** url as heroku url is https and your server url is probably http. 

*Note: when you do this, your jquery may not work on your local server if it was a http url. *

 **##API not fetching data**
 If you are deploying a ruby API, you will need to update your urls. 
 My scenario included two deployed app, one being a ruby API and the other being a react app. 
 I changed the react app url so it fetches from my new API. This was in my env file but you may have it someplace else.
 I changed the API approved urls to include my new react app url. If using rackcors then you will find your urls in:
**config/initializers/cors.rb**


**##Navigating additional errors** 
All the data is there and should tell you what happened in the logs and there are two ways to view your logs which should help you figure out the error. Once you identify an error, try googling the error and usually you can find a fix pretty quickly.

**From the command Line: **
`heroku logs`

**From your Heroku account:**
go to https://dashboard.heroku.com/apps
Click on the app 
In the top right corner, click more and a dropdown will appear
click view logs
*Note: I preferred looking at these in my account as they are color coded and easy to spot in lengthy logs.*

Here is a link to a directory of error codes, however, I found this tells you what happened, not how to fix it as there could be multiple problems behind some of these errors. I suggest digging deeper into the logs for something more detailed. 
https://devcenter.heroku.com/articles/error-codes

I found the least amount of information on the heroku site itself, however there is a lot out there so just keep looking, you could try rephrasing the error or googling different parts of the error to expand your google results. 
