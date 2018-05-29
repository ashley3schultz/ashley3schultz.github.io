---
layout: post
title:      "tea-cli-app"
date:       2018-05-29 20:27:59 +0000
permalink:  tea-cli-app
---


This is the process of creating a ruby gem that scrapes different types of tea from the web. 

List of requirements 
1. Has a CLI to interact with the gem
2. Greets the user
3. Prints menus and submenus to choose from
4. Get a list of teas based on menu selection
5. Access individual details when index is selected
6. Says goodbye to the user when they exit

**Website selection**

The data I would like is
1. Tea Type
2. Name
3. Description 
4. Steeping instructions
5. Country of origin

I started out by trying to pick a website that has a lot of information about tea. After looking through Teapedia and Wikipedia, the information I found on there was very inconsistent. Some teas had an individual page and some did not. Even the information that was provided was inconsistent and therefore could lend to a less attractive user experience. In a lot of cases I couldn't find the data I was looking for on certain teas. Lastly, including all tea types, teapedia lists approximately 90 teas.
With that information in mind, I looked at one of my favorite tea shops called MeiLeaf out of London who has 120 teas listed on their site and with very consistent data. I finally landed on it due to being able to provide every piece of data on my list plus extra.
MeiLeaf has superior teas and if I could only shop one place, it would be with them. 

**setup**

This was probably the most challenging part of it all. It wasn't just creating a gem which I did using bundle. but also figuring out github and files structure that require each other. 
I watched several youtube videos in the process about github to try to understand what was happening. Needless to say I know how to use github quite well, especially after I accidently deleted my gem file and had to go back to find an earlier version on github using git checkout but once it was setup, now all the fun stuff could begin. 

**Where to begin**

I imagined that I was going to start with building my scraper but after watching some tutorial provided for the project, I decided to start with the CLI. I started by just putting a message in there that would print when the file was called. Then I added another file and put another message in that file. Soon I had my little chain of file that could call each other. I connected all the files with an environment file only requiring that file in the bin folder and the environment file requires all my other files. 
Once I had a chain of files that could call each other and that was working, I started writing a bit of code and then would get that part working again, and so on.
I enjoyed working this way because I could always tell where the code was breaking. 

**bin/teahead**

Session.new.call 
Basically I wanted to have a class called Session that initialized a new session and call a method 'call' within the Session class.
The first step was to define the call method within my Session class. Not that I had these methods to call yet, just one by one I wrote them and got each one working. 
The greeting and goodbye methods simply just print out a message but the middle one is where all the magic happens. I need to stay in the hub until the user exits the program. 

  ```
def call 
    greeting 
	  hub
	  goodbye	 
  end

  def greeting
    puts "   Welcome Teahead!"
    puts "   Please enter your name:"
    @name = gets.strip.capitalize
    puts 
    puts "   Hello #{@name}!"
    request_input("number")
    puts 
  end
```

When coding the hub method, I started with a while loop and then added an “if” statement that could direct the user to the correct method based on input. Eventually I added the Importer before I hit the loop as I only wanted to call this method once during a given session. Once the session enters the while statement, it will stay in there until the user exits the session. All the other methods in there started as just puts messages and then evolved into functioning methods. I built little test methods that would create some objects and then print those objects. I wanted to make sure that was working properly before I wrote my scraper methods

```
def hub
    main_menu
    input = nil
    Importer.scrape_teas
    while input != "exit"
      input = gets.strip.to_s.downcase
      if input == "menu"
        main_menu
      elsif input == "1"
        Teas.list_a_to_z
      elsif input == "2"
        type_submenu
      elsif input == "3"
        Teas.find_by_name
      elsif input == "4"
        Teas.search
      elsif input == "exit"
        nil
      else
        invalid_input_message(4)
      end
    end
```
		
