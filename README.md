# Data-Visualization-with-D3
FreeCodeCamp's exercises to practice D3 and SVG

## Introduction to the Data Visualization with D3 Challenges
D3.js, or D3, stands for Data Driven Documents. D3 is a JavaScript library to create dynamic and interactive data visualizations in the browser. It's built to work with common web standards, namely HTML, CSS, and Scalable Vector Graphics (SVG).

D3 takes input data and maps it into a visual representation of that data. It supports many different data formats. D3 lets you bind (or attach) the data to the Document Object Model (DOM). You use HTML or SVG elements with D3's built-in methods to transform the data into a visualization.

D3 gives you a lot of control over the presentation of data. This section covers the basic functionality and how to create visualizations with the D3 library.

## Create a Bar for Each Data Point in the Set
The last challenge added only one rectangle to the svg element to represent a bar. Here, you'll combine what you've learned so far about data(), enter(), and SVG shapes to create and append a rectangle for each data point in dataset.

A previous challenge showed the format for how to create and append a div for each item in dataset:
<pre><code>d3.select("body").selectAll("div")
  .data(dataset)
  .enter()
  .append("div")</pre></code>
There are a few differences working with rect elements instead of divs. The rects must be appended to an svg element, not directly to the body. Also, you need to tell D3 where to place each rect within the svg area. The bar placement will be covered in the next challenge.

## Dynamically Set the Coordinates for Each Bar
The last challenge created and appended a rectangle to the svg element for each point in dataset to represent a bar. Unfortunately, they were all stacked on top of each other.

The placement of a rectangle is handled by the x and y attributes. They tell D3 where to start drawing the shape in the svg area. The last challenge set them each to 0, so every bar was placed in the upper-left corner.

For a bar chart, all of the bars should sit on the same vertical level, which means the y value stays the same (at 0) for all bars. The x value, however, needs to change as you add new bars. Remember that larger x values push items farther to the right. As you go through the array elements in dataset, the x value should increase.

The attr() method in D3 accepts a callback function to dynamically set that attribute. The callback function takes two arguments, one for the data point itself (usually d) and one for the index of the data point in the array. The second argument for the index is optional. Here's the format:
<pre><code>selection.attr("property", (d, i) => {
  /* 
  * d is the data point value
  * i is the index of the data point in the array
  */
})</code></pre>
It's important to note that you do NOT need to write a for loop or use forEach() to iterate over the items in the data set. Recall that the data() method parses the data set, and any method that's chained after data() is run once for each item in the data set.

## Invert SVG Elements
You may have noticed the bar chart looked like it's upside-down, or inverted. This is because of how SVG uses (x, y) coordinates.

In SVG, the origin point for the coordinates is in the upper-left corner. An x coordinate of 0 places a shape on the left edge of the SVG area. A y coordinate of 0 places a shape on the top edge of the SVG area. Higher x values push the rectangle to the right. Higher y values push the rectangle down.

To make the bars right-side-up, you need to change the way the y coordinate is calculated. It needs to account for both the height of the bar and the total height of the SVG area.

The height of the SVG area is 100. If you have a data point of 0 in the set, you would want the bar to start at the bottom of the SVG area (not the top). To do this, the y coordinate needs a value of 100. If the data point value were 1, you would start with a y coordinate of 100 to set the bar at the bottom. Then you need to account for the height of the bar of 1, so the final y coordinate would be 99.

The y coordinate that is <code>y = heightOfSVG - heightOfBar</code> would place the bars right-side-up.

Change the callback function for the y attribute to set the bars right-side-up. Remember that the height of the bar is 3 times the data value d.

Note
In general, the relationship is <code>y = h - m * d</code>, where m is the constant that scales the data points.

## Add Attributes to the Circle Elements
The last challenge created the <code>circle</code> elements for each point in the <code>dataset</code>, and appended them to the SVG canvas. But D3 needs more information about the position and size of each <code>circle</code> to display them correctly.

A <code>circle</code> in SVG has three main attributes. The <code>cx</code> and <code>cy</code> attributes are the coordinates. They tell D3 where to position the center of the shape on the SVG canvas. The radius (<code>r</code> attribute) gives the size of the <code>circle</code>.

Just like the <code>rect</code> <code>y</code> coordinate, the <code>cy</code> attribute for a circle is measured from the top of the SVG canvas, not from the bottom.

All three attributes can use a callback function to set their values dynamically. Remember that all methods chained after <code>data(dataset)</code> run once per item in dataset. The d parameter in the callback function refers to the current item in dataset, which is an array for each point. You use bracket notation, like <code>d[0]</code>, to access the values in that array.

<pre><code>const dataset = [
                  [ 34,    78 ],
                  [ 109,   280 ],
                  [ 310,   120 ],
                  [ 79,    411 ],
                  [ 420,   220 ],
                  [ 233,   145 ],
                  [ 333,   96 ],
                  [ 222,   333 ],
                  [ 78,    320 ],
                  [ 21,    123 ]
                ];

    const w = 500;
    const h = 500;

    const svg = d3.select("body")
                  .append("svg")
                  .attr("width", w)
                  .attr("height", h);

    svg.selectAll("circle")
       .data(dataset)
       .enter()
       .append("circle")
       .attr("cx", d => d[0])
       .attr("cy", d=> h- d[1])
       .attr("r", 5) </code></pre>


## Data Visualization with D3: Use a Pre-Defined Scale to Place Elements
With the scales set up, it's time to map the scatter plot again. The scales are like processing functions that turn the x and y raw data into values that fit and render correctly on the SVG canvas. They keep the data within the screen's plotting area.

You set the coordinate attribute values for an SVG shape with the scaling function. This includes x and y attributes for rect or text elements, or cx and cy for circles. Here's an example:

<code>shape.attr("x", (d) => xScale(d[0]))</code>

Scales set shape coordinate attributes to place the data points onto the SVG canvas. You don't need to apply scales when you display the actual data value, for example, in the text() method for a tooltip or label.

  <pre><code><script>
    const dataset = [
                  [ 34,     78 ],
                  [ 109,   280 ],
                  [ 310,   120 ],
                  [ 79,   411 ],
                  [ 420,   220 ],
                  [ 233,   145 ],
                  [ 333,   96 ],
                  [ 222,    333 ],
                  [ 78,    320 ],
                  [ 21,   123 ]
                ];

    const w = 500;
    const h = 500;
    const padding = 60;

    const xScale = d3.scaleLinear()
                     .domain([0, d3.max(dataset, (d) => d[0])])
                     .range([padding, w - padding]);

    const yScale = d3.scaleLinear()
                     .domain([0, d3.max(dataset, (d) => d[1])])
                     .range([h - padding, padding]);

    const svg = d3.select("body")
                  .append("svg")
                  .attr("width", w)
                  .attr("height", h);

    svg.selectAll("circle")
       .data(dataset)
       .enter()
       .append("circle")
       .attr("cx", d => xScale(d[0]))
       .attr("cy", d => yScale(d[1]))
       .attr("r", 5)

    svg.selectAll("text")
       .data(dataset)
       .enter()
       .append("text")
       .text((d) =>  (d[0] + ", " + d[1]))
       .attr("x", d => xScale(d[0] + 10))
       .attr("y", d => yScale(d[1]))
  </script></code></pre>
