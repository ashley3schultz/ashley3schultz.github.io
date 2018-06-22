---
layout: post
title:      "Having Tea With Sinatra!"
date:       2018-06-21 17:16:15 -0400
permalink:  having_tea_with_sinatra
---


Ok, so maybe not Sinatra himself... however building a tea app with the Sinatra framework was also quite enjoyable. I enjoyed many cups of tea in the process and even a kombucha. So a good time was had by all.

As I approached this project, I thought about an app where a user can sign up for an account and post teas they have either tried or just one not already listed. There are a lot more teas than you realize so I have to create space for growth and always adding more teas. I once tried to log every tea I could find, I ended up with over 100 teas and according to google there are approximately 1500 tea varieties. The more I learn about tea, the more I realize how much I don't know about tea. I feel the same about coding which is why I love them both so much. Both tea and code is a lifelong evolution that I am super excited to discover.
So anyway... (Realizing my little rabbit trail) you can see why it is necessary to always add more teas. 

Any user can comment or post a review about any tea. The application tracks all the teas you have reviewed and when another user reviews the same tea, they will be added to your connections. or assoTEAtes. ok, bad joke...

I started out by creating some tables. One for teas, one for users, and another one for posts
A user can create new teas and only that user can edit or delete that tea
Similarly with a post, only the user who created the post can edit or delete it.
The user can create teas and posts then friend connections are made based on your posting history.
In the event that a tea is deleted, all the posts associated with that tea will also be deleted. I still may change the tea settings so that they cannot be deleted once created, only updated. But for now, I'll keep it this way. 

After the tables were created, I wrote some actions paths, they started out very simple and I used shotgun to run the application to make sure it was working properly

I started on some simple forms to get those working, all the way keeping my actions very simple.
Once I had my forms working, I started to build logic into my action paths and added some security measures and helper measures. 

**Helpers**
1. logged_in? -- Verifies weather the user is logged in or not.
2. current_user -- determines who the current user is.
3. valid_owner? -- Verifies the one editing is the valid owner which also checks logged in status.

**Securities**
1. Password encryption -- I used bcrypt to create a secure password for users.
2. Logged_in checks -- checks in every action path.
3. Before editing -- I coded a check to verify user is the valid owner of that item.
4. Pages not-visability if logged in -- if you are logged in, you can't see the login or signup pages.
5. Pages visable if not logged in -- if you are not logged in you can only see the login or signup pages. 

**Common senses** (ok not a real term but we all know what these are)
1. When signing up, the program checks if the user name or email exists in the system, if it does, you'll be redirected.
2. When creating an a new tea, it checks to see if it exists. If it does, it we'll just take you to that page.
3. I also created a tea class method to sort teas by type (oxidation)
That last one brought an error because I had named the column type. You can't use "type" as a column name as it interferes with activerecord, so I ended up renaming that column “oxidation” which is more accurate anyway. 

So now that all the logic, helpers and security checks are in place, I then did a little formatting to make the page more intuitive. I didn't go crazy with the design; I'll probably go back at some point. I just added a background picture and enlarged some texts and forms for a little easier navigation.

I also created a menu at the top because... 'Common sense'


Lastly I added some error messages in the event someone fills in the form incorectly.

The app name is Tea Connoisseur
I considered alternate names, while they were clever and punny I thought it may make it a little too wonky. 
Still may make another with that fun theme. You'll just have to wait to find out what that is.

















