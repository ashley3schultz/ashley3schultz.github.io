---
layout: post
title:      "Tea on rails"
date:       2018-07-22 19:41:38 +0000
permalink:  tea_on_rails
---


This was a fun project; this application follows restful convention but also has some interested adjustments to make it more user intuitive. Instead of focused on the entire process of the application, I decided to focus on the exceptions or special features.

**The Project Idea**
User can sign up
User can add listed teas to their collection
Users can post on any listed tea
Users can rate a tea in their collection
User can contribute a tea

Admin approves tea contributions
Admin can make others admin or non-admin
Admin can delete any posts

**User: Posts form**
This form is shown at the bottom of the tea show page
You can submit the post right on the page and it will take you back to the page. Editing the post will generate a form right on that page but since, we have a new post form and a edit post form both on the page, I have to define two variables, one for @edit_post and one for @new_post, plus, I had to set variable for @tea for the show page. I integrated extra function with edit action and to be able to handle that function.

```
def edit
@new_post = Post.new
@tea = Tea.find(params[:tea_id])
@user = current_user
if owner?(@post)
@edit_post = @post
end
render :'teas/show'
end
```

**User: Tea collection**
I added extra action beyond the restful convention to add a tea to your collection and to remove a tea from your collection. I wanted to keep this code out of the create function for teas, I thought about adding a different controller o handle this but since it’s added a tea to the users collection, it made sense that it lived in the tea controller especially because I could pass the tea into a nested route.
```
def add
current_user.teas << Tea.find_by(id: params[:id])
	redirect_to teas_path
end
```

```
def remove
	UserTea.find_by(tea_id: params[:id], user_id: current_user.id).destroy
	redirect_to root_path
end
```

**User: Tea Rating**
The tea rating is routed to the user_teas controller. This is a joins table and updates that based on the current user and the tea from a nested route. I passed the rating in the route as well, it ended up looking like this teas/1/rating/4 will give it a rating of 4 out of 5 rating. However, there is a validation that it must be 1-5 so teas/1/rating/9 will default to the max rating.

```
def rate 
	@row = UserTea.find_by(tea_id: params[:tea_id], user_id: current_user.id)
	if @row
		@row.update(rating: params[:id])
	end 
	redirect_to tea_path(@row.tea)
end
 ```
		
**User: New Tea custom validation**
I want to only create a pending tea if the tea does not already exists either in the pending teas table or in the teas table, however when it comes to tea, they often have two names, like dragenwell can also be known as long Jing or even longjing
So I it need to compare without spaces, and I also need to compare new tea fields like aka and name to any existing aka or names in either table.

New entry has
Name: Longjing
AKA: Dragon well

The database has
Name: Dragonwell
AKA: Long Jing

Therefor this validation should fail.

```
def uniue_entry
teas = []
parr = PendingTea.all.pluck(:name, :aka)
parr.each do |a|
a.each do |n|
teas << n.downcase.gsub(" ","") unless n.nil?
end
      	end
      	tarr = Tea.all.pluck(:name, :aka)
      	tarr.each do |a|
       		 a.each do |n|
          			teas << n.downcase.gsub(" ","") unless n.blank?
       		 end
     	 end
     	 if teas.include?(name.downcase.gsub(" ",""))
         		 errors[:name] << "#{name} exists"
      	end
     	 if teas.include?(aka.downcase.gsub(" ",""))
          		errors[:aka] << "#{aka} exists"
     	 end
    end
```

**User: Friends** 
This is simular to what I used in my Sinatra app but I liked the idea so much, I wanted to keep it. This will find connections based on if you have both have the same tea is your collection. Eventually I will add a connection strength based on if two users have rating one tea similarly within one point difference, the more tea rating you hve in common with anotheer user, it will create a stronger connection.

```
def friends
	friends = []
	teas.each do |tea|
		tea.users.each do |user|
		friends << user if user != self
end
end
friends.uniq
end
```

**User: Contributions**
Users can earn contributions points for each post they make or each tea they contribute.

**Admin: Home Page**
The home route is the main one for any user weather admin or non-admin, however, I wrote the method so that it weill render a particular page depending on your status.
1. if you aren not logged in it will only show you the signin/login page
2. if you are admin, it will rendor the admin page
3. otherwise it will rendor the application home page.

```
def home
if logged_in?
@posts = Post.where.not(user_id: "#{current_user.id}")
if admin?
        			@p_teas = PendingTea.all
        			@users = User.where.not(id: "#{current_user.id}")
        			@teas = Tea.all
        			render :admin
      		else
        			@p_teas = PendingTea.all
        			@users = User.all
       			 render :home
      		end
    	else
      		render :welcome
    	end
  end
```
	
Along with that home page check I created several helper methods to redirect based on status
	
Logged Out Redirector
```
def lo_redirector
if !logged_in?
render :welcome
   	 end
  end
```
	
Logged In redirector
```
def li_redirector
   	 if logged_in?
      		redirect_to root_path
    	end
  end
```
	
Non-Admin Redirector
```
def na_redirector
    	if !admin?
     		 redirect_to root_path
    	end
  end
```
	
I then just use a before_action macro to control what the user sees and has authorization to do.

This created a lot of extra features, but I felt like the app was way more interesting with these features. 

Note: This blog was written while drinking Midnight Sun Oolong Tea aka: Nong Xiang Jin Xuan.

