---
layout: post
title:      "Teafolio & Javascript"
date:       2018-08-16 21:48:57 +0000
permalink:  teafolio_and_javascript
---


Not to be confused with Tea v/s Java wich could be a heavy debate.
I'm blown away by the simplicity of javascript's function, pun intended...
Yet in it's simplicity  has emense capability and a makes eveything more user friendly. I think one of my most loved features is that when I chage something on the page, it doesn't refresh and therefore I keep my place on the page. Could you imaging scrolling through your instagram feed and eveytime you like or comment on a post that is took you right back to the top. That's a historic event now and thankfully forever in our past. 

Adding javascript to my teafolio app was alot of fun, I really do love the logic of working the backend but I really enjoyed this too as you get instant resaults, teasting is pretty fun to because you can track the process all the way through to the final post to the dom. 
Here are a few features I added to my app.

**All Things Posting**
this a one of the most obvious potentials for javascript. 
I just added an event listener on my new post form submit button which calls the createPost function which makes an ajax request to the server and then posts that info onto the dom. I created classes for both teas and posts and I used a serializer to process data into JSON.

```
$('#new_post').submit(function(e){e.preventDefault(); createPost(this)})`

function createPost(obj){
  var path = $('.new_post').attr('action')
  var values = $(obj).serialize()
  var posting = $.post(path + '.json', values)
  posting.done(function(info){
    $("#posts").append(`<div class='profile' id='postid-${info.data.id}'></div>`)
    post = new Post(info.data)
    postProfile(post)
    fillEditForm(post)
    $('#new_post textarea').val('')
    $("input").removeAttr('disabled')
	})
}
```

I won't paste all my code on here as there's over 200 lines of code but I'll highlight the main functions. I call several functions within createPost to access profile templates etc. 

Secondly I added function to process updating and delting a post. 
I simply changed the html.erb file to have hidden and show divs so that if i want to edit the post, when i click edit it calls renderEditForm which changes an attribute from hide to show and the css does the rest. At this same point I add a listener for that form. I can't build it into the form because in the begining there may not be any posts, no posts means no edit form. However instead of printing a long string of a form HTML to the dom, I decided to grab the HTML for the new post for and simply interject the data of the post. This happen during a new post creation that way you can create then edit it multiple times without a refresh. 

**Add, Rmove & Rate Teas**
This is several functions but they all end up a part of the same category which is tea buttons. at the bottom of each tea profile there is a set of button to rate the tea which only apear if the tea is in your collection and a button that either say 'Add to collection' or 'Remove from collection' based on weather or not it's in your collection. You can then rate that tea imediately without ever having to refresh, this means you can scroll through the list of teas, add, rate or remove and contiue scrolling.At first I had the button html info in the addTea and rmvTea functions and called those whenever I needed that HTML, however that was a major oversight as a quickly realized that hitting the database evertime, therefore I added a teaBtns which determins if the current user owns that tea end renders the correct HTML. This also hits the database but it doesnt make any updates. 

```
function addTea(teaid){
  var posting = $.get(`/teas/${teaid}/add.json`)
  posting.done(function(info){
    teaBtns(info.data.id)
  })
}

function rmvTea(teaid){
  var posting = $.get(`/teas/${teaid}/remove.json`)
  posting.done(function(info){
    teaBtns(info.data.id)
  })
}

function rateTea(teaid, num){
  var posting = $.get(`/teas/${teaid}/rate/${num}.json`)
  posting.done(function(info){
    var id = info.data.id
    $(`#teaid-${id} button.selected`).attr('class', 'rate-button')
    $(`#teaid-${id} button#rate-${num}`).attr('class', 'selected')
  })
}
```

**View the Next Tea**
This rendors the next tea without refresh. It creates a new tea object and then uses to call other functions that show each setion of the profile. The most difficult part was getting the tea rating to showup. Since this data lives in the joins table for UserTeas it is not returned with the tea data and so I end of hitting the database just for that detail to return the current user's rate of the tea. This happens during the creating of the tea buttons which is called in the teaProfile which is called in the nextTea function. 

```
function nextTea(teaid){
  var form = $('#newForm')[0].innerHTML
  $.get(`/teas/${++teaid}.json`, function(info){
    tea = new Tea(info.data)
    $('.tea-page').html(`
      <a href="javascript:nextTea(${tea.id})">Next</a>
      <div class="tea"></div>
      <div id="posts"></div>
      <div id="newForm">${form}</div>`)
      teaProfile(tea)
      teaPosts(tea.posts)
      $(`#new_post input#post_tea_id`).val(`${tea.id}`)
  })
}
```

**Just For Fun**
Lastly I wanted some additonal features outside of the requirement and so I pressed on.

**Toggle Views**
I added some view feature toggles on the Profile page to show the different types of data.
This is housed in a side bar for a clean look and renders recent posts by default.

Recent Posts
My Pending Teas
My Collection
My Posts
My Friends

Simularly, I added a toggle to show different tea types on the tea index page.

Black Tea
Green Tea
Oolong Tea
Puerh Tea
White Tea
Yellow Tea
All Tea

Lastly, a toggle tfor the admin view to feature a one page structure.

The great part is that each link contains the info about its feature and I can handle everything in on tiny function.

```
function toggleView(view){
  $(`.show-search`).html('')
  if(view === 'all'){
    $(`.hide`).attr('class', 'show')
  }else{
    $(`.show`).attr('class', 'hide')
    $(`#${view}`).attr('class', 'show')
  }
}
```

**Live Search**
a simple bar at the top of the teas index page which renders the resaults as you type. 
I first create a listener that processes this information on input. 
I created a new field to append this data and i simply hide the original teas as I want to be able to go back to all the teas and they will still be there when selecting 'All Teas' etc.
I grab the value in the search bar then send it an action to get all teas that match. 
Then I simply just print the resaults to the search section in the dom.

```
$('#search').on('input', function(e){e.preventDefault(); search(this)})

function search(item){
  $('.show').attr('class', 'hide')
  $('.show-search').html('')
  var string = $('#search').val()
  $.get(`/search/${string}.json`, function(info){
    info.data.forEach(function(t){
      tea = new Tea(t)
      $('.show-search').append(`<div class="profile" id="teaid-${tea.id}">
        <h2 class="tight">${tea.oxidation} Tea</h2>
        <h3 class="tight"><a href="/teas/${tea.id}">${tea.fullName()}</a></h3>
        ${tea.description}<br><div class="add-rmv"></div></div>`)
        teaBtns(tea.id)
    })
  })
}

```

**Tea Timer**
Very Lastly for the fun stuff, I added a tea timer. You can inup the time and it will count down and throw an alert when it hits zero. wrote extra code to handle the time format. basicaly I have two input boxes, on for minutes and another for seconds and if it's less than 10 it renders with a 0 infront to give it that timer look.

```
function timer() {
  var min = $('#min').val()
  var sec = $('#sec').val()
  if((min > 0) || (sec > 0)){
    if(sec == 0){
      if(min < 11){min = '0' + --min}else{--min}
      $('#min').val(`${min}`)
      $('#sec').val(`59`)
    }else{
      if(sec < 11){sec = '0' + --sec}else{--sec}
      $('#sec').val(`${sec}`)
    }
    setTimeout(timer, 1000);
  }else{
    alert('alarm')
  }
}
```

And with that the application is complete.






 


