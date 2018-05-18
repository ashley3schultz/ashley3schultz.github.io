---
layout: post
title:      "Tea Relationship"
date:       2018-05-18 21:04:30 +0000
permalink:  tea_relationship
---


The great this about object oriented Ruby is that we can create objects. further more we can create ojbect relationship which reflect very realistic examples.  A person (object or instance) can own other objects like tea. 

let's code a Person class the we can create instances of individuals. Let's give them a name what well instantiate the new person. We will also give each person we create an empty coupboard to fill with tea. 

Then we are aslo going to create a couple classes for teas. When we add teas to our cuboard it will instantiate a new tea that belongs to the Black tea class and will also belong to the Person class via their cupboard. 

I have three files in here fore the sake of space and columns. One for the Person class and a Green class and a Black class. However, realistilly we would need a totle of 6 different classes for the teas. (White, Yellow, Green, Oolong, Black, & Puerh) 

Code is below with notes. 

<table style="width: 100%;">
<tbody>
<tr>
<th>tea.rb</th>
<th>black.rb</th>
<th>green.rb</th>
</tr>
<tr>
<td>
<pre>
class Person

**#this allows us to read the name of the person**  
  attr_reader :name


**#we will store all created persons in this array** 
  @@all = []

**#when we initialize a person they will be given a name, an empty cupboard & stored in the @@all array**
  def initialize(name)
    @name = name
    @cupboard = {:black => [], :green => [], :oolong => [], :puerh => [], :white => [], :yellow => []}
		@@all << self
  end 
  
**#class method this allows us to see our all array**
 def self.all  
    @@all
  end 
  
**#intance method so we can see into the cupboard**
	def cupboard
    @cupboard
  end
	
**#this is where we add black teas to our cupboard. This initializes a new black tea**
  def add_black_tea(name, steep_temp = nil)
    tea = Black.new(name, steep_temp)
    cupboard[:black] << tea
  end
  
 

end
</pre>
</td>
<td>
<pre>
class Black
  attr_reader :name
  attr_accessor :country, :province, :info, :steep_temp

@@all = []
  
**when a person adds a tea, a default temp is assigned if it was not called with one"**
  def initialize(name, steep_temp)
    @name = name 
    if steep_temp != nil
      @steep_temp = steep_temp 
    else 
      @steep_temp = 210
    end
    @@all << self
  end
  
  def self.all 
    @@all
  end 
</pre>
</td>
<td>
<pre>
class Green
  attr_reader :name
  attr_accessor :country, :province, :info, :steep_temp

@@all = []
  
**when a person adds a tea, a default temp is assigned if it was not called with one**
  def initialize(name, steep_temp)
    @name = name 
    if steep_temp != nil
      @steep_temp = steep_temp 
    else 
      @steep_temp = 170
    end
    @@all << self
  end
  
  def self.all 
    @@all
  end 
</pre>
</td>
</tr>
</tbody>
</table>

> Let's create some object for our new classes.

**a = Person.new("Ashley")**
<p>=> #< Person:0x0000000279f7f0 @name="Ashley", @cupboard={:black=>[], :green=>[], :oolong=>[], :puerh=>[], :white=>[], :yellow=>[]}></p>
 
>  There's a person with an emty cupboard with the name of "Ashley"
 
 
**a.add_black_tea("Buland Black", 195)**
<p> => [#< Black:0x0000000263a108 @name="Buland Black", @steep_temp=195>]</p>
 
 > We now have a new black tea the belongs to the Black class
 
 
**a.add_black_tea("Ruby Black")**
<p> => [#< Black:0x00000002181620 @name="Buland Black", @steep_temp=195>, #<Black:0x000000020293b8 @name="Ruby Black", @steep_temp=210>]</p>

>  Now we created a new black tea and added it to our cuboud.
 
 
**Black.all**
<p>  => [#< Black:0x00000002181620 @name="Buland Black", @steep_temp=195>, #<Black:0x000000020293b8 @name="Ruby Black", @steep_temp=210>]</p>
 
 > This shows all all of the black Teas
 
 
**a.add_green_tea("Gunpowder")**
<p>  => [#< Green:0x00000001b820a8 @name="Gunpowder", @steep_temp=170>]</p>
 
 > Now we created a Green tea
 
 
**Green.all**
<p>  => [#< Green:0x00000001b820a8 @name="Gunpowder", @steep_temp=170>]  </p>
  
> This shows all all of the Green Teas


**a.cupboard**
<p>  => {:black=>[#< Black:0x00000002181620 @name="Buland Black", @steep_temp=195>, #< Black:0x000000020293b8 @name="Ruby Black", @steep_temp=210>], :green=>[#< Green:0x00000001b820a8 @name="Gunpowder", @steep_temp=170>], :oolong=>[],:puerh=>[], :white=>[], :yellow=>[]}

> this shows me all the teas that are in my cupboard. 
 
 
**Person.all**
<p> [=> #<Person:0x00000001c10380 @name="Ashley", @cupboard={:black=>[#<Black:0x00000001bf7e48 @name="Buland Black", @steep_temp=195>, #<Black:0x00000001be46b8 @name="RubyBlack", @steep_temp=210>], :green=>[#<Green:0x00000001b820a8 @name="Gunpowder", @steep_temp=170], :oolong=>[], :puerh=>[], :white=>[], :yellow=>[]}>]</p>

> Shows all persons created

