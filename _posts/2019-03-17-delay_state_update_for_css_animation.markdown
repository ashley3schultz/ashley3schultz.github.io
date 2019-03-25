---
layout: post
title:      "Delay State Update For CSS Styling"
date:       2019-03-17 23:30:21 -0400
permalink:  delay_state_update_for_css_animation
---

During my time developing this week, I came up with a design idea to display some projects. 
I want my projects to display carousel style as small images in a horizontal row at the top of the page, and when you hover over one, the image enlarges to fill the screen. 

### My react app is structured like this: 

ProjectsContainer
1.   ProjectsList
2. 	Project

### My state looks like this:

```
state {
  projects: {
    {title: "my first project", details: "the nitty gritty", status: "standby"},
	  {title: "my second project", details: "other details", status: "standby"}
  },
  project: {},
  animation: ""
}
```

### 1. Creating the carousel 

**ProjectsList**
```
import React from 'react';

const ProjectsList = props => {
    return (
        <div className="carousel-projects">
            {props.projects.map(project => {
                return (
                    <div key={project.title} className="project">
                        <img 
                            className={project.status}
                            href={project.url} 
                            src={require(`./images/${project.title}.PNG`)} 
                            alt={project.title}
                            onMouseOver={props.handleProjectHover}
                        />
                    </div>
                )
            })}
        </div>
    )
}

export default ProjectsList
```

**CSS**
```
...

.carousel-projects .project {
  padding: 10px;
  width: 200px;
  display: inline-block;
  margin: 10px 0px;
  position: relative;
  text-align: center;
}

.carousel-projects img {
  width: 100%;
  filter: grayscale(100%);
  z-index: 0;
}

.carousel-projects img:hover, .carousel-projects img.featured {
  opacity: 0;
}

...
```

Now the list of images displays correctly and when you hover over, it becomes transparent. 

### Handle the hover
I pass down a handleProjectHover function from the container to the ProjectsList component and on hover the function is called and updates state in a few ways.

1. Updates the **status** on all projects by setting the hovered project to "featured" and all others to "standby" 
2. Updates the **project** to the one that was hovered
3. Updates the **animation** to "loaded"

**ProjectsContainer**
```
...

  handleProjectHover = (event) => {
    const status = event.target.getAttribute('class')
    if (status === "standby"){
      const title = event.target.getAttribute('alt')
      const project = this.props.projects.find(p => p.title === title)
      this.props.updateStatus(title)
      this.props.updateProject(project)
      this.props.updateAnimation('loaded')
    }
  }
	
...
```
	
Note:	*I wrapped most of it in an "if statement" because I don't need to update state if the target is already set to featured.*
### Creating the featured project

The last line of my handleProjectHover function, I update the animation to "loaded" and this is because I need to make sure the project is loaded which styles the image so it rests directly behind the carousel list at its starting point. So when I hover, the project in the list, it becomes transparent revealing the other image that will enlarge.

```
import React from 'react';

const Project = props => {
    const project = props.project
    return (
        <div className='featured-projects'>
            <div key={project.title} className="project">
                <img 
                    className={props.animation}
                    href={project.url} 
                    src={require(`./images/${project.title}.PNG`)} 
                    alt={project.title}
                />
                <h2 className={project.animation}>{project.title}</h2>
                <p className={project.animation}>{project.info}</p>
            </div>
        </div>
    )
}

export default Project
```

**CSS**
```
...

.featured-projects img {
  position: relative;
  top: -135.5px;
  width: 200px;
  margin: auto;
  z-index: -1;
}

...
```

### Creating the animation 

I added some transition styling to my featured image so that when the state changes, the transition slowly enlarges to 80% of the screen. 

**CSS**
```
...

.featured-projects img.playing {
  position: relative;
  top: 0px;
  width: 80%;
  transition-duration: 3s;
  transition-timing-function: ease;
}

...
```

### Adding a delay
I thought that when the project loaded the CSS transition would play out but because state changes so last, the HTML never has a chance to load to the DOM and so the CSS never displays the image at the top of the page first.

At the precise moment the state.animation is set to "loaded", I need to delay the code long enough for the DOM to be updated. It sounds long but it we're talking milliseconds here. To catch that precise moment, I used the life cycle componentDidUpdate function. 

