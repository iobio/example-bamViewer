# Step 2
In this step we'll create the iobio commands to grab the data, but we won't do anthing with the data yet.

### Add spinner
Add a spinner so that we can see when the app is working

Inside he container div at the end add a spinner
```html
<!-- spinner -->
<img id='spinner' src="../assets/img/spinner.gif"></img>
```

In the style tag at [line 19](https://github.com/iobio/example-app/blob/master/step1/app.step1.html#L19), add some style to the spinner
```html
<style type="text/css">
	.container { margin-top: 100px; }

    #spinner { display: none; margin-top: 60px; }
</style>
```

### Create script element
Inside the body tag but at the very end add a script tag. All of your JavaScript will go in here.

```html
<script>

</script>
```

### Add webservice as a default
We'll be using samtools to grab regions of a bam. So we'll use the standard samtools webservice located at ```services.iobio.io/samtools```. All webservices follow this naming scheme e.g. ```services.iobio.io/freebayes```, ```services.iobio.io/vep```.

```html
<script>
    // Defaults
  	var webservice = 'services.iobio.io/samtools'
</script>
```

### Get data from URL
Create a function that can grab a region of data from any url pointing to a BAM file
```JavaScript
function goUrl() {
    // Get inputs
  	var url = $('#url').val();
    var region = $('#region').val() ;

    // Show spinner
    $('#spinner').css('display', 'inline');

    // Create iobio command
    var cmd = new iobio.cmd(webservice, [ 'view', url, region ])

    // Do stuff with results
    cmd.on('data', function(data) {
        console.log(data);
    })

    // Error handling
    cmd.on('error', function(err) { console.log('Error: ' + err);})

    // Run command
    cmd.run();
}
```

### Get data from FILE
Create a function that can grab a region of data from any url pointing to a BAM file
```JavaScript
function goFile(evt) {
    // Figure out which is BAM and which is BAI
    var bam = evt.files[0].name.slice(-3) == 'bam' ? evt.files[0] : evt.files[1];
    var bai = evt.files[0].name.slice(-3) == 'bai' ? evt.files[0] : evt.files[1];

    // Parse Region
    var region = $('#region').val();
    var chr = region.split(':')[0];
    var start = +region.split(':')[1].split('-')[0];
    var end = +region.split(':')[1].split('-')[1];
    var alns;

    // Show spinner
    $('#spinner').css('display', 'inline');

    var bamR = new readBinaryBAM(bai, bam);
    bamR.bamFront(function(){
        bamR.getAlns(chr, start, end, function(alnseq){
            console.log(alnseq);
        })
    });
}
```

### Results
We should now see something like this
![alt text](https://raw.githubusercontent.com/iobio/example-app/master/assets/img/step2.png)
