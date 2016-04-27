# Step 1

In this step we'll create a rough UI that can take the inputs we need, but won't do anything yet.

### Setup
First, clone the repo, and use the skeleton file to get a good starting point in a new directory

```
  git clone https://github.com/iobio/example-bamViewer.git
  cd example-bamViewer/start
  cp app.skeleton.html bamViewer.html
```

### Add Buttons
Add input buttons for user to select a region and data (either via a file or an URL)

``` html
  <div class='container text-center'>
		<!-- Title -->
		<h1>BAM Viewer</h1>

		<!-- inputs -->
		<div  id="input">
			<!-- region input -->
			<div>Region: <input id='region' type='text' value="2:4000000-4001000" ></input></div>

			<!-- url input -->
			<button class='btn btn-info' type="button" data-toggle="modal" data-target="#urlModal">Open Url</button>

			<!-- file input -->
			<label class="btn btn-info" for="my-file-selector">
			    <input id="my-file-selector" onchange="goFile(this)" type="file" style="display:none;" multiple>
			    Open File
			</label>
		</div>

	</div>
```

### Hook up modal
Use bootstrap to create a modal for the URL button.

```html
  <!-- in container div -->
  <!-- url modal -->
	<div class="modal fade" id="urlModal" tabindex="-1" role="dialog" aria-labelledby="urlModal" aria-hidden="true">
    <div class="modal-dialog">
        <div class="modal-content">
            <div class="modal-body text-center">
                <h3>Enter Url</h3>
                <input id='url' type='text' value="http://s3.amazonaws.com/iobio/NA12878/NA12878.autsome.bam"></input>
                <button onclick='goUrl()' type="button" data-dismiss="modal" class="btn btn-primary">Go</button>
            </div>
    	</div>
  	</div>
```

### Results
We should now see something like this
![alt text](https://raw.githubusercontent.com/iobio/example-bamViewer/master/assets/img/step1.png)