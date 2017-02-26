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
## Project Structure
I created a main file 'index.html' which references three additional .html pages: 'Problem1.html', 'Problem2.html', and 'Problem 3.html'. The file consists of a header section and three ```<div>``` tags corresponding to each of the problem solutions. I specified some style settings for both the header and the ```<div>``` tags in a ```<style>``` tag. 

## Problem 1
Calculate and display a histogram showing how much money countries spend per year per citizen. Experiment with buckets of different widths: 200, 500, and 1000. Space out the bars of your graph to make it visually more appealing. Add x and y axis to your plot with appropriate scales. When you hover over a bar in your histogram display the list of countries in that bucket. Change the color of the bar over which you hover.

## Problem 2 
Display the same information as in Problem 1 as a pie chart.

## Problem 3
Select any 20 countries. Create a 2D graph with 'Per capita total spending' on the horizontal axis and 'Doctors per 10,000 population' on the vertical axis. Represent every country as a colored circle with the diameter proportional to the life expectancy (LE) for that country. For example, you could use the value LE – 45 (i.e., subtract 45 from each LE) to make the difference more pronounced.  Change the color of the circles every 5 years: 55 to 60, 61 to 65, 65 to 70, etc. Display country names next to the circles. 
