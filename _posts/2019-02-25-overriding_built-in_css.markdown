---
layout: post
title:      "Overriding Built-in CSS"
date:       2019-02-24 22:37:37 +0000
permalink:  overriding_built-in_css
---


This week while working on a freelance project I came across some plug-in styling issues. Plugin are great and quick way to implement features on WordPress, but they often come with styling that doesn’t suit you needs in some little way. In this case, I had nested drop downs
```
<div>Country</div>
  <div>State</div>
    <div>City</div>
      <div>Content</div>
```
			
So when you click on the country herder you see the state headers and then click in the state header to see the cities and then finally you click on the city header to reveal the content.

If you had a ton of content under each of these tabs, this would be a good setup, but in this case, there was just one listing per state so all those dropdown clicks where cumbersome for our needs.

When you write an application from scratch, the CSS is simple. You just name an attribute and specify the styling for the section. But when working with plugins, you have the styling that comes with the plugin and various CSS functions used to style even one thing as styling can be inherited so it not always the direct path that was used to style. So in order to write CSS to fix the solution, you first need to know what CSS attributes are being called to style that section.

There are a couple tricks to finding this.
Inspect the page - Go to the live page and right click on the attribute
Inspect the CSS. In the html select the highlighted line then click the CSS tab on the right the side, in my case I just needed to grab the edge and pull it to expand the CSS section.
This way you can see the CSS affecting that attribute.

You can play around in the dom to see what html attributes change when the item is clicked.

In my case there were several tiers of div selectors with the class equal to ”collapse” that changed when opening and if I click into the class and remove the collapse, there was the content for the list item so that was the item I needed to change.

We have two things we need to accomplish with the CSS
Remove the tabs
Show the content

Removing the tabs is simple because the CSS I add to the WordPress site will override the other one. But let’s oiling e the hierarchy of the html

**HTML**

```
<div class=“collapse” expanded=“false”>
  <ul>
    <li>
      <a id=“county-0”> DON’T REMOVE
      <div class=“collapse” expanded=“false”>
        <ul>
          <li>
            <a id=“state-0-0”> REMOVE </a>
            <div class=“collapse” expanded=“false”>
              <ul>
                <li>
                  <a id=“city-0-0-0”> REMOVE
                  <div class=“collapse” expanded=“false”>CONTENT
```

The divs with the content is nested so we can’t make the divs hidden as we will hide the content as well. So we just hide the “a” tags which will remove the drop downs. But since all the ids are different, we need the starts with “^” characters to grab these.
And instead of stating it like a#state you use a[id^=state]

**CSS to remove dropdown**
```
a[id^=state], a[id^=city] {
display: none;
}
```

Hunting for why the div weren’t showing was more difficult because they were inherited. It I finally identified the when they are selected, they display block style. Be Ian our nested attribute is not displaying when the top level is clicked we need to specify that is displays block when the top level is selected since the children will never be selected.

**CSS to show content only if selected**
```
div[expanded=true] div.collapse {
display: block;
}
```

This will automatically hide everything using the built in Plugin CSS and only display block when the top level expended attributes is equal to true.

Finally we have no nested drop downs just the one for the country and the listed items will be displayed under each one only when selected.

This blog was written while drinking Long Jing Tea.

