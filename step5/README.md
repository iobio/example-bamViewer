# Step 5
So we have our basic viewer up and working. We can now have a little fun and alter the chart to do some more use-case specific stuff. In this case we'll be showing strandedness, mate pairs, mismatches, and deletions. Some features are built in to the visualizatin and some are just added on top, using some custom d3 code.

### Use arrows to show strandedness
Showing strandedness is built in to the alignment chart, so we can just add an extra attribute to our chart to get it to work. At [line 161](https://github.com/iobio/example-bamViewer/blob/master/step4/app.step4.html#L161) we can add the direction attribute and give some information on how to determine if an alignment is forward or reverse. I've included the whole chart below, but you only have to add the last line. Also make sure to remove the semicolon from the .tooltip line below, since we are adding to our chained functions.
```JavaScript
var chart = iobio.viz.alignment()
	.width(width)
	.height(height)
	.margin(margin)
	.yAxis(null)
	.xValue(function(d) { return d.x; })
	.yValue(function(d) { return d.y; })
	.wValue(function(d) { return d.w; })
	.id(function(d) { return 'read-' + d.data.id.replace('.', '_'); })
	.tooltip(function(d) { return "id:  " + d.data.id + "<br/>" + "pos: " + d.data.start + ' - ' + (d.data.end) + "<br/>"  + "seq: " +       d.data.seqStr
		+ "<br/> cigar: " + d.data.cigarStr;
	})
	.directionValue(function(d) { return d.data.flag.read_reverse_strand ? 'reverse' : 'forward' ; }) // ADD THIS HERE
```

### Show mate pair
Lets say we want to emphasize mate pairs in this visualization. Here we can add some custom d3 code that is not part of the chart to get a magnifying effect. Add this right below where you call the chart, below [line 168](https://github.com/iobio/example-bamViewer/blob/master/step4/app.step4.html#L168)
```JavaScript
selection.selectAll('.alignment') // alignment is g tag that contains a polygon tag
	.on('mouseover', function(d) {
        d3.selectAll('#' + this.id).select('polygon') // Magnify the polygon
        	.attr('transform', 'scale(3,3)')
        	.each(function(){ // forces hovered alignment to front
				this.parentNode.parentNode.appendChild(this.parentNode);
		    });
    })
    .on('mouseout', function(d) {
        d3.selectAll('#' + this.id).select('polygon').attr('transform', 'scale(1,1)');
    })
    .select('polygon') // Add stroke to actual polygons
	    .style('stroke', 'white')
	    .style('stroke-width', '1px')
```

### Show mismatches and deletions
Finally, lets see if we can show some finer sequence information. We could add an element for each nucleotide, but that would increase our number of DOM objects by around 100X for the default URL BAM sample and even more for other samples. So instead we can be a little clever with color gradients to get the same effect. Here we'll use a color gradient to show mismatches as red and deletions as black. So we'll use the .color chart helper attribute to set the fill color for the alignment polygons. I've only included the last line of the chart for context. So add this new color attribute at [line 163](https://github.com/iobio/example-bamViewer/blob/master/step4/app.step4.html#L163)  right below the directionValue attribute we added above.
```JavaScript
// snipped off rest of the chart definition above
.directionValue(function(d) { return d.data.flag.read_reverse_strand ? 'reverse' : 'forward' ; }) // previously added
.color(function(d,i) { // Add from here down
	var seqLength = d.data.seqStr.length,
		pos = 0,
		stopColor;

	// Create linear gradient
	var linearGradient = d3.select('#viz svg').append('linearGradient').attr('id', 'gradient-' + i);

    // Use MD Tag to get Mismatch and Delete information
	if (d.data.md) {
	    // Split on numbers to get each span
		d.data.md.split('MD:Z:')[1].split(/([0-9]+)/).forEach(function(c) {
			if (c == '' ) { return } // Ignore empties
			else if(isNaN(c)){ // Handle non numbers
				if (c[0] == '^') { // Deletion
					percent = pos + (c.length-1) / seqLength * 100;
					stopColor = 'black';
				} else { // Mismatch
					percent = pos + (c.length) / seqLength * 100;
					stopColor = 'red';
				}

			} else { // Match - Handle numbers
				var percent = pos + parseInt(c) / seqLength * 100;
				stopColor = '#b4b4b4';
			}
			linearGradient.append('stop')
					.attr('offset', pos + '%')
					.attr('stop-color', stopColor);

			linearGradient.append('stop')
				.attr('offset', percent + '%')
				.attr('stop-color', stopColor);

			pos = percent;
		})
		return 'url(#' + 'gradient-' + i;
	} else {
		return 'rgb(180,180,180)';
	}
});
```

### Results
[Step5 Live](http://iobio.github.io/example-bamViewer/step5/app.step5.html)

Screenshot
![alt text](https://raw.githubusercontent.com/iobio/example-bamViewer/master/assets/img/step5.png)
