---
layout: post
title:      "Pairing App Revisited"
date:       2019-02-10 22:53:18 +0000
permalink:  pairing_app_revisited
---


I tend to see patterns everywhere; in data, phone numbers and human behavior. I see it even if the pattern was never intended, I find myself looking for one. So when it comes to programming I tend to keep looking for one until I find it. I have even had applications working that keep scratching my brain because I know there must be a better way to solve it or a pattern inside it. 

I created a pairing app some time ago. It's for a networking group that pairs up members for meet ups and each member will meet with everyone in the group. So if there are 10 members, there will be 9 meet ups before the cycle starts over. There are 9 meet ups because they won't meet with themselves. 

The pairing app has been scratching my brain for some time, I found some patterns that worked within ranges but that weren't scalable and so the scratching continued and I kept revisiting it, trying different patterns until...

I cracked it. There's nothing like the feeling... Closure since you finally figured it out and a little dumbfounded because it was so simple and you can't believe you didn't see it sooner. 

No matter how long it takes, when debugging or problem solving, I'm always hit with how I didn't see it sooner even if it was minutes. 

So here is the long awaited simple pattern for pairing up members. 

---
layout: post
title:      "Pairing App Revisited"
date:       2019-02-10 22:53:18 +0000
permalink:  pairing_app_revisited
---


![Table](https://drive.google.com/file/d/1YSMU-_DY2BB2C2KabXEWgmgNEEr2_AOx/view?usp=drive_web&amp;usp=embed_facebook&source=ctrlq.org)

There should be an even number of members.
The first one is the "Key" and we assign an id to each one starting with 0 and so the key id is equal to 0 and we assign the rest incrementing by 1.
For the first round, we start with id 1 and pair them with the highest id number and each round after we pair them with the previously paired id - 1 and so on. When we reach 1, we start at the highest number again and so it loops like this:  5, 4, 3, 2, 1, 11, 10, 9, 8, 7, 6.
We use the same pattern vertically and horizontally and when each id falls on its own id, they are paired with the key. 
This pattern works with any number of members and so it is fully scalable and doesn’t need to iterate over members to find matches, making this a fast and reliable solution.

This does get a little complicated if members drop out in the middle of the sequence even if they drop out in even numbers as you end up with duplicate meet ups or members without meet ups, but you could customize based on preference. 

Finally that little place in my brain can rest after finding the pattern. Well, at least on this one. 

This blog was written to a cup of Midnight Sun Oolong Tea, a personal favorite from Mei Leaf Teas.

