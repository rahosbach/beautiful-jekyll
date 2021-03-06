---
layout: post
title: Creating an Interactive Bar Chart of U.S. Covid-19 Confirmed Cases and Deaths by State and County
tags: [bar chart, Covid-19, d3.js]
---

### Introduction
For [my last post](https://rahosbach.github.io/2020-04-02-d3Covid19Choropleth/) I developed a time-lapse choropleth map of Covid-19 confirmed cases and Covid-19-related deaths for the U.S. using [data from the New York Times](https://github.com/nytimes/covid-19-data) (NYT).  While the choropleth map showed the spread of the virus across the U.S. (at least in terms of confirmed cases and deaths), it was difficult for a viewer to obtain detailed data from some smaller counties, and the map did not provide overall state-level counts.  Therefore, for this post I wanted to make an interactive bar chart that also showed confirmed cases and deaths from Covid-19 over time, but that provided more detail at both the state and county levels.

This post relies on the same data that were used to develop the choropleth map: the NYT [**Coronavirus (Covid-19) Data in the United States**](https://github.com/nytimes/covid-19-data) GitHub repository.  This repository contains cumulative Covid-19 confirmed cases and Covid-19-related deaths by state and county, dating back to the first reported Covid-19 confirmed case in Washington State on January 21, 2020.  Furthermore, the NYT is updating this repository frequently as new data become available; therefore, this interactive bar chart (and the choropleth map from my previous post) stay up-to-date with the NYT data.

The NYT GitHub repository's README provides detail on the data files, their methodology, and key definitions (in addition to other information).  In addition, the NYT has been tracking the spread of Covid-19 in the U.S.  You can view their U.S. tracking page [here](https://www.nytimes.com/interactive/2020/us/coronavirus-us-cases.html).

### Using the Bar Chart
Before setting you loose on the interactive bar chart, some details are necessary:
1. The radio buttons at the top allow you to toggle between confirmed cases of Covid-19 and Covid-19-related deaths.  The bar chart and the county-level data (see #3 below) will correspond to your selection.
2. The slider is set to the latest date for which data are available by default.  You can see the cumulative counts for earlier dates by dragging left, and later dates by dragging right.
3. Hovering your cursor over one of the state bars or state names will do two things:
    1. Show a tooltip displaying the cumulative count of confirmed cases or deaths (depending on your radio button selection) and what fraction of the national total the state represented on the selected date.
    2. Reveal a list of the counties in the state (to the right of the bar chart) containing the count by county and what fraction of the state total the county represented on the selected date.
4. **If the "Deaths" radio button is selected**: The first reported Covid-19-related death did not occur until February 29, 2020 (1 reported death in Washington State).  However, the slider bar still allows you to go all the way back to January 21, 2020 (the date of the first reported Covid-19 case in the U.S.), and the bar chart will continue to list any states that had _cases_ on the selected date, even if a given state had no covid-19-related deaths by that date.  This allows you to keep track of which states had confirmed cases, even if no deaths had been reported.  As an example, on February 29, 2020 Washington State was the only state with a reported Covid-19-related death; therefore, a bar appears for Washington indicating 1 Covid-19-related death.  Moreover, by February 29th nine other states had Covid-19 confirmed cases: AZ, CA, IL, MA, NE, OR, TX, UT, and WI.  These other nine states are listed under Washington in the bar plot, but they have no bars showing because there are no Covid-19 related deaths for those states as of February 29th.  Similarly, the county-level data will list counties with confirmed cases but no Covid-19-related deaths by the given date.

### The Chart
<style>
    .axis .domain {
      font-family: Nunito, sans-serif;
      font-size: 0.8em;
      stroke: none;
    }

    .x line {
      stroke: white;
    }

    .ticks {
      font-family: Nunito, sans-serif;
      font-size: 0.7em;
    }
    
    .tick {
      cursor: default;
    }

    .track,
    .track-inset,
    .track-overlay {
      stroke-linecap: round;
    }

    .track {
      stroke: #000;
      stroke-opacity: 0.3;
      stroke-width: 10px;
    }

    .track-inset {
      stroke: #ddd;
      stroke-width: 8px;
    }

    .track-overlay {
      pointer-events: stroke;
      stroke-width: 50px;
      cursor: crosshair;
    }

    .handle {
      fill: #fff;
      stroke: #000;
      stroke-opacity: 0.5;
      stroke-width: 1.25px;
    }
    
    .label {
      font-family: Nunito, sans-serif;
      font-size: 0.7em;
    }

    .tooltip {
      position: fixed;
      opacity: 0;
      pointer-events: none;
      background: #e0e0e0;
      max-width: 200px;
    }

    .tip-header {
      font-family: Nunito, sans-serif;
      border-bottom: 2px solid white;
      padding: 0.9rem 1rem;
    }

    .tip-body {
      font-family: Nunito, sans-serif;
      padding: 0.2rem 0;
    }

    .ttstate {
      font-family: Nunito, sans-serif;
      font-size: 0.9em;
      color: #555;
      margin: 0;
      font-weight: 600;
    }

    .ttbody {
      font-family: Nunito, sans-serif;
      font-size: 0.8em;
      color: #555;
      margin: 0;
      font-weight: 300;
    }

    .tip-info {
      font-family: Nunito, sans-serif;
      margin: 0;
      padding: 0.3rem 1em;
      font-size: 0.8em;
    }

    .tip-info:nth-of-type(odd) {
      background: #eee;
    }

    .flex-container {
      display: flex;
      flex-direction: row;
    }

    .selected-container {
      flex-direction: column;
      margin-left: 1rem;
    }

    .selected-header {
      font-family: Nunito, sans-serif;
      background: #f0f0f0;
      color: #777;
      padding: 0.5rem 2rem;
      text-align: left;
      font-weight: 600;
    }

    .selected-body {
      font-family: Nunito, sans-serif;
      overflow: auto;
      font-size: 0.6em;
      cursor: default;
      line-height: 0.4em;
    }
    
    .selected-element {
      margin-top: 5px;
      margin-bottom: 0px;
    }

    .selected-title {
      font-family: Nunito, sans-serif;
      font-weight: 600;
    }
    
    .controls {
      margin-left:50px;
    }
    
    .controls label {
      font-family: Nunito, sans-serif;
      font-size: 0.8em;
    }
</style>
<link href="https://fonts.googleapis.com/css2?family=Nunito:wght@300;600&display=swap" rel="stylesheet">
<script src="https://d3js.org/d3.v5.min.js"></script>
<script src="https://d3js.org/d3-array.v2.min.js"></script>
<div class="controls">
    <input type="radio" class="myRadio" id="cases" value="cases" name="metric" checked>
    <label for="cases">Confirmed Cases</label><br>
    <input type="radio" class="myRadio" id="deaths" value="deaths" name="metric">
    <label for="deaths">Deaths</label>
</div>
<div class="slider-container"></div>
<div class="flex-container">
    <div class="bar-chart-container"></div>
    <div class="selected-container">
        <div class="selected-header">Hover over a bar or state name...</div>
        <div class="selected-body"></div>
</div>
<div class="tooltip">
    <div class="tip-header">
        <div class="ttstate"></div>
        <div class="ttbody"></div>
    </div>
    <div class="tip-body"></div>
</div>
<script>
    // Drawing utilities.
    function formatTicks(d) {
        if (d == 0) {
            return d3.format("d")(d);
        } else if (d < 1) {
            return d3.format("0.1f")(d);
        } else {
            return d3.format(".2s")(d)
                .replace("k", " thou")
                .replace("M", " mil")
                .replace("G", " bil")
                .replace("T", " tril");
        };
    };

    // Type conversion.
    function convert_types(d) {
        const date_formatted = parseDate(d.date);
        return {
            date: date_formatted,
            date_label: formatDateForLabel(date_formatted),
            county: d.county,
            state: d.state,
            county_state: d.county + ", " + d.state,
            fips: d.fips,
            cases: +d.cases,
            deaths: +d.deaths
        };
    };

    // Data utilities.
    const parseDate = string => d3.timeParse("%Y-%m-%d")(string);
    const formatDateForLabel = date => d3.timeFormat("%b. %d")(date);

    // Data preparation.
    function filterData(data, dateref) {
        return data.filter(d => {
            return d.date.getTime() === dateref.getTime();
        });
    };

    function prepareBarChartData(data, metric) {
        const dataMap = d3.rollup(
            data,
            v => d3.sum(v, leaf => leaf[metric]),
            d => d.state
        );
        if (metric === "cases") {
            var dataArray = Array.from(dataMap, d => ({
                state: d[0],
                cases: d[1]
            }));
        } else {
            var dataArray = Array.from(dataMap, d => ({
                state: d[0],
                deaths: d[1]
            }));
        };
        return dataArray;
    };

    // Tooltip handler.
    function mouseover(element, dataForDate, metric) {

        // Get bar data.
        var barData = d3.select(element).data()[0];

        if (barData.length) {
            // For hovering over a state name (not the bar), a string
            // will be returned that has a length associated with it.
            const preState = barData;
            const preVal = d3.sum(dataForDate.filter(d => d.state == preState), d=> d[metric]);
            if (metric == "cases") {
                barData = {"state": preState, "cases": preVal};
            } else {
                barData = {"state": preState, "deaths": preVal};
            };
        };

        var nationalTotal = d3.sum(dataForDate, d => d[metric]);

        // Get metric.
        if (metric == "cases") {
            var bodyData = [
                ['Confirmed Cases', d3.format(",")(barData[metric])],
                ['U.S. Fraction', d3.format(".1%")(barData[metric] / nationalTotal)]
            ];
        } else {
            var bodyData = [
                ['Deaths', d3.format(",")(barData[metric])],
                ['U.S. Fraction', d3.format(".1%")(barData[metric] / nationalTotal)]
            ];
        };

        // Filter date data down to selected state and sort
        var stateData = dataForDate
            .filter(d => {
                return d.state === barData.state;
            })
            .sort((a, b) => d3.descending(a[metric], b[metric]) || d3.ascending(a.county, b.county));

        // Build tooltip.
        const tip = d3.select(".tooltip");

        tip
            .style("left", d3.event.clientX + 15 + "px")
            .style("top", d3.event.clientY + "px")
            .transition()
            .style("opacity", 0.98);

        tip.select(".ttstate")
            .html(barData.state);
        tip.select(".ttbody")
            .html(d3.select('.label')._groups[0][0].textContent + ", 2020");

        d3.select(".tip-body")
            .selectAll("p")
            .data(bodyData)
            .join("p")
            .attr("class", "tip-info")
            .html(d => d[0] + ": " + d[1]);

        const stateTotal = d3.sum(stateData, d => d[metric]);

        // Update selected title.
        d3.select('.selected-header')._groups[0][0].textContent = "County Data for " + barData.state;
        // Update selected elements.
        d3.select(".selected-body")
            .selectAll(".selected-element")
            .data(stateData, d => d.county)
            .join(
                enter => enter
                .append("p")
                .attr("class", "selected-element")
                .html(d => `<span class="selected-title">${d.county} County</span> | ${d3.format(",")(d[metric])} ${((d[metric] == 1) ? metric.slice(0,-1) : metric)} (${d3.format(".1%")(d[metric]/stateTotal || 0)} of state total)`),
                update => update
                .html(d => `<span class="selected-title">${d.county} County</span> | ${d3.format(",")(d[metric])} ${((d[metric] == 1) ? metric.slice(0,-1) : metric)} (${d3.format(".1%")(d[metric]/stateTotal || 0)} of state total)`),
                exit => exit.remove()
            );
    };

    function mousemove() {
        d3.select(".tooltip")
            .style("left", d3.event.clientX + 15 + "px")
            .style("top", d3.event.clientY + "px");
    };

    function mouseout() {
        d3.select(".tooltip")
            .transition()
            .style("opacity", 0);
    };

    // Main function.
    function ready(data) {
        const dateMin = d3.min(data, d => d.date),
            dateMax = d3.max(data, d => d.date);

        let metric = 'cases';

        function click() {
            const newDate = d3.timeParse("%b. %d")(d3.select('.label')._groups[0][0].textContent);
            newDate.setYear(2020);

            var choices = [];
            d3.selectAll(".myRadio").each(function(d) {
                cb = d3.select(this);
                if (cb.property("checked")) {
                    choices.push(cb.property("value"));
                };
            });
            metric = choices[0];

            update(newDate, data, metric);
        };

        const dataClean = filterData(data, dateMax);
        const barChartData = prepareBarChartData(dataClean, metric).sort((a, b) =>
            d3.descending(a[metric], b[metric]) || d3.ascending(a.state, b.state)
        );

        // Margin convention.
        const margin = {
            top: 35,
            right: 40,
            bottom: 40,
            left: 120
        };
        const width = 400 - margin.left - margin.right;
        const height = 800 - margin.top - margin.bottom;

        // Scales.
        const xScale = d3.scaleLinear()
            .range([0, width]);

        const yScale = d3.scaleBand()
            .paddingInner(0.25);

        const dateScale = d3.scaleTime()
            .domain([dateMin, dateMax])
            .range([0, width])
            .clamp(true);

        // Draw slider.
        var slider = d3.select(".slider-container")
            .append("svg")
            .attr("width", width + margin.left + margin.right)
            .attr("height", 50)
            .append("g")
            .attr("class", "slider")
            .attr("transform", "translate(" + margin.left + "," + 50 / 1.7 + ")");

        slider.append("line")
            .attr("class", "track")
            .attr("x1", dateScale.range()[0])
            .attr("x2", dateScale.range()[1])
            .select(function() {
                return this.parentNode.appendChild(this.cloneNode(true));
            })
            .attr("class", "track-inset")
            .select(function() {
                return this.parentNode.appendChild(this.cloneNode(true));
            })
            .attr("class", "track-overlay")
            .call(d3.drag()
                .on("start.interrupt", function() {
                    slider.interrupt();
                })
                .on("start drag", function() {
                    update(d3.timeDay(dateScale.invert(d3.event.x)), data, metric);
                }));

        slider.insert("g", ".track-overlay")
            .attr("class", "ticks")
            .attr("transform", "translate(0," + 18 + ")")
            .selectAll("text")
            .data(dateScale.ticks(5))
            .enter()
            .append("text")
            .attr("x", dateScale)
            .attr("text-anchor", "middle")
            .text(function(d) {
                return formatDateForLabel(d);
            });

        var handle = slider
            .insert("circle", ".track-overlay")
            .attr("class", "handle")
            .attr("r", 9);

        var label = slider.append("text")
            .attr("class", "label")
            .attr("text-anchor", "middle")
            .attr("font-size", "0.7em")
            .text(formatDateForLabel(dateMax))
            .attr("transform", "translate(0," + (-15) + ")")

        // Draw base.
        const svg = d3.select(".bar-chart-container")
            .append("svg")
            .attr("width", width + margin.left + margin.right)
            .attr("height", height + margin.top + margin.bottom)
            .append("g")
            .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

        // Draw bars.
        const bars = svg
            .append("g")
            .attr("class", "bars");

        // General update pattern.
        function update(date, data, metric) {

            // Filter and prepare data.
            const dataClean = filterData(data, date);
            const barChartData = prepareBarChartData(dataClean, metric).sort((a, b) =>
                d3.descending(a[metric], b[metric]) || d3.ascending(a.state, b.state)
            );

            // Update scales.
            const xMax = d3.max(barChartData, d => d[metric]);
            const numStates = d3.count(barChartData, d => d[metric]);
            xScale.domain([0, d3.max([xMax, 5])]);
            yScale
                .domain(barChartData.map(d => d.state))
                .rangeRound([0, height / 55 * numStates]) // 55 "states" in data set

            // Setup transition
            const dur = 1000;
            const t = d3.transition().duration(dur);

            // Remove any county data for a previously-selected state
            d3.select('.selected-header')._groups[0][0].textContent = "Hover over a bar or state name...";
            d3.select(".selected-body")
                .selectAll(".selected-element")
                .transition(t)
                .remove();

            // Setup color
            var color = (metric === "cases") ? "silver" : "firebrick"
            // Update bars.
            bars
                .selectAll(".bar")
                .data(barChartData, d => d.state)
                .join(
                    enter => {
                        enter
                            .append("rect")
                            .attr("class", "bar")
                            .attr("y", d => yScale(d.state))
                            .attr("height", yScale.bandwidth())
                            .style("fill", "white")
                            .transition(t)
                            .delay((d, i) => i * 20)
                            .style("fill", color)
                            .attr("width", d => xScale(d[metric]))
                    },

                    update => update
                    .transition(t)
                    .delay((d, i) => i * 20)
                    .style("fill", color)
                    .attr("y", d => yScale(d.state))
                    .attr("width", d => xScale(d[metric])),

                    exit => exit
                    .transition()
                    .duration(dur / 2)
                    .style("fill-opacity", 0)
                    .remove()
                )

            // Update axes.
            xAxisDraw.transition(t).call(xAxis.scale(xScale));
            yAxisDraw.transition(t).call(yAxis.scale(yScale));

            // Update slider.
            handle.attr("cx", dateScale(date));
            label
                .attr("x", dateScale(date))
                .text(formatDateForLabel(date));

            // Add tooltip and county data.
            d3.selectAll(".bar")
                .on("mouseover", function() {
                    mouseover(this, dataClean, metric);
                })
                .on("mousemove", mousemove)
                .on("mouseout", mouseout);
            d3.select(".y.axis").selectAll(".tick")
                .on("mouseover", function() {
                    mouseover(this, dataClean, metric);
                })
                .on("mousemove", mousemove)
                .on("mouseout", mouseout);
        };

        // Draw axes.
        const xAxis = d3
            .axisTop(xScale)
            .tickFormat(formatTicks)
            .ticks(4)
            .tickSizeOuter(0)
            .tickSizeInner(-height);

        const xAxisDraw = svg
            .append("g")
            .attr("class", "x axis");

        const yAxis = d3.axisLeft(yScale).tickSize(0);

        const yAxisDraw = svg
            .append("g")
            .attr("class", "y axis");

        // Initial render.
        update(dateMax, data, "cases");

        // Listen to click events.
        d3.selectAll(".myRadio")
            .on("click", click);

        // Prep selected elements' handler.
        d3.select(".selected-container")
            .attr("width", width + margin.left + margin.right)
            .attr("height", height + margin.top + margin.bottom);
    };

    // Load data.
    d3.csv("https://raw.githubusercontent.com/nytimes/covid-19-data/master/us-counties.csv", convert_types)
        .then(res => {
            ready(res);
        });
</script>
