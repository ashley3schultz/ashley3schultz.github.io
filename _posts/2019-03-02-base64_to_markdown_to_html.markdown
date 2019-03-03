---
layout: post
title:      "Base64 to Markdown to HTML"
date:       2019-03-03 03:36:31 +0000
permalink:  base64_to_markdown_to_html
---

My blogs are in a Github repo which mean I can fetch them through the Github API. 
The list of blogs can be accessed at: 
https://api.github.com/repos/ashley3schultz/ashley3schultz.github.io/contents/posts/

But the content of each blog needs to be fetch singularly at: 
https://api.github.com/repos/ashley3schultz/ashley3schultz.github.io/contents/posts/2018-04-13-where_the_journey_begins.markdown

On ComponentDidMount I fetch the list of blogs and store them to state, then when the link is clicked, I fetch the content for that blog. 

Meanwhile the files are in originally written in markdown then encoded with Base64 for the API so my agenda becomes:

1. Fetch the blog list from Github
2. Fetch the blog content from Github
3. Translate Base64 to Markdown
4. Translate Markdown to HTML

## Fetch The Blog List from Github
I started be creating a fetch request to Github 
```
const API_URL = "https://api.github.com/repos/ashley3schultz/ashley3schultz.github.io/contents/_posts/"

export const fetchBlogs = () => {
    return dispatch => {
        return fetch(API_URL)
        .then(response => response.json())
        .then(blogs => dispatch(updateBlogs(blogs)))
        .catch(error => console.log(error));
    }
}
```
The JSON contains this info: 
```
{
    {
		    name: "2018-04-13-where_the_journey_begins.markdown",
				path: .....
				sha: .....
				size: .....
				url: .....
				html_url: .....
				git_url: .....
				download_url: .....
				type: "file",
				_links: .....
    },
		... ... ...
}
```
We only need the name attribute because the date and name are combined.
I created a function to parse the date and title from the name.
```
const title = (data) => (
    data.substr(11).replace(/(.markdown)|_/gi," ").replace(/^(.)|\s(.)/gi, ($1) => $1.toUpperCase())
)

const date = (data) => (
    new Date(data.substr(0,10)).toString().substr(4,11)
)
```
I could have done this in the blogList component but then if I need to call this in other places later, the code gets ugly pretty quick. I like to have clean data with no extra items being stored in state so I simply iterate over the JSON to create a new list of objects that only contains the info I need. 
```
export const updateBlogs = (blogs) => {
    const list = []
    blogs.map((blog) => {
        const item = {
            title: title(blog.name), 
            date: date(blog.name),
            path: '/blog/' + blog.name.replace('.markdown','')
        }
        return (list.push(item))
    })
    return { type: "UPDATE_BLOGS", blogs: list.reverse() }
}
```

Now each item in the array looks more like this:
```
[
    {
        title: "Where The Journey Begins",
		    date: "Apr 20 2018",
		    path: "where_the_journey_begins",
				content: ""
    }, 
		... ... ...
]
```

## Fetch The Blog Content from Github
When the blog is clicked, another fetch happens to collect the content. 
```
export const fetchBlog = (name) => {
    return dispatch => {
        return fetch(API_URL + name)
        .then(response => response.json())
        .then(blog => dispatch(updateBlog(blog)))
        .catch(error => console.log(error));
    }
}
```
The JSON contains this info: 
```
{
    name: "2018-04-13-where_the_journey_begins.markdown",
    path: .....
    sha: ".....
    size: .....
    url: .....
    html_url: .....
    git_url: .....
    download_url: .....
    type: .....
    content: "LS0tCmxheW91dDogcG9zdAp0aXRsZTogICAgICAiV2hlcmUgdGhlIEpvdXJu
		ZXkgQmVnaW5zIgpkYXRlOiAgICAgICAyMDE4LTA0LTEzIDE3OjAxOjUxIC0w
		NDAwCnBlcm1hbGluazogIHdoZXJlX3RoZV9qb3VybmV5X2JlZ2lucwotLS0K  ",
		encoding: "base64",
		_links: .....
}
```

## Translate Base64 to Markdown
This time we need the content attribute and we need to decode the Base64 using this argument "atob()"
 function
 ```
 export const updateBlog = (data) => {
    return { type: "UPDATE_BLOG", blog: window.atob(data.content) }
}
```
This outputs markdown, which the files are originally written in. Markdown looks like this:
```
---
layout: post
title:      "Where the Journey Begins"
date:       2018-04-13 17:01:51 -0400
permalink:  where_the_journey_begins
---

I have always been a student at heart. I don’t like just knowing information but I enjoy understanding it and I enjoy the process to understanding. 

**I am a student of tea…**

I had no original agenda to become so knowledgeable about tea, it was really my passion that made a way for me. I wanted to know more about tea so I began a journey.
```

For some reason the content included strange characters (â) wherever quotes where in the blog so we use a function to replace those funky characters with quotes and then we need to get rid of the headers. 
```
const content = (data) => (
    data.replace(/(â)/gi,"'").replace('---','').replace('---','&&&').split('&&&')[1]
)
```
Now our blog looks more like this:
```
{
title: "Where The Journey Begins",
date: "Apr 20 2018",
path: "where_the_journey_begins"
content: "
I have always been a student at heart. I don’t like just knowing information but I enjoy understanding it and I enjoy the process to understanding. 

**I am a student of tea…**

I had no original agenda to become so knowledgeable about tea, it was really my passion that made a way for me. I wanted to know more about tea so I began a journey."
}
```

## Translate Markdown to HTML
I do this in the blog show component. There is a prebuilt solution for this called Showdown which you can install and use pretty simply. 
```
import React from 'react';

const Blog = props => {
    var showdown  = require('showdown'),
        converter = new showdown.Converter(),
        mytext = props.blog.content,
        myhtml = converter.makeHtml(mytext)
				
    return (
        <div className="blog-content">
            <h2 className="blog-title">{props.blog.title}</h2>
            <p className="blog-date">{props.blog.date}</p>
            <div dangerouslySetInnerHTML={{__html: myhtml}} />
        </div>
)}

export default Blog
```

This now gives us clean html to use.

