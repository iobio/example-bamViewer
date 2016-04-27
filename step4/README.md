# Step 4
Now we can finally hook up the real data to our visualization code and see our bam viewer

### Add SAM/BAM helper parser functions
First we need to add some helper functions to parse the SAM/BAM records coming back. Add these at the end of the ```script``` tag
```JavaScript
function parseSamRecord(str) {
    var rec = {},
        fields = str.split("\t")

    rec.id = fields[0];
    rec.flag = unpackFlag(+fields[1]);
    rec.rname = fields[2];
    rec.start = +fields[3];
    rec.cigarStr = fields[5];
    rec.seqStr = fields[9];
    rec.end = rec.start + rec.seqStr.length;
    rec.md = fields.slice(11).filter(function(d) { return d.slice(0,2) == 'MD' })[0];

    return rec;
}

function unpackFlag(flagValue) {
    var flag = {};

    lstFlags = [
        ["read_paired", 0x1],
        ["read_mapped_in_proper_pair", 0x2],
        ["read_unmapped", 0x4],
        ["mate_unmapped", 0x8],
        ["read_reverse_strand", 0x10],
        ["mate_reverse_strand", 0x20],
        ["first_in_pair", 0x40],
        ["second_in_pair", 0x80],
        ["not_primary_alignment", 0x100],
        ["read_fails_quality_checks", 0x200],
        ["read_is_PCR_or_optical_duplicate", 0x400],
        ["supplementary_alignment", 0x800]
    ];

    for(var i = 0; i < lstFlags.length; i++) {

        if(lstFlags[i][1] & flagValue) {
            flag[ lstFlags[i][0] ] = true;
        } else {
            flag[ lstFlags[i][0] ] = false;
        }
    }
    return flag;
}
```

### Hook up URL data to visualization
At [line 89](https://github.com/iobio/example-bamViewer/blob/master/step3/app.step3.html#L85) replace the ```data``` event callback with this code
```JavaScript
// Do stuff with results
var partialRecord = '';
cmd.on('data', function(msg) {
    var recs = (partialRecord + msg).split("\n");
	// Keep track of records that have been cut off at the end of the message
    partialRecord = recs[recs.length-1].slice(-1) == "\n" ? '' : recs.pop()
	recs.forEach(function(recStr) {
        alns.push( parseSamRecord(recStr) );
	})
	draw( alns );
})
```

### Hook up FILE data to visualization
At [line 115](https://github.com/iobio/example-bamViewer/blob/master/step3/app.step3.html#L115) replace ```console.log(alnseq)```  If you don't plan on testing file data, you can skip this bit
```JavaScript
// Add a few fields to match the alignments coming from the URL
alns = alnseq.map(function(d) {
		d.head.id = d.head.read_name;
		var md = d.aux_data.filter(function(t) { return t.tag == 'MD'; })[0];
		if (md) d.head.md = 'MD:Z:' + md.value
		d.head.flag = unpackFlag(d.head.flag);
		d.head.start = d.head.pos;
		d.head.end = d.head.start + d.head.l_seq;
		return d.head;
	})
draw(alns);
```

### Results
[Step4 Live](http://iobio.github.io/example-bamViewer/step4/app.step4.html)

Screenshot
![alt text](https://raw.githubusercontent.com/iobio/example-bamViewer/master/assets/img/step4.png)
