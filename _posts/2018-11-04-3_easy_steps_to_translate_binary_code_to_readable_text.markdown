---
layout: post
title:      "3 easy steps to translate binary code to readable text"
date:       2018-11-05 01:11:13 +0000
permalink:  3_easy_steps_to_translate_binary_code_to_readable_text
---


Recently I received a text from my bestie. She's not a nerd like I am but she loves me in spite of that. This is how the text read:

01101001 00100000 01101100 01101111 01110110 01100101 00100000 01111001 01101111 01110101

I absolutely adore that she went through the trouble to send such a text.
So given my new text and not know what it said without looking up how to translate it, I found it quite interesting to find the pattern of how these 1s and 0s are generated. 

## **1. Each set of eight represents one number...**
Starting from the right enter 1, as you move to the next left column, multiply the current column by 2. Then place the set of 8 1s and 0s in the 2nd row. So you’ll end up with something like this. 

128 | 64 | 32 | 16 |  8  |  4  |  2  | 1
  0    |  1   |   1  |  0  |  1  |  0  |  0  | 1

## **2. Add all number where there is a 1**
for instance, in this first set, the first number will will add is 64 because there is a one in that column. So this: 

128 | 64 | 32 | 16 |  8  |  4  |  2  | 1
  0    |  1   |   1  |  0  |  1  |  0  |  0  | 1
	
will be equal to this:

64 + 32 + 8 + 1 = 105

Then do that with all sets of 8.

105
32
108
111
118
101
32
121
111
117

## **3. Translate using the ascii chart.**
There is a character associated with each of these numbers that can be found on the ascii chart. 
To decrypt any alphanumeric lines you just need to know 4 numbers and everything can be counted from there.

32 = “ “ (space)
48 = “0” (zero)
65 = “A” (uppercase A)
97 = “a” (lowercase a)

You can count 0-9, A-Z and a-z from there in increments or 1. Ascii charts are easy to find, but by only knowing those 4 numbers you can get pretty far without any help, which is pretty fun if you ask me. 

So so translated this it my text:

105 = i
32 =  
108 = l
111 = o
118 = v
101 = e
32 =  
121 = y
111 = o
117 = u

I have left something an extra tidbit for your own plessure.

01010100 01101000 01101001 01110011 00100000 01100010 01101100 01101111 01100111 00100000 01110111 01100001 01110011 00100000 01110111 01110010 01101001 01110100 01110100 01100101 01101110 00100000 01110111 01101000 01101001 01101100 01100101 00100000 01100100 01110010 01101001 01101110 01101011 01101001 01101110 01100111 00100000 01111001 01101111 01110101 01101110 01100111 00100000 01100111 01110101 01110011 01101000 01110101 00100000 01110100 01100101 01100001 00100000 00111010 00101001
