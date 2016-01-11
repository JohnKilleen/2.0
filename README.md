Durham NC adaptations of Tobin Bradley's Quality of Life Explorer (in development). See Bradley's notes below.
=================


Quality of Life Dashboard v2
=================

A joint project between the City of Charlotte, Mecklenburg County, and UNCC. The first release of the dashboard is now in the [v1](https://github.com/tobinbradley/Mecklenburg-County-Quality-of-Life-Dashboard/tree/v1) branch.

The current (master) version is ready for production, but we decided to add a new calculation at the last minute requiring new data deliverables and code before we launch. You can see a reasonably current verision of the pre-launch site [here](http://mcmap.org/qol). Our launch date is looking like May 2015.

The dashboard uses D3 for visualizations. The good news is besides all the cool features in the new version has it is less than half the size over the wire and loads twice as fast. The bad news is IE8 absolutely will not work. I'm sorry/you're welcome.

We hope you find this project useful. Patches are always welcome!

<!--
Here's a handy [YouTube Tutorial](https://www.youtube.com/watch?v=qmx2mZXeHZQ) on customizing the Dashboard for your area of interest if reading isn't your bag.
-->

## Setting Up

installing markdown to json
1  npm install gulp-markdown-to-json --save-dev
2  npm install gulp-util
3  add to gulpfile.js 

### Install node
Installing [node](http://nodejs.org/) is a piece of cake. On Windows, download the install file and click Next until it stops asking you to click Next. On MacOS I have no idea, but there's probably a draggy-droppy involved. Any Linux distro will have nodejs in its repos, so do a `sudo pacman -S nodejs` or `sudo apt-get install nodejs` or whatever package management magic you system employs.

To make sure everything went well here, when you're done installing pull up a terminal (that's DOS for you Windows types) and type `node --version`. You should get some like `v0.10.28`.

### Install topojson, gulp and bower
I'm using [gulp](http://gulpjs.com/) as the build/dev system, because awesome. We'll be using [topojson](https://github.com/mbostock/topojson) to encode our geography. [Bower](http://bower.io/) is being used to manage JavaScript dependencies. To install them, head to a terminal and type:

    npm install -g gulp topojson bower

Because Windows is the only desktop OS that doesn't come with a C compiler, you'll need to get one to get topojson to install. Fortunately the free [Express version of Visual Studio](http://go.microsoft.com/?linkid=9816758) works fine. There is more information about this [here](https://github.com/TooTallNate/node-gyp). Yay Windows.


### Clone or download/unzip the project
If you have git installed, just:

    git clone https://github.com/tobinbradley/Mecklenburg-County-Quality-of-Life-Dashboard.git  
If you don't have git installed, grab the zip file instead. *The rest of the commands you see below will need to be run in your project root folder.*

### Install the node and bower dependencies
We need to install our project dependencies. Those dependencies are specified in package.json and bower.json respectively. We will also run a gulp task to swap in a simple search.

    npm install
    bower install

### Swap in the simple search
The default search autocomplete is for Mecklenburg and won't work for anyplace else. This command will swap it out with a simple search for your neighborhood ID's. The advanced search will be renamed search.js.advanced if you want some examples of how to add additional search services.

    gulp init

### Fire it up!
The default gulp task starts [BrowserSync](https://github.com/BrowserSync/browser-sync) and launches your current web browser to view the site. Live reload is enabled, so changes will automatically refresh in your browser.

    gulp

## Customizing the Dashboard for your area
Data in the dashboard comes in three pieces:

<div style="text-align: center">
<img src="http://i.imgur.com/H3aTnOW.png" style="max-width: 100%">
</div>

* The neighborhood geography topojson with your neighborhood id.
* The metric data. You will need 1 to 3 files depending on the calculation and what you want to show.
* The metric metadata.

### Topojson
The first thing you'll need to do is convert your neighborhood layer to topojson. There are a lot of options during conversion, which you can peruse [here](https://github.com/mbostock/topojson/wiki/Command-Line-Reference). You should start out with an Esri shapefile or geojson file projected to WGS84 (EPSG:4326). You can preview, manipulate, and project your data with [QGIS](http://www.qgis.org/).

Here's a good general call to create your topojson file, but do play around with the options if you want more or less line generalization:

    topojson -o geography.topo.json -s 7e-11 --id-property=id_field your_shapefile.shp

With `id_field` being the field in the shapefile you want to use for your neighborhood identifier. Copy that file into `dist/data`. Make note of what your shapefile was named - you'll need that information when you update `config.js`.

### Metrics
Metric files are simple CSV files named to reflect what they are. The are stored in `src/data/metric`. The format is always the same:

     id,y_2012,y_2014

 With `y_XXXX` representing the year of that data column. Null values are expressed as nothing, like:

     id,y_2012,y_2014
     11,100,200
     12,,400

Depending on what you want to do with the metric, you will name your files slightly different things. If your metric is normalized, like population density, you would have it as `n1.csv`. If your metric is raw, like population, you would name it `r1.csv`. If your metric is going to be re-normalized (i.e. weighted average), you would need the denominator or units for the normalization. That would be `d1.csv`. The number in these names is just a unique number for that metric - it can be anything you set in your `config.js`.

These CSV's are converted into JSON files when the project is built.

**Tip 1: CSV column case sensitivity**

Spreadsheet software often likes to capitalize the first letter in the `y_XXXX` field. That will turn your life into a furious ball of nothing, so keep an eye on that. You can all your files at once on Linux like so:

     for f in *.csv; do sed -e 's/\(.*\)/\L\1/' $f > out/$f; done

**Tip 2: Sort on the ID**

Your CSV files should be sorted on the metric ID. On Linux to auto do this to all your files:

    export LC_NUMERIC=C
    for f in *.csv; do sort -n $f > out/$f; done​

**Tip 3: Beware the hanging zero**

Some identifiers like Census tracts can have a hanging zero, like 541.10. If you are manipulating your data files in spreadsheet software, make sure your ID column is being treated as a string and not a number or it will get dropped and your life will be destroyed.


### Metadata
Metadata files are in markdown and are named for the metric, like `m1.md`, and are located in `src/data/meta`. Your metadata file needs to maintain a structure with h2 and h3's laid out like this:

    ## Title of Metric
    Median age of poodles

    ### Whis is this important?
    Because we like poodles.

    ### About the Data
    I hang out at dog parks and type stuff in my phone. Circa 1986.

    ### Additional Resources
    Dog pound yo.

When processing the HTML from the markdown, the h2 and h3 tags are used in a really awful lefty-righty kind of way. If you insert another h2 or h3, you will be boned. Same if you drop one. If you want to format the metadata differently, you must edit `src/scripts/functions/metadata.js` to meet your needs. It won't be hard, but not doing it and jacking with the markdown will lead to disappointment.

After you add metadata, run `gulp build` to convert the markdown to HTML.

### Customize config.js
`src/scripts/config.js` has knobs you will need to turn to set up the dashboard for your area. It is all well documented there. Each metric has a JSON description with a few required and many optional properties.

In particular, note the `type` on the metric description, which describes the calculation to be performed. The type of calculation to be performed effects what the dashboard will look for.

* `sum`: This is for raw variables, like population. It'll look for `rX.csv`.
* `mean`: This is for normalized variables, like population density. It'll look for `nX.csv`.
* `normalize`: This will normalize data or perform a weighted average. Suppose you wanted to have a weighted average for people per acre (population density). If you specify normalize, it'll look for a raw population in `rX.csv` and the number of acres for the denominator in `dX.csv` and produce a weighted average.

*Note the normalize type doesn't work yet. It doesn't break, it just does the same thing as mean.*

### Odds and ends
You will have a few additional things to fiddle with, all of which you'll find in either `src/index.html` or `src/report.html`. Different app titles, logos, making some custom charts for your report - that kind of thing. It'll be pretty easy. At the bottom of report.html you will find example chart templates which will require a bit more thought, but all of the chart properties are logically laid out in data attributes on the canvas elements.

## Build for deployment
Building for deployment does all of the niceties for you - JavaScript concatenation and minification, LESS preprocessing/auto-prefixing/minification, Markdown conversion, CSV to JSON conversion, image optimzation, and cache busting. From the root folder run:

    gulp build

Copy the contents of the `dist` folder to your production web server and you're good to go.
