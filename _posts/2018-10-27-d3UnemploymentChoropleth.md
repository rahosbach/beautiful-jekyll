---
layout: post
title: Experimenting with d3.js to Create an Animated Choropleth Map of U.S. Unemployment by County
tags: [choropleth, d3.js, map, unemployment]
---

<svg width="960" height="600"></svg>
<style>
div.tooltip {   
 	position: absolute;           
	text-align: center; 
	vertical-align: middle;          
	width: auto;                 
	height: auto;                 
	padding: 2px;             
	font: 12px sans-serif;    
	color: white;    
	background: gray;   
	border: 0px;      
	border-radius: 8px;           
	pointer-events: none;         
}

.counties :hover {
  stroke: black;
  stroke-width: 2px;
}

.county-borders {
  fill: none;
  stroke: #fff;
  stroke-width: 0.5px;
  stroke-linejoin: round;
  stroke-linecap: round;
  pointer-events: none;
}

.year.label {
  font: 500 85px "Helvetica Neue";
  fill: gray;
}

.overlay {
  fill: none;
  pointer-events: all;
  cursor: ew-resize;
}
</style>
<script src="https://d3js.org/d3.v4.min.js"></script>
<script src="https://d3js.org/d3-scale-chromatic.v1.min.js"></script>
<script src="https://d3js.org/topojson.v1.min.js"></script>
<script src="https://d3js.org/queue.v1.min.js"></script>
<script>
  var svg = d3.select("svg");
  var path = d3.geoPath();
  var format = d3.format("");
  var height = 600;
  var width = 960;

  var colorScheme = d3.schemeReds[9];
  colorScheme.unshift("#eee");

  var color = d3.scaleQuantize()
    .domain([0, 20])
    .range(colorScheme);
  var x = d3.scaleLinear()
    .domain(d3.extent(color.domain()))
    .rangeRound([600,860]);
  var g = svg.append("g")
    .attr("transform", "translate(0,40)");

  g.selectAll("rect")
    .data(color.range().map(function(d){ return color.invertExtent(d); }))
    .enter()
    .append("rect")
      .attr("height", 8)
      .attr("x", function(d){ return x(d[0]); })
      .attr("width", function(d){ return x(d[1]) - x(d[0]); })
      .attr("fill", function(d){ return color(d[0]); });

  g.append("text")
    .attr("class", "caption")
    .attr("x", x.range()[0])
    .attr("y", -6)
    .attr("fill", "#000")
    .attr("text-anchor", "start")
    .attr("font-weight", "bold")
    .text("Unemployment Rate (%)");

  g.call(d3.axisBottom(x)
  	.tickSize(13)
  	.tickFormat(format)
  	.tickValues(color.range().slice(1).map(function(d){ return color.invertExtent(d)[0]; })))
	.select(".domain")
  	.remove();

  var div = d3.select("body")
    .append("div")
    .attr("class", "tooltip")
    .style("opacity", 0);

  // Add the year label; the value is set on transition.
  var label = svg.append("text")
    .attr("class", "year label")
    .attr("text-anchor", "end")
    .attr("y", height - 125)
    .attr("x", width)
    .text(1990);

  queue()
    .defer(d3.json, "https://d3js.org/us-10m.v1.json")
    .defer(d3.csv, "https://raw.githubusercontent.com/rahosbach/rahosbach.github.io/master/_data/unemployment_by_county_year.csv")
    .await(ready);
  
  function ready(error, us, unemployment) {
    if (error) throw error;

  		// Initialize data to 1990
  		var currentYear = 1990;

  		// Add an overlay for the year label.
  		var box = label.node().getBBox();
  
  		var overlay = svg.append("rect")
    		.attr("class", "overlay")
    		.attr("x", box.x)
    		.attr("y", box.y)
    		.attr("width", box.width)
    		.attr("height", box.height)
    		.on("mouseover", enableInteraction);
  
	  	// Start a transition that interpolates the data based on year.
	  	svg.transition()
	      .duration(25000)
	      .ease(d3.easeLinear)
	      .tween("year", tweenYear)
	      //.each();

      counties = svg.append("g")
        .attr("class", "counties")
		    .selectAll("path")
		    .data(topojson.feature(us, us.objects.counties).features)
		    .enter()
		    .append("path")
		    .attr("d", path)
		    .call(style,currentYear)

      function style(counties, year){
        newunemployment = interpolateData(year);

			var rateById = {};
			var nameById = {};

	  	newunemployment.forEach(function(d) {
	  		var newcode = '';
        if (d.code.length < 5) {
          newcode = '0' + d.code;
          d.code = newcode;
        } 
        rateById[d.code] = +d.rate;
        nameById[d.code] = d.name;
      });
      
			counties.style("fill", function(d) { return color(rateById[d.id]); })
				.on("mouseover", function(d) {      
			    	div.transition()        
			        .duration(200)      
			        .style("opacity", .9);      
			      div.html(nameById[d.id] + ' in ' + Math.round(currentYear) +': <br><strong>' + rateById[d.id] + '%</strong>')
			        .style("left", (d3.event.pageX) + "px")     
			        .style("top", (d3.event.pageY - 28) + "px");})   
			   // fade out tooltip on mouse out               
			   .on("mouseout", function(d) {       
			      div.transition()        
			       .duration(500)      
			       .style("opacity", 0);});
		}

		svg.append("path")
		  .datum(topojson.mesh(us, us.objects.states, (a, b) => a !== b))
		  .attr("fill", "none")
		  .attr("stroke", "white")
		  .attr("stroke-linejoin", "round")
		  .attr("d", path);

		// After the transition finishes, you can mouseover to change the year.
	  function enableInteraction() {
      var yearScale = d3.scaleLinear()
        .domain([1990, 2017])
        .range([box.x + 10, box.x + box.width - 10])
        .clamp(true);

      // Cancel the current transition, if any.
      svg.transition().duration(0);

      overlay
        .on("mouseover", mouseover)
        .on("mouseout", mouseout)
        .on("mousemove", mousemove)
        .on("touchmove", mousemove);

      function mouseover() { label.classed("active", true); }
      function mouseout() { label.classed("active", false); }
      function mousemove() { displayYear(yearScale.invert(d3.mouse(this)[0])); }
    }

	  // Tweens the entire chart by first tweening the year, and then the data.
	  // For the interpolated data, the dots and label are redrawn.
	  function tweenYear() {
	    var year = d3.interpolateNumber(1990, 2017);
	    return function(t) { displayYear(year(t)); };
	  }

    // Updates the display to show the specified year.
    function displayYear(year) {
      currentYear = year;
      counties.call(style,year)
      label.text(Math.round(year));
    }

    // Interpolates the dataset for the given (fractional) year.
    function interpolateData(year) {
      return unemployment.filter(function(row) {
      return row['year'] == Math.round(year);
    });
	    }
};
</script>
### Using the Map
1. The map will animate automatically to show unemployment rates by county from 1990 to 2017.
2. At any point, you can hover over the year label (on the right side) to stop the animation.  Moving your cursor horizontally over the year label will automatically update the year that is shown on the choropleth (move left to decrease the year, right to increase the year).
3. Hover over a county to see a tooltip providing the county name and the unemployment rate in that year.

