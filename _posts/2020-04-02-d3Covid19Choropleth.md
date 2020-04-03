---
layout: post
title: Creating an Animated County-Level Choropleth Map of U.S. Covid-19 Confirmed Cases and Deaths
tags: [choropleth, Covid-19, d3.js, map]
---

<input type="radio" class="myRadio" id="cases" name="metric" value="cases" checked>
<label for="cases">Confirmed Cases</label><br>
<input type="radio" class="myRadio" id="deaths" name="metric" value="deaths">
<label for="deaths">Deaths</label><br>
<svg width="960" height="600" style="background-color:lightgray"></svg>
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
  background: #001B3A;   
  border: 0px;      
  border-radius: 8px;           
  pointer-events: none;         
}

.states {
  stroke: gray;
}

.counties {
  stroke: white;
  fill: white;
}

.counties :hover {
  stroke: black;
  stroke-width: 2px;
}

.county-borders {
  fill: none;
  stroke: black;
  stroke-width: 0.5px;
  stroke-linejoin: round;
  stroke-linecap: round;
  pointer-events: none;
}

.date.label {
  font: 500 58px "Helvetica Neue";
  fill: black;
}

.source.label {
  font: 500 14px "Helvetica Neue";
  fill: black;
  font-weight: bold;
}

.source.link {
  font: 500 14px "Helvetica Neue";
}