Eventually I created a module which contained all my menus and messages. This way I could call on them from the Session class or from the Teas class. These modules contained easy to call method that would request info, print out a main menu, print submenus and simply messages for directions on how to use the program or a re-direction if the input is invalid. 


```
module Concerns

  module Menus
    TEA_TYPES = ["White", "Green", "Yellow", "Oolong", "Black", "Puerh", "Blend", "Tisanes"]

    def request_input(item_type)
      puts "   Please enter a #{item_type} from the list."
    end

    def main_menu
      puts "1. List all teas"
      puts "2. List teas by type"
      puts "3. Find tea by name"
      puts "4. Search by word or phrase"
      puts "To exit this sisseion enter: exit"
    end

    def type_submenu
      input = nil
      until TEA_TYPES.include?(input)
        request_input("type")
        TEA_TYPES.each {|item| puts "      #{item}"}
        input = gets.strip.capitalize
      end
      puerh_submenu if input == "Puerh"
      Teas.list_by_type(input) if input != "Puerh"
    end

  def puerh_submenu
    input = nil
    until input == "Ripened" || input == "Raw"
      request_input("type")
      puts "      Raw"
      puts "      Ripened"
      input = gets.strip.capitalize
    end
    Teas.list_by_type(input)
  end
end

  module Messages

    def invalid_input_message(num)
      puts "Sorry, I didn't get that!"
      puts "Please enter 1-#{num} or enter: menu"
    end

    def get_details_message
        puts "To learn more about a tea enter the index number"
        puts "To return to the main menu enter: menu"
    end

    def see_more_message(num)
      puts "To see another profile enter: 1-#{num}"
      puts "To return to the list enter: list"
      puts "To return to the main menu enter: menu"
    end
  end
end
```

Then I coded the class and methods that make it all function which is the Teas class. 
I started by giving each teas object attributes. A few of these attributes will be scraped with the initial scraper method that pulls a list of all teas, which then prints each tea card with the name, other name if available & tea type. 
From the printed list, the user can type in an index number from the list and the second scraper will scrap the individual tea profile page filing in the remaining attributes. 
From the menu options we have the following options:

1. List teas alphabetically
2. List teas by type
3. Fine teas by 
4. Search teas by phrase or word
 
```
class Teas
  extend Concerns::Menus
  extend Concerns::Messages
  attr_accessor :name, :aka, :type, :info, :url, :notes, :origin, :gongfu_instructions, :western_instructions

  @@all = []

  def initialize
    @@all << self
  end

  def self.all
    @@all
  end

  def self.list_a_to_z
    self.all.each.with_index(1) { |t, i| print_tea_card(t, i)}
    get_details_message
    learn_more(self.all)
  end

  def self.list_by_type(input)
    tea_array = self.all.select { |tea| tea.type.include?(input)}
    tea_array.each.with_index(1) { |t, i| print_tea_card(t, i)}
    get_details_message
    learn_more(tea_array)
  end

  def self.find_by_name
    puts "Please enter a tea name"
    input = gets.strip.downcase
    tea_array = self.all.select {|obj| obj.name.downcase.include?(input) || obj.aka.downcase.include?(input)}
    tea_array.each.with_index(1) { |t, i| print_tea_card(t, i)}
    get_details_message
    learn_more(tea_array)
  end

  def self.search
    puts "Enter a phrase or word you would like to search for?"
    input = gets.strip.downcase
    tea_array = self.all.select {|obj| obj.name.downcase.include?(input) || obj.aka.downcase.include?(input) || obj.type.downcase.include?(input) || obj.info.downcase.include?(input)}
    tea_array.each.with_index(1) { |t, i| print_tea_card(t, i)}
    get_details_message
    learn_more(tea_array)
  end

  def self.print_tea_card(t, i)
    puts "#{i}."
    puts "    Name: #{t.name}"
    puts "    AKA:  #{t.aka}" if t.aka != ""
    puts "    Type: #{t.type}"
  end
```
	
