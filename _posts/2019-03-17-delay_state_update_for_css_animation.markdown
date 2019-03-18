---
layout: post
title:      "Delay State Update For CSS Animation"
date:       2019-03-18 03:30:20 +0000
permalink:  delay_state_update_for_css_animation
---


### Container
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

### Index
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

### Show
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

### CSS
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
