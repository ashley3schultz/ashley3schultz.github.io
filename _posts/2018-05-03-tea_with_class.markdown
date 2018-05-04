---
layout: post
title:      "Tea With Class"
date:       2018-05-04 03:58:46 +0000
permalink:  tea_with_class
---


<h3>A little about Classes and a little about Tea...</h3>
A <strong>Class</strong>is like a blueprint. We can create and shape object based on how we write the class. A class can have objects or "instances" that belong to the class. Once we write the class we can create objects.
<p style="padding-left: 25px;"><strong>Object:</strong> such as "green_tea" could have data of its own</p>
<p style="padding-left: 50px;"><strong>Data:</strong> @name, @type, @steep_temp</p>
<p style="padding-left: 75px;">Each piece of data will need a reader and a writer to operate outside of the method it is defined in. </p>

<p style="padding-left: 75px;">Writer:</p>
<p style="padding-left: 100px;"><strong> def name = (name)</strong></p>
<p style="padding-left: 125px;"><strong>     @name = name</strong></p>
<p style="padding-left: 100px;"><strong>end</strong></p>
<p style="padding-left: 75px;">Reader:</p>
<p style="padding-left: 100px;"><strong>def name</strong></p>
<p style="padding-left: 125px;"><strong>     @name</strong></p>
<p style="padding-left: 100px;"><strong>end</strong></p>
<p style="padding-left: 125px;">or</p>
<p style="padding-left: 75px;">Writer Macro:</p>
<p style="padding-left: 100px;"><strong>attr_writer :name </strong></p>
<p style="padding-left: 75px;">Reader Macro:</p>
<p style="padding-left: 100px;"><strong>attr_reader :name </strong></p>
<p style="padding-left: 75px;">Reader & Writer Macro:</p>
<p style="padding-left: 100px;"><strong>attr_accessor :name </strong></p>
<p style="padding-left: 25px;">Methods can be created within the class to add functionality or behaviors.</p>
<p style="padding-left: 50px;">Class methods operates on the class</p>
<p style="padding-left: 75px;"><strong>Class Method:</strong> def self.new_method</p>
<p style="padding-left: 75px;">"self" refers to the class "Tea" that's how ruby knows that this is a class method
<strong>self.new_method</strong> is the same as <strong>Tea.new_method</strong></p>
<p style="padding-left: 100px;">Class methods are used on the class itself.  We could look up all teas in our cupboard or search all the teas for a particular tea. 
<p style="padding-left: 50px;">Instance method operates on the instance.</p>
<p style="padding-left: 75px;"><strong>Instance Method:</strong> def steep_time
<p style="padding-left: 75px;">In an instance method "self" would refer to whatever was passed into the method. like green_tea.steep_temp in this case self would be equal to green_tea. Or calling the method with an argument like steep_temp(green_tea).</p>
<p style="padding-left: 100px;"> This method could take the return the correct steep_time for the tea passed into the method.</p>

Let's write the class for our Tea

<hr />

<span style="color: #000080;"><strong>class Tea</strong></span>

<span style="color: #000080;"><strong>end</strong></span>

<hr />

Yep, that's it, we can create objects now by typing

<span style="color: #800080;"><strong>Tea.new</strong></span>

However, we haven't created a place to store the tea. It will create a new object every time but we need to store it. let's add an array to store the tea. We want to store the tea in the class itself so we will use @@. in short @ is for the instance <span style="color: #333333;">and @@ is for the class. We want all teas to be stored in the class.</span>

<hr />

<span style="color: #000080;"><strong>class Tea</strong></span>

<span style="color: #333333;"><span style="color: #000080;"><strong>@@cupboard = []</strong></span> <span style="color: #808080;">#this sets the cupboard to an empty array, as we add teas to the cupboard the array will change</span></span>
<p style="padding-left: 30px;"></p>
<span style="color: #000080;"><strong>end</strong></span>

<hr />

<span style="color: #333333;">Now we need to collect the teas as they are created. we can do this by writing an initialize method. We'll also use attr_reader so we can initialize the tea with a name like "Dragonwell" or "Assam" and a tea type like "green" or "black".</span>

<hr />

<span style="color: #000080;"><strong>class Tea</strong></span>

<span style="color: #000080;"><strong>attr_reader :name, :type </strong></span><span style="color: #808080;">#using reader because we are "writing" this code in the initialize method</span>

