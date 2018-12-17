---
layout: post
title:      "Pairing Conundrums"
date:       2018-12-17 05:25:38 +0000
permalink:  pairing_conundrums
---


At those wrapping up moment where you application is working beautifully and then suddenly you relize there is another thing to program. 

I got the application working so that when pairing two people up it first checks a few things: 

1. They can't be paired with themself1
```
member_a != member_b
```

2. Niether member is paired with someone else
```
!spoken_for.include?(member_a)
!spoken_for.include?(member_b)
```

3. They haven't been paired before
```
!member_a.pairs.include?(member_b)
```

This generates pairs down the list but what happens if the last two members (to be paired) ave already been paired before? 

Process one. I radomized the list until the last two members in the array haven't been previouslly paired... But if one of them is spoken for during the itteration then that doesn't work either. 

Pocess two. Keep rematching everyong until you get everyone match. While this could eventually work, there is no telling how long that could take so not a good solution. 

Process three. 
If a pair cannot be found in the remaining unpaired members. Go back and ittererate of the the generated pairs until you find one where the one member is a good match for them and the other member is a good match for another remaining unpaired member. This gets trick but I have broken it down into steps. 

I don't save the new pairs (joins table) to the database until we have sucessfully match everyone. 
This wat we aren't updated constantly. 

Try to find a pair

If a pair isn't found, opperation steal a pair takes place and give the remainder to the last one. 

If the remainder pair cannot be matched another opperation steal will take place. 

And possibly after a few tries, we should have all members paired and everyone should be paired with someone they haven't been paired with before. 

Tada!!