### Introduction
I've wanted to learn [d3.js](https://d3js.org/) for quite a while now, and I was recently able to take Emma Saunders' LinkedIn Learning course entitled, ["D3.js Essential Training for Data Scientists."](https://www.linkedin.com/learning/d3-js-essential-training-for-data-scientists).  A wealth of material was covered in the course, and it was the best resource I've found thus far to try to learn the intricacies of d3.

After completing the course, and therefore having at least some inkling of how to generate plots in d3, I wanted to create my own d3 plot.  [Choropleth maps](https://en.wikipedia.org/wiki/Choropleth_map) were not covered in the course, but I thought it would be an interesting challenge to develop a choropleth map that showed annual average unemployment rate by U.S. county over a range of years.  I knew there were some examples I could use to get me started, but little did I know the challenge this would be.

### Data and Useful Examples
There were a number of example d3 plots I used to inform my choropleth map, but a particular [choropleth map](https://beta.observablehq.com/@mbostock/d3-choropleth) created by [Mike Bostock](https://bost.ocks.org/mike/) (the creator of d3.js) provided a link to the [U.S. Bureau of Labor Statistics webpage](https://www.bls.gov/lau/#tables) from which I could download annual average unemployment rates by U.S. county for 1990 through 2017.

Thanks to the great community of d3.js developers out there, a plethora of great d3 plot examples can be found online.  Here is a listing of the key examples I used to help inform the coding of my d3 choropleth map:
- [jgu's The Wealth & Health of Nations](http://bl.ocks.org/michellechandra/0b2ce4923dc9b5809922) helped with understanding how to animate my choropleth map by year.
- [Mike Bostock's D3 Choropleth](https://beta.observablehq.com/@mbostock/d3-choropleth) provided my first understanding of how to generate a county-level choropleth map in d3. 
- [Mike Bostock's Threshold Choropleth](https://bl.ocks.org/mbostock/3306362) provided a threshold legend as well as a more compact way to bring in and incorporate [my data](https://raw.githubusercontent.com/rahosbach/rahosbach.github.io/master/_data/unemployment_by_county_year.csv).
- [Michelle Chandra's Basic US State Map - D3](http://bl.ocks.org/michellechandra/0b2ce4923dc9b5809922) provided styling and animation for the tooltips in my map.
