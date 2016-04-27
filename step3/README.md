# Step 3
In this step we'll create the visualization for our BAM viewer and visualize some test data

### Define dimensions of visualization
Add some dimension variables at the beginning of the script tag (around line 68)
```JavaScript
// Defaults
var webservice = 'services.iobio.io/samtools',
    margin = {top: 30, left: 30, right: 30, bottom: 30},
    width = 800,
    height = 500;
```

### Add visualization at the end of the script tag
We are going to add the visualization code at the end of the script tag. Don't bother putting it into a function yet. We'll do that later.
```JavaScript
// Draw Alignment Visualization

// Temporary data for testing
alns = [{start:1, end:3, id:'1'}, {start:2, end:4, id:'2'}, {start:3, end:5, id:'3'},{start:4, end:6, id:'4'}];

// Hide spinner
$('#spinner').css('display', 'none');

// Create pileup layout to calculate position (based on width)
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
	.tooltip(function(d) { return "id:  " + d.data.id + "<br/>" + "pos: " + d.data.start + ' - ' + d.data.end + "<br/>"
	});

// Create selection with viz div and the alignment data
var selection = d3.select('#viz').datum( pileup(alns) );

// Draw chart
chart(selection);
```

A few things to note:
* The pileup layout here does the heavy lifting for determining an efficient pileup. After the alignments are run throught the pileup layout each alignmetn has an x and y coordinate. This keeps the chart simple and allows it to simply place each alignment based on these coordinates.
* The pileup returns data in the format of ```{ x:xpos, y:ypos, data:{} )``` where the original data is now stored in data. This is done so that nothing in your original data is overwritten. This is what the id accessor function grabs the id by doing ```d.data.id```
