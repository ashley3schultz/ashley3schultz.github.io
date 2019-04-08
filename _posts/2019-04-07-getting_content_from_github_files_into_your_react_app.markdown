---
layout: post
title:      "Getting Content From GitHub Files Into Your React App"
date:       2019-04-08 03:05:51 +0000
permalink:  getting_content_from_github_files_into_your_react_app
---


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

### Conteiner 

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

