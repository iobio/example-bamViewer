# Step 3
In this step we'll create the visualization for our BAM viewer and visualize some test data.

### Add div to hold visualization
At [line 47](https://github.com/iobio/example-bamViewer/blob/master/step2/app.step2.html#L47), add a div that we will later render the visualization into.
```JavaScript
<!-- Visualization -->
<div id='viz' style="width:100%"></div>
```

### Define dimensions of visualization
Add some dimension variables at the beginning of the script tag at [line 67](https://github.com/iobio/example-bamViewer/blob/master/step2/app.step2.html#L67).
```JavaScript
// Defaults
var webservice = 'services.iobio.io/samtools/a',
    margin = {top: 30, left: 30, right: 30, bottom: 30},
    width = 800,
    height = 500;
```

### Add visualization at the end of the script tag
We are going to use the alignment visualization from the iobio.viz library. This visualization can take a pileup and has some specific alignment features.
```JavaScript
// Draw Alignment Visualization
function draw(alns) {
	// Hide spinner
	$('#spinner').css('display', 'none');

	// Create pileup layout which will calculate position(x,y) of each alignment passed through it
	var pileup = iobio.viz.layout.pileup().sort(null).size(width);

	// Create alignment chart with attributes
	var chart = iobio.viz.alignment()
		.width(width)
		.height(height)
		.margin(margin)
		.yAxis(null) // remove yAxis
		.xValue(function(d) { return d.x; }) // let the chart know how to get the x coordinate of each alignment
		.yValue(function(d) { return d.y; }) // let the chart know how to get the y coordinate of each alignment
		.wValue(function(d) { return d.w; }) // let the chart know how to get the width of each aligmnent
		.id(function(d) { return 'read-' + d.data.id.replace('.', '_'); }) // Set id and remove '.'s b\c they are inivalid ids
		.tooltip(function(d) { return "id:  " + d.data.id + "<br/>" + "pos: " + d.data.start + ' - ' + d.data.end + "<br/>"});

	// Create selection with viz div and the alignment data
	var selection = d3.select('#viz').datum( pileup(alns) );

	// Draw chart
	chart(selection);
}

// Test visualization
// Temporary data for testing
alns = [{start:1, end:3, id:'1'}, {start:2, end:4, id:'2'}, {start:3, end:5, id:'3'},{start:4, end:6, id:'4'}];
draw(alns);
```

So it doesn't look like much now, but that will change when we hook up the real data, which we'll be doing in the next step.

Also a few things to note:
* The pileup layout here does the heavy lifting for determining an efficient pileup. After the alignments are run through the pileup layout each alignment has an x and y coordinate. This keeps the chart simple and allows it to simply place each alignment based on these coordinates.
* The pileup returns data in the format of ```{ x:xpos, y:ypos, data:{} )``` where the original data is now stored in data. This is done so that nothing in your original data is overwritten. This is why the id accessor function grabs the id by doing ```d.data.id```.

### Results
[Step3 Live](http://iobio.github.io/example-bamViewer/step3/app.step3.html)

Screenshot
![alt text](https://raw.githubusercontent.com/iobio/example-bamViewer/master/assets/img/step3.png)
