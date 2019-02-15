---
layout: post
title:      "Custom Functions for WordPress"
date:       2019-02-15 23:33:53 +0000
permalink:  custom_functions_for_wordpress
---


This week I had a few tasks in my freelance developer world. Most of the time these tasks consist of front end development, adding some CSS, formatting a page, or just researching plugins to incorporate a certain feature. But this time, I didn't find a plugin that met me needs. 

I was looking for a plugin that displays certain information for the current user. Yes, there are plugin that offer that but there usually limited to a handful of shortcodes and I have found them to be limited. 

> In light of the fact that I have already been using a plug in called *User Shortcodes* I decided to grab all data they provided with their six shortcodes. 
*User Shortcodes* offers six shortcodes
1. [currentuser_username] – Display the current user name
2. [currentuser_useremail] – Display the current user email
3. [currentuser_displayname] – Display the current user display name
4. [currentuser_firstname] – Display the current user first name
5. [currentuser_lastname] – Display the current user last name
6. [currentuser_id] – Display the current user numeric ID

All that, but I still needed two more user attributes, so I decided to just a write a custom function to create shortcodes. 

Basically I need a phone number and an address so I started with the phone number function.

**Phone Number**
```
function show_cu_phone_function( $atts ) {
global $current_user, $user_login;
      wp_get_current_user();
add_filter('widget_text', 'do_shortcode');
return "{$current_user->billing_phone}";
}
add_shortcode( 'cu_phone', 'show_cu_phone_function' );
```

Note: on the 5th row (the return line) this is where we get a certain attribute.
To find the exact text (i.e. "billing_phone") for the attribute, 
1. Go into a user profile on your WordPress site.  
2. Click edit on any user
3. Scroll down to the fields section (there are several and even more if you have plugins that use them)
4. Right click on the field you want and then select inspect from the dropdown
5. When you see the html pop up then copy the id from the highlighted field

It will look something like this 
```
<input type="text" name="billing_phone" id="billing_phone" value="" class="regular-text">
```

so we repeat these steps to create the address shortcode, this this time there are several attributes the address so we format everything a little different
 
 ```
function show_cu_street_function( $atts ) {
global $current_user, $user_login;
      wp_get_current_user();
add_filter('widget_text', 'do_shortcode');
return "{$current_user->billing_address_1} {$current_user->billing_address_2}";
}
add_shortcode( 'cu_street', 'show_cu_street_function' );
``` 

Note: On the 5th line where we call attributes, I am actually returning the line 1 and line 2 of the address fields. 

Similarly I created a function for the city, state and zip section of the address. I did these in separate functions because I need to parse the information in a particular way. But you could make this one function. 

 ```
function show_cu_csz_function( $atts ) {
global $current_user, $user_login;
      wp_get_current_user();
add_filter('widget_text', 'do_shortcode');
return "{$current_user->billing_city} {$current_user->billing_state} {$current_user->billing_postcode}";
}
add_shortcode( 'cu_csz', 'show_cu_csz_function' );
```

Now that we have all the functions, let’s talk implementation. 

There are two ways to go about implementing these new functions so you can use them on the site. 

**Plugin**
*My Custom Functions* is a WordPress plugin that makes this all very easy. This is easier for a couple reasons. 
1. no need to access the application files. 
2. If you update your theme, you don't need to worry about transferring the functions over. 
3. If there is an error in your code, the plugin will let you know when you try to save it. 

All you need to do is install and activate the *My Custom Functions* plugin and add the functions in the settings section. 
You do need to make sure it is turned on. There is a toggle located near the upper right of the text area.  
Then save it and you should be set. 


**Update theme**
To update the theme you need to have access to the application files. Go in and edit the functions.php file and paste the functions in the file. They should go just above the last closing tag.
```?>```

Make sure to check you site to be sure the code is working and it didn't break the site, as there's always a chance when editing those files. Also keep in mind that if you update your theme, these shortcodes go with it. 

No finally in a page, we can call our shortcodes like this.

**[cu_phone]
[cu_street]
[cu_csz]**

This is how it looks when editing the page, but if the fields are not empty, you will see the data printed on the actual page. 

Just for reference, here are links to the two referenced plugins:
1. [User Shortcodes](https://wordpress.org/plugins/user-shortcodes/)
2. [My Custom Functions](https://wordpress.org/plugins/my-custom-functions/)