**ProjectsContainer**
```
...

  componentDidUpdate(prevProps) {
    const project = this.props.project
    const aniDif = this.props.animation !== prevProps.animation
    const loaded = this.props.animation === 'loaded'
    if (aniDif && loaded && !!project) {
      this.delayAnimation()
    }
  }
	
...
```

If the criteria are met, the code runs to update the animation but I need my delay just before this happens.

**ProjectsContainer**
```
...

  const delay = (ms) => new Promise(res => setTimeout(res, ms));
	
  delayAnimation = async () => {
    await delay(100);
    this.props.updateAnimation('playing')
  }
	
...
```
	
Note: *This needs to be a asynchronous function because it will pause everything otherwise. *

Finally, the view is as expected!

### All together now

**ProjectsContainer**
```
import React, { Component } from 'react';
import ProjectsList from '../components/ProjectsList';
import Project from '../components/Project';
import { connect } from 'react-redux'
import { fetchProjects, updateStatus, updateProject, updateAnimation } from "../actions/portfolio";

const delay = (ms) => new Promise(res => setTimeout(res, ms));

class ProjectsContainer extends Component {

  componentDidMount() {
    this.props.fetchProjects(projects)
  }

  componentDidUpdate(prevProps) {
    const project = this.props.project
    const aniDif = this.props.animation !== prevProps.animation
    const loaded = this.props.animation === 'loaded'
    if (aniDif && loaded && !!project) {
      this.delayAnimation()
    }
  }

  delayAnimation = async () => {
    await delay(100);
    this.props.updateAnimation('playing')
  }

  handleProjectHover = (event) => {
    const status = event.target.getAttribute('class')
    if (status === "standby"){
      const title = event.target.getAttribute('alt')
      const project = this.props.projects.find(p => p.title === title)
      this.props.updateStatus(title)
      this.props.updateProject(project)
      this.props.updateAnimation('loaded')
    }
  }

  render() {
    return (
        <div className="ProjectsContainer">
            <ProjectsList 
              projects={this.props.projects} 
              handleProjectHover={this.handleProjectHover}
            />
            {this.props.animation ? 
            <Project
              project={this.props.project}
              animation={this.props.animation} 
            /> : <div></div>}
        </div>
    )
  }
}

const mapStateToProps = (state) => {
  return {
    projects: state.projects,
    project: state.project,
    animation: state.animation,
    delay: state.delay
  }
}

export default connect(mapStateToProps, {updateProjects, updateStatus, updateProject, updateAnimation})(ProjectsContainer)
```

**ProjectsList**
```
import React from 'react';

const ProjectsList = props => {
    return (
        <div className="carousel-projects">
            {props.projects.map(project => {
                return (
                    <div key={project.title} className="project">
                        <img 
                            className={project.status}
                            href={project.url} 
                            src={require(`./images/${project.title}.PNG`)} 
                            alt={project.title}
                            onMouseOver={props.handleProjectHover}
                        />
                    </div>
                )
            })}
        </div>
    )
}

export default ProjectsList
```

**Project**
```
import React from 'react';

const Project = props => {
    const project = props.project
    return (
        <div className='featured-projects'>
            <div key={project.title} className="project">
                <img 
                    className={props.animation}
                    href={project.url} 
                    src={require(`./images/${project.title}.PNG`)} 
                    alt={project.title}
                />
                <h2 className={project.animation}>{project.title}</h2>
                <p className={project.animation}>{project.info}</p>
            </div>
        </div>
    )
}

export default Project
```

**CSS**
```
.carousel-projects .project {
  padding: 10px;
  width: 200px;
  display: inline-block;
  margin: 10px 0px;
  position: relative;
  text-align: center;
}

.carousel-projects img {
  width: 100%;
  filter: grayscale(100%);
  z-index: 0;
}


.carousel-projects img:hover, .carousel-projects img.featured {
  opacity: 0;
}

.featured-projects img {
  position: relative;
  top: -135.5px;
  width: 200px;
  margin: auto;
  z-index: -1;
}

.featured-projects img.playing {
  position: relative;
  top: 0px;
  width: 80%;
  transition-duration: 3s;
  transition-timing-function: ease;
}
```

