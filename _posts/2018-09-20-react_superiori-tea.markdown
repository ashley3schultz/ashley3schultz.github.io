---
layout: post
title:      "React Superiori-TEA"
date:       2018-09-20 15:11:45 -0400
permalink:  react_superiori-tea
---


When I thought about this react project, I really wanted to do a game which is something really different from what my other projects were. I thought I'd stick to my theme of coding to a tea which actually proved to be slightly harder to come up with game ideas for strictly a tea related theme. 

**Superiori-tea**

this is a tea harvesting game which you can pick leaves from the tea trees and collect them into your basket. there are five levels, one for each oxidation of tea (black, green, oolong, white and yellow) and on each level careful attention needs to be given to pick the right cultivar (tree variety) as well as collecting the right leaves such as the buds, young leaves and so on, you can also earn bonus point for collecting all the best leaves with no extra and your bonus will be based on the remaining seconds in the game. 

![](https://drive.google.com/open?id=FILEID)

One of the most time consuming aspects what how to align the leaf images. I need them to layout in order and the is a left side and a right side so getting them to align correctly was a bit of a fun puzzle. Secondly, I need the leaves to collect into my state when clicked and then disappear.

I started out with just a few state attributes, (basket, time remaining and level) however the further I pushed into this project I realized there were so many more things that I needed in state. I ended up with 10 including level, score, level scores, basket, time, playing status, games, message.

Along with all those state attributes came a lot of actions and reducers to handle state change. 

When I started this project, I began with just a simple react project, when I got that working, I implemented redux, then implemented my api connection, My containers are connected to the stores and I mainly have one major container 'Game Container' that handle the game play, I used my extra routes to view games and to view my games. 

Lastly I wanted to show certain items only under value of state. If you haven't entered your name, you will not be allowed to play until you do so, this prevents games being saved with no name attribute, I implemented a similar condition when viewing my scores since it will search games based on your name. 

In the game component, I prevented some components like level, score and tea trees to not be seen unless the game playing status is equal to true. This keeps the game clear of unnecessary data at each state of the game. 

I created several components who had child components, who had child component...

![](https://drive.google.com/open?id=1lck8uaF6kHz0LDSlB0ypoQ05InlP-qNe)

Finally I added styling, I actually added some tree styling in the beginning to have better testing ability, but I finished off the final styling near the end as well as cleaning up the code a bit. The game plays out through  the levels and saves to the api when completed and. I made it very simple in that I gave exact directions on how to play which also show live scoring with each leaf click. so I any given point in the level you can see the purity of your basket,  the quality of the basket and what percentage you have collected of the best leaves. I will most likely add a difficulty level which will eliminate tips and some score views to make it a little more difficult. Overall the game is what I imagined it would be. It also has a ton of potential to expand with levels and variety of challenges. 

[Rails API]([ https://github.com/ashley3schultz/superiori-tea-api)
[React App]( https://github.com/ashley3schultz/superiori-tea)



