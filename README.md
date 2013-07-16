D3js Reusable Map
===
D3js Reusable Map is the beginings of an interactive map API based on the [D3js](http://d3js.org/) library created by [mbostock](https://github.com/mbostock).

The demo lends heavily from the excellent [d3js map tutorial](http://bost.ocks.org/mike/map/) with a focus on [reusable pattern](http://bost.ocks.org/mike/chart/) and help from [bleeding edge](http://bleedingedgepress.com/our-books/developing-a-d3-js-edge/). That and the many [b.locks](http://bl.ocks.org/mbostock) and [d3js tutorials](https://github.com/mbostock/d3/wiki/Tutorials) from [mbostock](https://github.com/mbostock) that demonstrate d3js usage!

#### [B.lock #6009045](http://bl.ocks.org//thefitzpaddy/6009045)
To view the result of this repo visit b.lock [#6009045](http://bl.ocks.org//thefitzpaddy/6009045)!

## Data Preparation
The following outlines the method by which to prepare your own data in TopoJSON format for use by d3js.

### Prerequisites
Prerequisites are required to prepare custom TopoJSON datasets;

- _[GDAL](http://www.gdal.org/)_

	The Geospatial Data Abstraction Library (GDAL) is used primarily for ogr2ogr data transformations.

- _[TopoJSON](https://github.com/mbostock/topojson)_

	[mbostock](https://github.com/mbostock) has designed TopoJSON as an extension to GeoJSON where geometries are not encoded discretely - if geometries share features, they are combined (such as borders). Additionally TopoJSON encodes numeric values more efficiently and can incorporate a degree of simplification. This simplification can result in savings of file size of 80% or more depending on the area and use of compression.

### Data Source
- _[110m Populated Places](http://www.naturalearthdata.com/downloads/50m-cultural-vectors/)_

	[Natural Earth](http://www.naturalearthdata.com/) provides a variety of [cultural, physical and raster datasets](http://www.naturalearthdata.com/downloads/) maintained by cartographer [Nathaniel Vaughn Kelso](http://kelsocartography.com/) (and [others](www.naturalearthdata.com/about/contributors/)). Shapefiles are beautifully simplified by hand for different resolutions, letting you choose the resolution appropriate to your application.

- _[50m Cultural Vectors](http://www.naturalearthdata.com/downloads/50m-cultural-vectors/)_

	Filtered by capital cities.

### Processing
The Shapefiles from [Natural Earth](http://www.naturalearthdata.com/) are converted to GeoJSON format and then TopoJSON.

- Shp to GeoJSON using [ogr2ogr](http://www.gdal.org/ogr2ogr.html)

		~# ogr2ogr -f GeoJSON -select oid_, iso_a2, iso_a3, admin, pop_est 
			-o output.json input.shp

- GeoJSON to TopoJSON using [TopoJSON API](https://github.com/mbostock/topojson/wiki/Command-Line-Reference)

		~# topojson -p OID_, ISO_A2, ISO_A3, ADMIN, POP_EST 
			-o output.json -- admin0=input.json/input.shp

##### Notes on Processing 
TopoJSON

- If the properties parameter (-p) is left blank then no attributes are included in the TopoJSON.
- The attributes entered using the properties parameter (-p) are CASE SENSITIVE to those in the input file
- To specify the object name within the generated TopoJSON file, prefix the input file with “name=”
		
		~# topojson -p OID_, ISO_A2, ISO_A3, ADMIN, POP_EST 
			-o output.json -- admin0=input.json/input.shp

Simplification

- TopoJSON includes a parameter for polygon [simplification](http://bost.ocks.org/mike/simplify/) using the Visvalingam algorithm.

		~# topojson -p OID_, ISO_A2, ISO_A3, ADMIN, POP_EST 
			--simplify-proportion 0.8 -o output.json input.json/input.shp


## D3js Twine API Usage
The D3js Twine API is designed on the principals of method chaning and [d3.selections](http://bost.ocks.org/mike/selection/), following the [reusable pattern](http://bost.ocks.org/mike/chart/) with a minor modification in the binding of the div element to the DOM so that it is hidden to the user.

### Adding a Map
To initialise a map call the **_d3.twine.map_** function which accepts the div element by which to bind the SVG to the DOM.

- **_Options_**: The div id in which to bind the SVG to the DOM.

		// Creating an instance, passing the div id
		var myMap = d3.twine.map("#map");

- **_Note_**: The _d3.twine.map_ function accepts a [d3.selection](http://bost.ocks.org/mike/selection/) in which to bind the SVG to the DOM, however this is handled within _d3.twine.map_ and hidden from the user.

		/* d3.twine.map, line 36 */
		// Bind svg to DOM using d3 selection.
		exports(d3.select(id));

### Update the Map
The **_.update()_** function is required to apply the changes to the SVG that are **_not_** boolean.

	// Creating an instance, passing the div id
	var myMap = d3.twine.map("#map");
	// Update changes
	myMap
		.width(800)
		.update();	

- **_Note_**: The _.update()_ function can only be called after the map is initialised.

		/* FAIL! */
		var myMap = d3.twine.map("#map").height(400)
			.update();
		
		/* PASS! */
		var myMap = d3.twine.map("#map").height(400);
			myMap.update();

### Accessing the API
The _d3.twine.map_ options are accessible using [method chaining](http://en.wikipedia.org/wiki/Method_chaining).

	// Declare the map
	var myMap = d3.twine.map("#map")
		.height(600)
		.tooltip(true)
		.region("PK");
		
	// Update changes
	myMap
		.update();	
		
### Getter-Setter
Functions act as **_Get_** methods when no arguments are passed and **_Set_** methods when arguments are.

	// Set tooltip
	myMap.tooltip(true);
	// Get tooltip
	console.log(myMap.tooltip); //returns true

	/* Meanwhile, in d3.twine.map */
	//Get/Set tooltip()
	exports.tooltip = function(_) {
		if (!arguments.length) return tooltip;
		tooltip = _;
		return exports;
	};
	
### Region
The region of the map can be specified using a string.

- **_Options_**: Text string of any region in the list ("world", "africa", "americas", "asia", "europe", "north america", "oceana").
		
		// Zoom to Europe
		myMap.region("europe").update();
		
- **_Options_**: Or any [ISO Alpha 2](http://www.nationsonline.org/oneworld/country_code_list.htm) code.

		// Zoom to Pakistan
		myMap.region("PK").update();
		
### Next...?
The current implementation supports a specific data layer _("admin")_, this will be expanded to accept various data sources with a generic API abstracted from the user.

There are many areas to improve the API, pull requests welcome!

##### Example
#### [B.lock #6009045](http://bl.ocks.org//thefitzpaddy/6009045)
To view the result of this repo visit b.lock [#6009045](http://bl.ocks.org//thefitzpaddy/6009045)!
