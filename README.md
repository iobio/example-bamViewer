# IOBIO Example App

![alt text](https://raw.githubusercontent.com/iobio/example-bamViewer/master/assets/img/step5.png)

This is a tutorial to build a very simple web app with iobio. If you want to skip to the end and see the finished product [click here](http://iobio.github.io/example-bamViewer/step5/app.step5.html). This tutorial will include
 * Grabbing some genomic data using websockets
 * Visualizing the data
 * Customizing the Visualization

We will be making use of a few iobio libraries including:
 * [```iobio.js```](https://github.com/iobio/iobio.js) - create commands and handle websocket creation and returning data
 * [```iobio.viz```](https://github.com/iobio/iobio.viz) - visualization library that handles streaming data

To learn more about these libraries go to there home pages linked above.

Each step below has a directory in this repo. If things go wrong somewhere along the way you can simply copy the example file (e.g. app.step2.html) in the step directory you are currently at.

### Step 1 - [Create UI For Data Selection](/step1)

### Step 2 - [Create iobio Command](/step2)

### Step 3 - [Create Visualization](/step3)

### Step 4 - [Hook Up Data to Visualization](/step4)

### Step 5 - [Improve visualization](/step5)

### Step 6 - Test the streaming!
One of the defining characteristics of iobio is its streaming nature. The visualization we've created (and all iobio.viz visualizations) can handle streaming data by default. To see this in action test a bigger region like this 20:4000000-4009000. This looks especially nice using the app.step4.html file, which has lots of colors.
