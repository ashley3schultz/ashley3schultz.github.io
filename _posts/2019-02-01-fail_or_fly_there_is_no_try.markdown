---
layout: post
title:      "Fail Or Fly There Is No Try"
date:       2019-02-01 16:04:47 -0500
permalink:  fail_or_fly_there_is_no_try
---

I completed a technical challenge yesterday, it's not my first but I still get pre-test anxiety. It's all the mystery on the other side of the "Start Challenge" button and you start questioning everything that you know and wondering about what you don't know that you don't know. Then the questions started flooding in.

1. What if I get stumped...
2. What if I don't come up with the best solution...
3. What if they ask me to hack the Death Star...

Okay, that last one has never happened in a technical challenge that I have done, but it doesn't stop me from wondering every little possible scenario that could happen. 

In light of all the interview jitters I have been thinking about the power of self-talk. I often imagine of my thought process like a function, the function is there but what I input into the function, changes the return value.

```
def self_talk():
    arg =  input("Enter A Question:\n").lower()
    print("YES! {}!".format(arg.replace("am i", "You are")).replace("?",""))
				
self_talk() 
```

Enter A Question:
*Am I good enough?*
**YES! You are good enough!**

Enter A Question:
*Am I worthless?*
**YES! You are worthless!**


As you can predict, whatever you pass into the argument will return "YES!".

> Research is beginning to reveal that positive thinking is about much more than just being happy or displaying an upbeat attitude. Positive thoughts can actually create real value in your life and help you build skills that last much longer than a smile. [read article](https://www.huffpost.com/entry/positive-thinking_b_3512202)

This article explain how positive thoughts open your mind to possibilities and even set you up for success in the future, while negative ones close your mind and limit you from seeing opportunities and possibilities. 

In light of the fact that our positive and negative thoughts shape our near future, I imagine a bank if you will that can go into a positive and negative balance and change our default thought process. 

In light of "There Is No Try", I give you a jedi_training() function.
```
# Added for awesomeness 
from termcolor import cprint                #   python3 -m pip install termcolor
from pyfiglet import figlet_format      #   python3 -m pip install pyfiglet

def jedi_training():
    the_force = 0
    arg = ""
		
    while True:
        arg = input("Enter A Question:\n").lower()
        if arg == 'quit':
            break
						
        trying = " try" in arg
        the_force += -1 if " not" in arg or "less" in arg else 0 if trying else 1
        side = "Dark" if the_force < 0 else "Light"
        msg1 = "You have the power of the {side} side!"
        msg2 = "Do or do not, there is no try!"
        color = "green" if trying else "blue" if side == "Light" else "red"
        cprint(figlet_format(msg2 if trying else msg1), color=color)
				
jedi_training()
```

Enter A Question:
*Am I not good enough to beat Vader*   => set the_force -1
**You have the power of the Dark side**

Enter A Question:
*Is the force with me?*   => set the_force 0
**You have the power of the Light side**

Enter A Question:
*Am I going to hack the death star?*   => set the_force 1
**You have the power of the Light side**

Enter A Question:
*Am I trying hard enough*   => doesn't change the_force
**Do or do not, there is no try!**

All that to say…
 When the pre-test questions arose, I took a different approach before clicking start. I sat at my computer and stared at the button.

"Start Challenge" 

I sat and thought about what this button really represents to me.

1.  The joy I get from programming...
2.  My love for research and problem solving...
3.  The possibilities that are on the other side...

Then I realized that regardless of whether I fail or fly, this is a part of my journey and will be an opportunity no matter what the outcome is. Even if I fail, it's an opportunity to learn more and now know what I didn't know which is a valuable asset. This is just a step in the journey.

Now the button incased my journey and everything I love about it. Now it was inviting me in.