I wanted to create a list of items that could function with a numbered list. Select an item, return to that list and select another item. Therefor I created the “learn more” method that stays in a while loop until the menu is selected again. I pass in the array of objects and it operates and print tea cards from that array and references tea by number. This is super functional and intuitive.

  ```
def self.learn_more(array)
    input = nil
    while input != "menu"
    input = gets.strip
      if (1..array.size) === input.to_i
        Importer.scrape_tea_profile(array[input.to_i - 1])
        print_tea_profile(array[input.to_i - 1])
        see_more_message(array.size)
      elsif input == "list"
        array.each.with_index(1) {|t, i| print_tea_card(t, i)}
        get_details_message
      elsif input == "menu"
        nil
      else
        invalid_input_message(array.size)
      end
    end
    main_menu
  end

  def self.print_tea_profile(obj)
    puts ""
    puts "    NAME: #{obj.name}"
    puts "    AKA:  #{obj.aka}" if obj.aka != ""
    puts "    TYPE: #{obj.type}"
    puts "    ORIGIN: #{obj.origin}"
    puts ""
    puts "    INFO:"
    puts "       #{obj.info}"
    puts ""
    puts "    TASTING NOTES:"
    obj.notes.pop
    obj.notes.each do |note|
      n = note.split(/\n/).delete_if(&:empty?)
      puts "       #{n[0]}: #{n[1]}"
    end
    puts ""
    puts "    GONGFU STEEPING INSTRUCTIONS:"
    obj.gongfu_instructions.each { |k, v| puts "       #{k.to_s.upcase.gsub("_"," ")}: #{v.gsub("    ","")}"}
    puts ""
    puts "    WESTERN STEEPING INSTRUCTIONS:"
    obj.western_instructions.each { |k, v| puts "       #{k.to_s.upcase.gsub("_"," ")}: #{v.gsub("    ","")}"}
  end
end
```

To be honest, I went back an forth a lot, I pretty sure I created the scraper mid point through the tea class methods and then I went back and updated my teas class methods and added more functionality. I think that's just the way I work. I think of something, add it, test it. go back to the original thing and so on.
Somewhere after the list by type and list a to z methods in the teas class I wrote my scraper so I could easily see real information.
The scraper was pretty straight forward. I actually really like scraping. Something about gigging around for information and that's really appealing and satisfying when you find it.
I like to start out using “.first” on the end to identify that I'm grabbing the right thing and then enumerate over the site and check my work as I go using pry. It's kind of like a puzzle to me, which is why I enjoy it so much. Don't get me wrong, it can certainly have its frustrations but super rewarding.
The profile page was easier in that I didn't have to iterate over HTML however, some of the info was in larger chunks and had to be parsed to look more presentable. I have to remove lots of spaces and parse by line. However, in the case of the “notes” attribute, the header and the description where separated by “\n” (next line) but the whole section was like that, so instead of splitting the string by line and then joining each section back together, I chose to parse this once into an array by separating by "." and then parse is again in the printer. Lastly I did have to iterate over the steeping instructions and then I created a hash with this information since there are two sets of instructions plus a lot of details. This could have been done several ways, but I thought the hash would make the most scenes. Later in the printer I just print the keys substituting the '_ ' for a space and that prints out quite nicely. 

