---
layout: post
title:      "CSS Animation VS Transition"
date:       2019-03-25 01:53:14 +0000
permalink:  css_animation_vs_transition
---

Let's say I have a image styled in the top left corner of the screen at point A and I want to move it from there to the bottom right corner of the screen at point B. I could use transition or animation but that act differently. 

### Transition 
Transition moves the image from point A to point B and stays at point B. 

```
img.pointA {
  position: absolute;
  top: 0px;
	left 0px;
}

img.pointB {
  position: absolute;
  bottom: 0px;
	right: 0px
  transition-duration: 5s;
  transition-timing-function: ease;
}
```

When the image class changes to "pointB" the transition starts. It will take five seconds to move from the top left corner to the bottom right corner and then it will stay there. 

### Animation 
The animation moves from point A to point B but will return to the styled point wherever that is. If I wanted it to stay at point B I would need to style it there. With animation, you can use keyframes which is a function that can be called within another function. 

```
img {
  position: absolute;
  bottom: 0px;
  right: 0px;
  animation: 5s ease moveImage;
}

@keyframes moveImage {
	0% {
    transform: translateY(-100) ; 
	}
	100% {
    transform: translateY(0) ;
	}
}
```

### Real application styling
In my real application, I explained in my blog last week that I want the image to expand from the middle of the carousel and scale up and move down. The laptop image is going to fade in around the project image. 

```
.featured-projects img {
  opacity: 0;
  width: 71%;
  position: absolute;
  top: 60%;
  left: 14.5%;
  z-index: -2;
}

.featured-projects img.playing {
  opacity: 1;
  -webkit-animation: 500ms ease projImage;
  -moz-animation: 500ms ease projImage;
  animation: 500ms ease projImage;
  z-index: 0;
}

@-webkit-keyframes projImage {
	0% {
    -webkit-transform: translateY(-76%) scale(.25);
    transform: translateY(-76%) scale(.25);
	}
	100% {
    -webkit-transform:  translateY(0) scale(1);
    transform: translateY(0) scale(1);
	}
}

/*  PROJECTS - LAPTOP IMAGE   */

.laptop img {
  width: 98%;
  position: absolute;
  top: 54%;
  margin: auto;
  left: 1%;
  opacity: 0;
  z-index: 0;
}

.laptop img.playing {
  margin: auto;
  opacity: 1;
  z-index: 1;
  -webkit-animation: 500ms ease projLaptop;
  -moz-animation: 500ms ease projLaptop;
  animation: 500ms ease projLaptop;
  -webkit-transition: 500ms ease 500ms opacity;
  -moz-transition: 500ms ease .1s opacity;
  transition: 500ms ease .1s opacity;
} 

@-webkit-keyframes projLaptop {
	0% {
    -webkit-transform: translateY(-60%) scale(.25);
    transform: translateY(-60%) scale(.25);
  }
	100% {
    -webkit-transform:  translateY(0) scale(1);
    transform: translateY(0) scale(1);
	}
}
```

### CSS Responsively
Because we are using "position: absolute", 100% is the screen width not the container width. Because of that, I want to scale up to 1000px but no more so I added a @media for when the screen get equal to or larger than 1000px wide.

```
@media screen and (min-width: 1000px) {

  div.featured-projects img {
    width: 710px;
    position: absolute;
    top: 60%;
    left: calc(calc(100vw - 710px) / 2);
    z-index: -2;
  }  

  div.laptop img {
    width: 980px;
    position: absolute;
    top: 54%;
    left: calc(calc(100vw - 980px) / 2);
    opacity: 0;
    z-index: 0;
  }
  
}
```
	
Where in the smaller screen widths I did 98% and 1% margin on each side so the image stays centered. 
On the larger screen, the calculation function helps to define the margin on each side so that the image stays in the center of the screen like: 100% - 980px / 2

