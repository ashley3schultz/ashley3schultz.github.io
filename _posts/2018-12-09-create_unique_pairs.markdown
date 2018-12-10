---
layout: post
title:      " Create Unique Pairs"
date:       2018-12-10 04:36:01 +0000
permalink:  create_unique_pairs
---


This little app idea came from a group i attended. There was a need to pair people up but they have to be paired with some one new every time until everyone has been paired with everyone and then the cycle starts over. This was a simple app but it will remember the data and and pair people automatically and then store that data. 

First off, I used the has_and _belong_to_many setup but using the same model. I wrote about that in my previous blog. 

Now we can define a member and then see its pairs with:
member = Member.find(id) 
member.pairs 

Then we can see all of that members pairs. I added another colunm to my connections table "current" to see who everyone is currently paired with.

Next we have a little code to create unique pairs. 

First we find out if some one has been paired with everyone. In this case the group leader is the first one in the table so we can compare length 

Member.all.first.pairs.size >= Member.where(active: true).size 

If it is equal or higher than that number we clear all previous pairs and start fresh. 

Connection.destroy_all 

Otherwise, we itterate over the members and pair them up. 

we update all "current" attributes to false so when we create new ones we can see just the current pairs. 

We havent decided what happens if there are an odd number or members, there will be either three members in one group or one person will be solo. I'm learning toward the first one but it seams it would be nice to be able to choose I'll add that option. 

The rules for pairing:

1. A member cannot be paired with themselves
2. A member cannot be paired with someone in their pairs
3. A member cannot be paired with someone already paired up.

we iterate over the member and for each member we itterate over the members until we find a match that meets all three requirements. 

Once the match is found we:

1.We create a new connection with current attribute equal to true
2. Add to a member-a and member-b to the spoken_for array 

After everyone has been paired we will show the resaults on the special page where it will show each set of pairs.

Each time you click the button new unique pairs will be made.

You can also edit member to make them active or inactive and even wipe the data for previous pairs in the event that you reach a point of starting over before you have been paired with everyone. 

I later plan to make a side addition where you can create a new list, add any name and pair them off. You will have option to pair two people together or in light of christmas right aroundthe corner, you could pair secret sents style. 
