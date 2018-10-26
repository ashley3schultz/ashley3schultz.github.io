---
layout: post
title:      "Flattening Deeply Nested Arrays"
date:       2018-10-26 22:24:44 +0000
permalink:  flattening_deeply_nested_arrays
---


Recently I put together a function that flattens an array. Simple enough but I decided to also add feature for flattening deeply nested arrays as well, because why limit yourself. I actually went fully for it and made a small react app where you can enter an array into the form and it will return the flattened array. 

For setup in my react app I added to state attributes, one for the array entered and one for the flattened array. 
I chose not to have this be the same one so that you could see the previous one and the flattened one at the same time rather than just updated the original. 

**The State**

```
  state = {
    input: [],
    array: []
  }
```

**Storing The Input**

```
  handleInput = (event) => {
      this.setState({input: event.target.value})
  }
```


**Change string input into actual array**

```
  handleSubmit = (event) => {
    event.preventDefault()
    var nestedArray = JSON.parse(this.state.input)
    this.setState({array: this.flattenArray(nestedArray)})
  }
```

**Flatten Array when called in handleSubmit Function**

```
  flattenArray = (array) => {
    var newArray = []
    var deepFlatten = (b) => {
      if(b.length > 1) {
        b.forEach((c) => { 
          deepFlatten(c) 
        })
      } else {
        newArray.push(b)
      }
    }
    array.forEach((a) => { 
      deepFlatten(a) 
    })
    return newArray
  }
```

One of my favorite JS features is that you can call a function while inside that same function. I created an empty array variable within the flattenArray function and then declared a function inside of it to deeply flatten. Anytime there is an array while iterating over the array, it simply we call the function again and at the end we have a completely flattened array. For a code challenge this was meant for integers only. 
This is what we end up with

```
var array = [1, 2, 3, [4, [1, 2, 3]], 5, [6]]

flattenArray(array)
=>  [1, 2, 3, 4, 1, 2, 3, 5, 6]
```

