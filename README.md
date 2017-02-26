# D3-Visualization-Tutorial
This tutorial is based on my solutions to three problems assigned as part of the course CSCI-E63 Big Data Analytics at Harvard University in Spring 2015.

##  Dependencies
D3 (or Data-Driven Documents)  is a Javascript library for in-browser visualization and document manipulation based on data. For this project we need d3.js or d3.min.js both of which can be obtained from http://d3js.org/ . 
For tooltips to be used with D3 visualizations we are using d3.tip.v0.6.3.js available from http://labratrevenge.com/d3-tip/ .
For convenience, they are provided in the d3 and d3-tip subdirectories.

## Input Data
Data regarding worldwide spending on healthcare compiled by Harvard University from various sources including The Guardian and The World Bank is provided in the spreadsheet 'Health spending by country.xlsx' which is included in the Data subdirectory.

For problems 1 and 2 I extracted the data from the given Excel spreadsheet into prob1_spending.csv (also included in the Data subdirectory). I used the columns titled 'Location' and 'Per capita total spending on health (PPP int. $)' from this file. I cleaned the data by removing countries without per capita information. The beginning of the file looks like below:

```
Location	Spending
Afghanistan	44
Albania	577
Algeria	330
Andorra	3254
Angola	168
Antigua and Barbuda	991
Argentina	1287
Armenia	239
Australia	3441
Austria	4388
Azerbaijan	579
Bahamas	1988
Bahrain	1083
Bangladesh	57
Barbados	1523
…
```
## Problem 1
Calculate and display a histogram showing how much money countries spend per year per citizen. Experiment with buckets of different widths: 200, 500, and 1000. Space out the bars of your graph to make it visually more appealing. Add x and y axis to your plot with appropriate scales. When you hover over a bar in your histogram display the list of countries in that bucket. Change the color of the bar over which you hover.

The file 'Problem1.html' contains the histogram code. It begins with a style section in which histogram bars and mouse-over styles are set:
```
<!-- setup styles for histogram bars and mouseovers -->
<style>
            .d3-tip {
                line-height: 1;
                font-weight: bold;
                padding: 12px;
                background: rgba(0, 0, 139, 0.8);
                color: #fff;
                border-radius: 2px;
            }
            .bar rect {
                fill: cornflowerblue;
                shape-rendering: crispEdges;
            }
            .bar rect:hover{
                fill: rgba(0,0,139,.8);
            }
            .axis path, .axis line {
                fill: none;
                stroke: #000;
            }
</style>
```
I chose to have a blue histogram and a darker blue highlight for the rectangle and the tool tip when the mouse hovers above a given rectangle. Both the tool tip and the hovering rectangle are shown with slight transparency (a=0.8 in the rgba spec). Axes are shown in black.

The JavaScript d3 libraries are specified in the following section:
```
<!-- include d3 and d3.tip packages -->
<script src="d3/d3.min.js"></script>
<!-- tooltips for d3.js SVG visualizations -->
<script src="d3-tip/d3.tip.v0.6.3.js"></script> 
```
The data is loaded and parsed for usage in histogram construction into a data structure with two fields: Spending and Location.

```
// ------------------------------------------------------------------
// Read in the spending data from .csv file 
// ------------------------------------------------------------------
d3.csv("prob1_data.csv", parser,
       function(error, csvdata) {
       spendinghist(csvdata);
}); 

// ------------------------------------------------------------------
// Helper function to parse spending info
// ------------------------------------------------------------------
function parser(d) {
    d.pSpending = +d.Spending;
    return d;
}
```
Histogram bins are specified next:

```
// histogram bins 
var binsize = 500; // modify this for different bin widths
var minbin = 0;
var maxbin = 10000;
var numbins = (maxbin - minbin) / binsize;
var binmargin = 10;  // space between bars in units of Spending 
```
After some additional graph dimensioning (see submitted code), the histogram is computed. The histogram data “schema” consists of two fields:  numcountry  for the number of countries with per capita spending falling into a given bin and  meta for the metadata associated with each bin, i.e., the list of countries concatenated into a string to be displayed in the tool tip. The histogram is computed by iterating through each row of the input file and adding 1 to the bin corresponding to that row’s Spending value.