<span style="color: #333333;"><span style="color: #000080;"><strong>@@cupboard = []</strong></span> <span style="color: #808080;">#this sets the cupboard to an empty array, as we add teas to the cupboard the array will change</span></span>
<p style="padding-left: 30px;"><span style="color: #333333;"><span style="color: #000080;"><strong>def initialize(name, type)</strong></span> <span style="color: #808080;">#we are adding two arguments <strong>name</strong> and <strong>type</strong> to this because we want to state name the tea and state what type of tea it is when we create it. </span></span></p>
<p style="padding-left: 60px;"><span style="color: #333333;"><span style="color: #000080;"><strong>@type = type </strong></span><span style="color: #808080;">#this "labels" the tea with the <strong>type </strong></span></span></p>
<p style="padding-left: 60px;"><span style="color: #333333;"><span style="color: #000080;"><strong>@name = name</strong></span><span style="color: #808080;"> #this "labels" the tea with the <strong>name</strong></span></span></p>
<p style="padding-left: 60px;"><span style="color: #333333;"><span style="color: #000080;"><strong>@@cupboard &lt;&lt; self </strong></span><span style="color: #808080;">#this pushes the new object to the cupboard array</span></span></p>
<p style="padding-left: 30px;"><span style="color: #000080;"><strong>end</strong></span></p>
<span style="color: #000080;"><strong>end</strong></span>

<hr />

Now every time we create a tea it will get stored in the cupboard.

<span style="color: #800080;"><strong>Tea.new("Dragonwell", "Green")</strong></span>

<span style="color: #808080;"> #=> [# < Tea: 0x00000027a5f38 @type="Green", @name="Dragonwell" > ]</span>

<span style="color: #800080;"><strong>Tea.new("Assam", "Black")</strong></span>

<span style="color: #808080;"> #=> [# < Tea: 0x00000027ae58 @type="Black", @name="Assam" > ]</span>

What if we want to see all the teas in our cupboard. We will need to write a class method to show us all the teas (objects) that are in the cupboard (array). Let's do that.

<hr />

<span style="color: #000080;"><strong>class Tea</strong></span>

<span style="color: #000080;"><strong>attr_reader :name, :type </strong></span><span style="color: #808080;">#using reader because we are writing this code in the initialize method</span>

<span style="color: #333333;"><span style="color: #000080;"><strong>@@cupboard = []</strong></span> <span style="color: #808080;">#this sets the cupboard to an empty array, as we add teas to the cupboard the array will change</span></span>
<p style="padding-left: 30px;"><span style="color: #333333;"><span style="color: #000080;"><strong>def initialize(name, type)</strong></span> <span style="color: #808080;">#we are adding two arguments <strong>name</strong> and <strong>type</strong> to this because we want to name the tea and designate the tea type when we create it. </span></span></p>
<p style="padding-left: 60px;"><span style="color: #333333;"><span style="color: #000080;"><strong>@type = type </strong></span><span style="color: #808080;">#this "labels" the tea with the <strong>type </strong></span></span></p>
<p style="padding-left: 60px;"><span style="color: #333333;"><span style="color: #000080;"><strong>@name = name</strong></span><span style="color: #808080;"> #this "labels" the tea with the <strong>name</strong></span></span></p>
<p style="padding-left: 60px;"><span style="color: #333333;"><span style="color: #000080;"><strong>@@cupboard &lt;&lt; self </strong></span><span style="color: #808080;">#this pushes the new object to the cupboard array</span></span></p>
<p style="padding-left: 30px;"><span style="color: #000080;"><strong>end</strong></span></p>
<p style="padding-left: 30px;"><span style="color: #333333;"><span style="color: #000080;"><strong>def self.cupboard </strong></span></p>

<p style="padding-left: 60px;"><span style="color: #000080;"><strong>@@cupboard </strong></span><span style="color: #808080;">#this will show the cupboard array</span></p>
<p style="padding-left: 30px;"><span style="color: #000080;"><strong>end</strong></p>
<span style="color: #000080;"><strong>end</strong></span>

<hr />

<p Now we can see all the teas in the cupboard</p>

<p <span style="color: #800080;"><strong>Tea.cupboard</strong></span></p>

<span style="color: #808080;"> #=> [# < Tea: 0x00000027a5f38 @type="Green", @name="Dragonwell" > , # < Tea: 0x00000027ae58 @type="Black", @name="Assam" > ]</span>