```
class Importer

  def self.scrape_teas
      doc = Nokogiri::HTML(open('https://meileaf.com/teas/'))
      doc.css('div.product-card__info').each do |tea|
        new_tea = Teas.new
        new_tea.name = tea.css('h2 span').text
        new_tea.aka = tea.css('h3').text
        new_tea.info = tea.css('div.product-card__info-main p').text
        new_tea.type = tea.css('span.product-card__type').text
        new_tea.url = "https://meileaf.com#{tea.css('div.product-card__info-top a').first['href']}"
      end
  end

  def self.scrape_tea_profile(obj)
    path = obj.url
    i = []
    doc = Nokogiri::HTML(open(path))
    obj.notes = doc.css('div.product-tasting-notes dl').text.gsub("    ","").gsub("  ","").split('.').delete_if(&:empty?)
    obj.origin = doc.css('span.squiggle').text.gsub("    ","").gsub("  ","").split(/\n/).delete_if(&:empty?)[2]
    doc.css('td.brewing-instructions__td').each {|item| i << item.text.split(" ").join(" | ").gsub("&plus;","+")}
    obj.gongfu_instructions = {:water_temp => i[0], :grams_per_100ml_water => i[1], :first_infusion => i[2], :additional_infusions => i[3], :total_infusions => i[4]}
    obj.western_instructions = {:water_temp => i[0], :grams_per_100ml_water => i[5], :first_infusion => i[6], :additional_infusions => i[7], :total_infusions => i[8]}
  end
end
```
		
Lastly, I went through and formatted my menus and checked every scenario of my lists and data. Deselected any unused methods created for testing and just tidied up the code. 

Here is what the console looks like when used.


**// ♥ ./bin/teahead**
   
	 Welcome Teahead!
	 Please enter your name:
	 
**ashley**

    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     Hello Ashley!
     Please enter a number from the list.
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      1. List all teas
      2. List teas by type
      3. Find tea by name
      4. Search by word or phrase
      To exit this sisseion enter: exit

**2**
   
	 Please enter a type from the list.
      White
      Green
      Yellow
      Oolong
      Black
      Puerh
      Blend
      Tisanes
			
**yellow**

    ______________________________________________________________________
    1.

    Name: Amber Mountain
    AKA:  Huo Shan Huang Ya
    Type: Yellow
    ______________________________________________________________________
    ______________________________________________________________________
    2.

    Name: Diamond Peak
    AKA:  Junshan Yinzhen
    Type: Yellow
    ______________________________________________________________________
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    To learn more about a tea enter the index number
    To return to the main menu enter: menu
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**2**

    ______________________________________________________________________
  
    NAME: Diamond Peak
    AKA:  Junshan Yinzhen
    TYPE: Yellow
    ORIGIN: Yueyang, Hunan, China.
		
    INFO:
       Fragrant Yellow tea which is highly prized in China. Mountain snow, guava and starfruits with the warmth of pumpkin seeds and vanilla pods.
			 
    TASTING NOTES:
       EYES - DRY LEAF: Yellow and moss green buds and bud and one leaf
       NOSE - DRY LEAF: Creamy and sour hawthorn fruits
       NOSE - WET LEAF: Marzipan, spinach and gooseberry fools
       EYES - LIQUOR: Bright, pale yellow diamonds
       MOUTH - TEXTURE: Light to medium
       MOUTH - TASTE: Mountain snow, guava and starfruits with the warmth of pumpkin seeds and vanilla pods
       NOSE - EMPTY CUP: Apricot jam
       MOUTH - FINISH: Dry with fragrant, fruity tanginess
       EYES - WET LEAF: Clay green plump buds and young leaves
			 
	 GONGFU STEEPING INSTRUCTIONS:
       WATER TEMP: 75°c | 165F
       GRAMS PER 100ML WATER: 3.5
       FIRST INFUSION: 120
       ADDITIONAL INFUSIONS: +15
       TOTAL INFUSIONS: 5
    WESTERN STEEPING INSTRUCTIONS:
       WATER TEMP: 75°c | 165F
       GRAMS PER 100ML WATER: 0.6
       FIRST INFUSION: 240
       ADDITIONAL INFUSIONS: +60
       TOTAL INFUSIONS: 2
    ______________________________________________________________________
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    To see another profile enter: 1-2
    To return to the list enter: list
    To return to the main menu enter: menu
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**menu**
   
      1. List all teas
      2. List teas by type
      3. Find tea by name
      4. Search by word or phrase
      To exit this sisseion enter: exit

**exit**

    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       Goodbye Ashley!
     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~



