# Step 2
In this step we'll create the iobio commands to grab the data, but we won't do anthing with the data yet.

### Create script element
At the end of container div add a script tag. All of your JavaScript will go in here

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