```
// setup data structure holding the histogram
histdata = new Array(numbins);
for (var i = 0; i < numbins; i++) {
		histdata[i] = { numcountry: 0, meta: "" };
}

// fill histdata with # countries in each bin and
// meta data consisting of country list for each bin
csvdata.forEach(function(d) {
var bin = Math.floor((d.pSpending - minbin) / binsize);
		if ((bin.toString() != "NaN") && (bin < histdata.length)) {
			histdata[bin].numcountry += 1;
			histdata[bin].meta += "<tr><td>" + d.Location +	"</td><td>" + "$"+
				d.pSpending.toFixed(0) + "</td></tr>";
		}
 }); 
```
Three scales are required for the graph: one for the placement of bars along the x axis, one for the height of the bars along the y axis, and one for defining the width of each rectangle. All scales are linear:

```
// scale for the width of the histogram bars
var x = d3.scale.linear()
	   .domain([0, (xmax - xmin)])
	   .range([0, width]);

// scale for the location of the bars
var x2 = d3.scale.linear()
	   .domain([xmin, xmax])
	   .range([0, width]);
	
// scale for the number of countries
var y = d3.scale.linear()
	   .domain([0, d3.max(histdata, function(d) { 
			return d.numcountry; 
		})])
	   .range([height, 0]); // reverse as y=0 is at the top
```
The svg object is created inside the ```<div>``` tag associated with Problem 1: 
```
// place the histogram in the "magistraludi_d3_pb1" div
var svg = d3.select("#magistraludi_d3_pb1").append("svg")
	  .attr("width", width + margin.left + margin.right)
	  .attr("height", height + margin.top + margin.bottom)
	  .append("g")
	  .attr("transform", "translate(" + margin.left + "," + 
						margin.top + ")"); 
```
Subsequently, various graphics objects are appended to it: histogram bars, chart title, x and y axes, and their labels. Excerpts are shown here, see full code for details.
```
// set up the bars
var bar = svg.selectAll(".bar")
	  .data(histdata)
	  .enter().append("g")
	  .attr("class", "bar")
	  .attr("transform", function(d, i) { return "translate(" + 
	       x2(i * binsize + minbin) + "," + y(d.numcountry) + ")"; })
	  .on('mouseover', tip.show)
	  .on('mouseout', tip.hide);

// add rectangles of prescribed size at prescribed locations
bar.append("rect")
	  .attr("x", x(binmargin))
	  .attr("width", x(binsize - 2 * binmargin))
	  .attr("height", function(d) { return height - y(d.numcountry); }); 
    …
```
The tool tip object is used to enable interaction with the histogram in the form of color change and text popup when the mouse hovers above a bar. The metadata, namely the list of countries corresponding to that bar is shown. The direction (e=East of the object) and  offset with respect to the object are also set:

```
// setup the tooltip
var tip = d3.tip()
      .attr('class', 'd3-tip')
      .direction('e')
      .offset([0, 20])
      .html(function(d) {
        return '<table id="tiptable">' + d.meta + "</table>";});
    svg.call(tip);
```
The resulting graph:
[https://github.com/magistraludi/D3-Visualization-Tutorial/tree/master/html]

## Problem 2 
Display the same information as in Problem 1 as a pie chart.

## Problem 3
Select any 20 countries. Create a 2D graph with 'Per capita total spending' on the horizontal axis and 'Doctors per 10,000 population' on the vertical axis. Represent every country as a colored circle with the diameter proportional to the life expectancy (LE) for that country. For example, you could use the value LE – 45 (i.e., subtract 45 from each LE) to make the difference more pronounced.  Change the color of the circles every 5 years: 55 to 60, 61 to 65, 65 to 70, etc. Display country names next to the circles. 