a {
  fill: navy;
  background-color: transparent;
  text-decoration: underline;
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
<script src="https://cdnjs.cloudflare.com/ajax/libs/d3-legend/2.25.6/d3-legend.min.js"></script>
<script>
var svg = d3.select("svg");
var path = d3.geoPath();
var format = d3.format(",.0f");
var height = 600;
var width = 960;

var colorSchemeReds = d3.schemeReds[9].slice(4, 9);
var colorSchemeBlues = d3.schemeBlues[9].slice(4, 9);

var parseDate = d3.timeParse("%Y-%m-%d");
var formatDate = d3.timeFormat("%b. %d");
var formatDateOrig = d3.timeFormat("%Y-%m-%d");

// Add the date label; the value is set on transition.
var label = svg.append("text")
    .attr("class", "date label")
    .attr("text-anchor", "end")
    .attr("y", height - 125)
    .attr("x", width)
    .text("Jan. 21");

// Add source label
var source = svg.append("text")
    .attr("class", "source label")
    .attr("text-anchor", "start")
    .attr("y", height - 10)
    .attr("x", width - 475)
    .text("Source:");

// Add source label
var source_link = svg.append("text")
    .attr("class", "source link")
    .attr("text-anchor", "start")
    .attr("y", height - 10)
    .attr("x", width - 425)
    .html("<a href=https://github.com/nytimes/covid-19-data target=_blank color=blue>The New York Times</a>");


queue()
    .defer(d3.json, "https://d3js.org/us-10m.v1.json")
    .defer(d3.csv, "https://raw.githubusercontent.com/nytimes/covid-19-data/master/us-counties.csv")
    .await(ready);

function ready(error, us, covid) {
    if (error) throw error;
    var min_date = d3.min(covid, function(d) {
        return parseDate(d.date);
    });
    var max_date = d3.max(covid, function(d) {
        return parseDate(d.date);
    });

    covid.forEach(function(d) {
        if (d['county'] == "New York City") {
            return d['code'] = "36061";
        } else {
            return d['code'] = d.fips;
        }
    });

    update(0);

    function update(speed) {

        var choices = [];
        d3.selectAll(".myRadio").each(function(d) {
            cb = d3.select(this);
            if (cb.property("checked")) {
                choices.push(cb.property("value"));
            }
        });
        metric = choices[0];
        covid_filtered = covid.filter(function(row) {
            return +row[metric] > 0;
        });

        var div = d3.select("body")
            .append("div")
            .attr("class", "tooltip")
            .style("opacity", 0);

        if (metric == "cases") {
            var metric_label = " Confirmed Cases";
        } else {
            var metric_label = "-Related Deaths"
        }

        if (metric == "cases") {
            var color = d3.scaleThreshold()
                .domain([10, 100, 1000, 10000, 100000])
                .range(colorSchemeBlues);
        } else {
            var color = d3.scaleThreshold()
                .domain([10, 50, 100, 1000, 10000])
                .range(colorSchemeReds);
        }

        var x = d3.scaleLinear()
            .domain(d3.extent(color.domain()))
            .rangeRound([600, 860]);
        var g = svg.append("g")
            .attr("transform", "translate(0,40)");

        d3.select(".legendQuant").remove();

        svg.append("g")
            .attr("class", "legendQuant")
            .attr("transform", "translate(700,30)");

        var legend = d3.legendColor()
            .scale(color)
            .orient("vertical")
            .shapeWidth(50)
            .labels(d3.legendHelpers.thresholdLabels)
            .labelFormat(d3.format(","));

        d3.select(".caption").remove();

        g.append("text")
            .attr("class", "caption")
            .attr("x", width/2)
            .attr("y", -5)
            .style("font-size", "34px")
            .attr("fill", "#000")
            .attr("text-anchor", "middle")
            .attr("font-weight", "bold")
            .text("Covid-19" + metric_label);
        svg.select(".legendQuant")
            .style("font-size", "12px")
            .style("font-weight", "bold")
            .call(legend);

        // Initialize data to start date
        var currentDate = min_date;

        // Add an overlay for the date label.
        var box = label.node().getBBox();

        var overlay = svg.append("rect")
            .attr("class", "overlay")
            .attr("x", box.x)
            .attr("y", box.y)
            .attr("width", box.width)
            .attr("height", box.height)
            .on("mouseover", enableInteraction);

        // Start a transition that interpolates the data based on date.
        svg.transition()
            .duration(12000)
            .ease(d3.easeLinear)
            .tween("date", tweenDate)
        //.each();

        counties = svg.append("g")
            .attr("class", "counties")
            .selectAll("path")
            .data(topojson.feature(us, us.objects.counties).features)
            .enter()
            .append("path")
            .attr("d", path)
            .call(countyfill, currentDate)

        function countyfill(counties, date) {
            newcovid = interpolateData(date);

            var rateById = {};
            var nameById = {};

            newcovid.forEach(function(d) {
                var newcode = '';
                if (d.code.length < 5) {
                    newcode = '0' + d.code;
                    d.code = newcode;
                }
                rateById[d.code] = +d[metric];
                nameById[d.code] = d.county + " County, " + d.state;
            });

            counties.style("fill", function(d) {
                    return color(rateById[d.id]);
                })
                .on("mouseover", function(d) {
                    if (rateById[d.id] > 0) {
                        div.transition()
                            .duration(200)
                            .style("opacity", .9);
                        div.html(nameById[d.id] + ' on ' + formatDate(currentDate) + ': <br><strong>' + format(rateById[d.id]) + ' ' + metric.slice(0, -1) + '(s) </strong>')
                            .style("left", d3.event.pageX + "px")
                            .style("top", (d3.event.pageY - 100) + "px");
                    } else {
                        return null;
                    }
                })
                // fade out tooltip on mouse out               
                .on("mouseout", function(d) {
                    div.transition()
                        .duration(500)
                        .style("opacity", 0);
                });
        }

        svg.append("path")
            .datum(topojson.mesh(us, us.objects.states, (a, b) => a !== b))
            .attr("fill", "none")
            .attr("stroke", "gray")
            .attr("stroke-linejoin", "round")
            .attr("d", path);

        // After the transition finishes, you can mouseover to change the date.
        function enableInteraction() {
            var dateScale = d3.scaleTime()
                .domain([min_date, max_date])
                .range([box.x + 10, box.x + box.width - 10])
                .clamp(true);

            // Cancel the current transition, if any.
            svg.transition().duration(0);

            overlay
                .on("mouseover", mouseover)
                .on("mouseout", mouseout)
                .on("mousemove", mousemove)
                .on("touchmove", mousemove);

            function mouseover() {
                label.classed("active", true);
            }

            function mouseout() {
                label.classed("active", false);
            }

            function mousemove() {
                displayDate(dateScale.invert(d3.mouse(this)[0]));
            }
        }

        // Tweens the entire chart by first tweening the date, and then the data.
        // For the interpolated data, the dots and label are redrawn.01
        function tweenDate() {
            var date = d3.interpolateDate(min_date, max_date);
            return function(t) {
                displayDate(date(t));
            };
        }

        // Updates the display to show the specified date.
        function displayDate(date) {
            currentDate = date;
            counties.call(countyfill, date)
            label.text(formatDate(date));
        }

        // Interpolates the dataset for the given (fractional) date.
        function interpolateData(date) {
            return covid_filtered.filter(function(row) {
                return row['date'] == formatDateOrig(date);
            });
        }

        var radiobox = d3.selectAll(".myRadio")
            .on("click", function() {
                update(0)
            });
    };
};
</script>

### Using the Map
1. The map will animate automatically to show cumulative Covid-19 confirmed cases or Covid-19-related deaths (depending on what is selected) by county.
2. At any point, you can hover over the date label (in the bottom-right corner) to stop the animation.  Moving your cursor horizontally over the date label will automatically update the date that is shown on the choropleth (move left to decrease the date, right to increase the date).
3. Hover over a county to see a tooltip providing the county name and the cumulative confirmed cases or deaths by that date. (A tooltip will not appear for any counties that have yet to record a confirmed Covid-19 case or Covid-19-related death.)
4. Clicking the "Confirmed Cases" or "Deaths" radio buttons above the map will automatically restart the animation (from January 21, 2020) for the selected metric (_i.e._, confirmed cases or deaths).

### Introduction
By now, there are thousands (if not millions) of visualizations interpreting Covid-19 data as they become available.  However, I have yet to see a time-lapse choropleth map of Covid-19 confirmed cases and Covid-19-related deaths for the U.S.  On March 26, 2020, the New York Times (NYT) released their [**Coronavirus (Covid-19) Data in the United States**](https://github.com/nytimes/covid-19-data) GitHub repository to the public.  This repository contains cumulative Covid-19 confirmed cases and Covid-19-related deaths by state and county, dating back to the first reported Covid-19 confirmed case in Washington State on January 21, 2020.  Furthermore, the NYT is updating this repository frequently as new data become available.

The NYT GitHub repository's README provides detail on the data files, their methodology, and key definitions (in addition to other information).  In addition, the NYT has been tracking the spread of Covid-19 in the U.S.  You can view their U.S. tracking page [here](https://www.nytimes.com/interactive/2020/us/coronavirus-us-cases.html).

The purpose of this choropleth map is not to show precise data (though the tooltips provide that).  Instead, this map is intended to provide a sense of how fast Covid-19 is blanketing the U.S.
