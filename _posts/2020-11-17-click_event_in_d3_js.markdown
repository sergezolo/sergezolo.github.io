---
layout: post
title:      "Click event in D3.js"
date:       2020-11-17 05:26:57 +0000
permalink:  click_event_in_d3_js
---


I wanted my JavaScript project to be simple but interesting, and that idea led me to make an interactive map. I started googling how I could make the map and found a few sources with a complete solution, one of which I have used in my project.
So, first of all, I want to say a big thank you to a user by nickname NPashaP, who published the amazing US map on GitHub (https://gist.github.com/NPashaP/a74faf20b492ad377312#file-block).
Looking at the map code I’d split it into two main parts. The first part was an array of objects with state, its abbreviation, and a long string that represented the state outline:
```
[{id:"OK",n:"Oklahoma",d:"M380.34313,320.82146L363.65895,319.54815L362.77873,330.50058L383.24411,331...324.70805L449.67324,324.25081L404.63433,322.19319L380.34313,320.82146Z"}…]
```
The second part was having functions, that looked familiar and a bit different at the same time. They all had one thing in common: d3. A quick search showed that it was D3.js library, used for producing dynamic and interactive visualizations on the web.
A few facts and features:
-        stands for Data-Driven Documents.
-        developed by Mike Bostock to create custom interactive data visualizations using SVG, HTML, and CSS.
-        dynamic, intuitive and needs a minimum amount of effort.
-        Official web site: d3js.org
-        D3 Source code: https://github.com/d3/d3
-        the transition() function is quite powerful because internally, D3 works out the logic to interpolate between your values and find the intermittent states.
-        D3 provides great support for animation with functions like duration(), delay() and ease(). Animations from one state to another are fast and responsive to user interactions.
Code already had functions for hovering the states, but I also needed to add a click function for selecting or removing the states from my list. Sounds easy, but it took me almost a week to write only one function.
```
function mouseOver(d){
			d3	.select("#label")
				.transition()
				.duration(200)
				.style("opacity", .9);   

			d3	.select("#label")
				.html(label(d.n, data[d.id]))  
				.style("left", (d3.event.pageX) + "px")     
				.style("top", (d3.event.pageY - 28) + "px");
		}
		function mouseOut(d){
			d3	.select("#label")
				.transition()
				.duration(500)
				.style("opacity", 0);      
		}
```
After about a dozen articles and even more videos, my plan was clear. I need a function for selecting the id of the particular element and changing its color on click. Also, I wanted to return its initial color by clicking on the element the second time. The final working function was looking like this: 
```
	function mouseClick(d){
			const state = d3	
				.select(id)
				.selectAll(".state." + d.id) 
			if (state[0][0].style.fill === ""){
				state.style("fill",function(d){ return "#066406"})
				createUserState(d.n);
			} else {
				state.style("fill",function(d){ return ""})
				removeUserState(d.n);
			}
		}
```
I think this is the most important function in my project that helped me to realize how little I know and how much I need to learn. I am happy that the JavaScript project is complete, going to take a break and I will get back to it again… My next goal is to draw a map myself!