Let's add one more method for setting a steep temperature based on the tea type. This will be a instance method. we need to add a attr_accessor to the class for steep_temp. In the case of the name and the type of tea, that will always remain the same after we initialize it. However sometimes certain teas have a different steeping temperature outside of the norm so we'll need to be able to write this on occasion.

<hr />

<span style="color: #000080;"><strong>class Tea</strong></span>

<span style="color: #000080;"><strong>attr_reader :name, :type </strong></span><span style="color: #808080;">#using reader because we are writing this code in the initialize method</span></p>
<span style="color: #000080;"><strong>attr_accessor :steep_temp </strong></span>

<span style="color: #333333;"><span style="color: #000080;"><strong>@@cupboard = []</strong></span> <span style="color: #808080;">#this sets the cupboard to an empty array, as we add teas to the cupboard the array will change</span></span>
<p style="padding-left: 30px;"><span style="color: #333333;"><span style="color: #000080;"><strong>def initialize(name, type)</strong></span> <span style="color: #808080;">#we are adding two arguments <strong>name</strong> and <strong>type</strong> to this because we want to name the tea and designate the tea type when we create it. </span></span></p>
<p style="padding-left: 60px;"><span style="color: #333333;"><span style="color: #000080;"><strong>@type = type </strong></span><span style="color: #808080;">#this "labels" the tea with the <strong>type </strong></span></span></p>
<p style="padding-left: 60px;"><span style="color: #333333;"><span style="color: #000080;"><strong>@name = name</strong></span><span style="color: #808080;"> #this "labels" the tea with the <strong>name</strong></span></span></p>

<p style="padding-left: 60px;"><span style="color: #333333;"><span style="color: #000080;"><strong>@steep_temp = set_steep_temp(type)</strong></span><span style="color: #808080;"> #this will call the instance method and assign a temperature to the tea</span></span></p>

<p style="padding-left: 60px;"><span style="color: #333333;"><span style="color: #000080;"><strong>@@cupboard &lt;&lt; self </strong></span><span style="color: #808080;">#this pushes the new object to the cupboard array</span></span></p>
<p style="padding-left: 30px;"><span style="color: #000080;"><strong>end</strong></span></p>

<p style="padding-left: 30px;"><span style="color: #333333;"><span style="color: #000080;"><strong>def self.cupboard </strong></span></p>
<p style="padding-left: 60px;"><span style="color: #000080;"><strong>@@cupboard </strong></span><span style="color: #808080;">#this will show the cupboard array</span></p>
<p style="padding-left: 30px;"><span style="color: #000080;"><strong>end</strong></p>

<p style="padding-left: 30px;"><span style="color: #333333;"><span style="color: #000080;"><strong>def set_steep_temp(tea)</strong></span></p>
<span style="color: #000080;"><strong>
<p style="padding-left: 50px;">a = tea
<p style="padding-left: 50px;">answer = case a
<p style="padding-left: 100px;">when "White"
<p style="padding-left: 150px;">185
<p style="padding-left: 100px;">when "Green"
<p style="padding-left: 150px;">175
<p style="padding-left: 100px;">when "Yellow"
<p style="padding-left: 150px;">185
<p style="padding-left: 100px;">when "Oolong"
<p style="padding-left: 150px;">210
<p style="padding-left: 100px;">when "Black"
<p style="padding-left: 150px;">200
<p style="padding-left: 100px;">when "Puerh"
<p style="padding-left: 150px;">210	
<p style="padding-left: 100px;"><span style="color: #000080;">end</p>
<p style="padding-left: 50px;">answer</p>
<p style="padding-left: 25px;"><span style="color: #000080;">end</p>
<span style="color: #000080;">end</strong></span>

<hr />

<p <span style="color: #800080;"><strong>Tea.cupboard</strong></span></p>

<span style="color: #808080;"> #=> [# < Tea: 0x00000027a5f38 @type="Green", @name="Dragonwell" , @steep_temp=175 > , # < Tea: 0x00000027ae58 @type="Black", @name="Assam", @steep_temp=200 > ]</span>

In conclusion we have a class the stores new teas. Can give them properties such as name and a tea type and automatically provide a steep temperature based on the tea type. </p>

We could add additional methods like a tea timer etc. but we won't get into that today. </p>

This blog was written while enjoying a cup of green tea : )












