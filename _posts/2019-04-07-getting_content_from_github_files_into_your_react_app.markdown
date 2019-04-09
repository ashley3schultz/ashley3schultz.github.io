---
layout: post
title:      "Getting Content From GitHub Files Into Your React App"
date:       2019-04-07 23:05:52 -0400
permalink:  getting_content_from_github_files_into_your_react_app
---

I have a application I have been working on and given that I have all the content in a GitHub repo, I decided to fetch all data from there to feature in my portfolio site. As a sample I am fetching the few paragraphs from a file. This is the simplest version of all the content I’m fetching from the GitHub API while some other fetches involve fetching all the file titles like "Blog Titles" and then fetching the content as needed. 

### Container 

```
import React, { Component } from 'react';
import About from '../components/About';
import { connect } from 'react-redux'
import { fetchAbout  } from "../actions/portfolio";

class AboutContainer extends Component {

  componentDidMount() {
    this.props.fetchAbout()
  }

  render() {
    return (
        <div className="AboutContainer">
            <About about={this.props.about}/>
        </div>
    )
  }
}

const mapStateToProps = (state) => {
  return {
    about: state.about
  }
}

export default connect(mapStateToProps, {fetchAbout})(AboutContainer)
```

### Component

```
import React from 'react';

const About = props => {
    const imgPath = "https://raw.githubusercontent.com/ashley3schultz/ashley3schultz/master/images/About.jpeg"
    var showdown  = require('showdown'),
            converter = new showdown.Converter(),
            mytext = props.about,
            myhtml = converter.makeHtml(mytext)
    return (
        <div className='about'>
            <img className='profile-pic'
                src={imgPath} 
                alt='profile'
            />
            <div className='about-p'>
                <div dangerouslySetInnerHTML={{__html: myhtml}} />
            </div>
        </div>
    )
}

export default About
```

*Note: I am even getting the image from the repo using this URL pattern "https://raw.githubusercontent.com/USERNAME/REPONAME"*

### Actions

```
const UN = "ashley3schultz"
const GH = `https://api.github.com/repos/${UN}/${UN}`
const ABOUT_URL = `${GH}/contents/about.md`
const AUTH = `?client_id=${process.env.REACT_APP_AI}&client_secret=${process.env.REACT_APP_AS}`

export const fetchAbout = () => {
    return dispatch => {
        return fetch(ABOUT_URL + AUTH)
        .then(response => response.json())
        .then(about => dispatch(updateAbout(about)))
        .catch(error => console.log(error));
    }
}

export const updateAbout = (data) => {
    const about = window.atob(data.content).replace(/(â)/gi,"'")
    return { type: "UPDATE_ABOUT", about: about }
}
```

Here I fetch my GitHub Client Id and Client Secret from the .env file

### Reducer

```
export default function portfolioReducer(
    state = {
        about: ""
    }, action) {
    switch (action.type) {

        case "UPDATE_ABOUT":
            return {...state, about: action.about }

        default:
            return state;
    } 
  }
```

This setup allows me to update content whenever with our adding authenticated login to my site since I rely on my GitHub Acct. If I wanted to make the app dynamic which loads different content for different users, we could create an API to fetch data pretty easily with this setup.

