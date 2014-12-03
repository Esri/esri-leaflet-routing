# Esri Leaflet Routing

The Esri Leaflet Geocoder is a small series of API helpers and UI controls to interact with the ArcGIS Online routing and directions services.

**Currently Esri Leaflet Routing is in the design stage. Please feel free to propose new ideas or implimentation in the [issues](/issues)**

## Proposed Capibilities

Esri Leaflet Routing should launch with support for the following routing features:

### Routing 

* Sync http://resources.arcgis.com/en/help/arcgis-rest-api/#/Route_service_with_synchronous_execution/02r300000036000000/
* Async http://resources.arcgis.com/en/help/arcgis-rest-api/#/Route_service_with_asynchronous_execution/02r300000275000000/

Ideally Esri Leaflet would handle switching between the 2 modes automatically once a certain number of features is reached. It should also fully support optimizing the route by changing the order of the stops and support all the features in teh `stops` array like TimeWindows, CurbApproches and named routes, drive/walk and barriers

### Closest Facility

* Sync http://resources.arcgis.com/en/help/arcgis-rest-api/#/Closest_Facility_service_with_synchronous_execution/02r3000000n7000000/
* Async http://resources.arcgis.com/en/help/arcgis-rest-api/#/Closest_Facility_service_with_asynchronous_execution/02r30000020n000000/

Ideally Esri Leaflet would handle switching between the 2 modes automatically once a certain number of features is reached. Esri Leaflet Routing should also support all the input types for facilities as well as all options availavle on the REST APIs

### Drive Times (Service Area)

* http://resources.arcgis.com/en/help/arcgis-rest-api/#/Service_Area_service_with_synchronous_execution/02r3000000n2000000/
* http://resources.arcgis.com/en/help/arcgis-rest-api/#/Service_Area_service_with_asynchronous_execution/02r3000000n0000000/

Ideally Esri Leaflet would handle switching between the 2 modes automatically once a certain number of features is reached. Should support all features like barriers, ect...

### Fleet Routing (Vehicle Routing Problem)

* http://resources.arcgis.com/en/help/arcgis-rest-api/#/Vehicle_Routing_Problem_service/02r3000000n4000000/

Quite possibly Esris largest and most complex API. This optimized large fleets of vehicles across multupile desitionataion and can handle things like cargo volumes, ect.

Probally best to leave this off an inital version for to save some sanity.

### Traffic Layer

* http://resources.arcgis.com/en/help/arcgis-rest-api/#/Traffic_service/02r3000000n6000000/

The traffic layer would really just be an instance of `L.esri.DynamicMapLayer` with a predefined URL.

## Proposed API

### Routing

**A Simple Route**

```js
//start prepends to internal list of stops
//end appends to internal list of stops
var myRoute = new L.esri.Routing.Route().start(latlng).end(latlng).addTo(map);
```

**Styling the Route**

```js
var myRoute = new L.esri.Routing.Route({
    routeStyle: // path options or function,
    startMarker: // icon or function returning layer
    endMarker: // icon or function returning layer
    stopMarker: // icon or function returning layer
}).start(latlng).end(latlng).addTo(map);
```

**Popups on the Route**

```js
var myRoute = new L.esri.Routing.Route()
                .start(latlng)
                .bindStartPopup(function)
                .end(latlng)
                .bindEndPopup(function)
                .stop(latlng)
                .bindStopPopup(function)
                .addTo(map);
```

**Barriers**

```js
var myRoute = new L.esri.Routing.Route(
    {
    barrierStyle: // path options or function
})
.start(latlng)
.end(latlng)
.barrier(geojson)
.addTo(map);
```

**Advanced Features**

```js
var start = new L.esri.Routing.Stop()
                .allowUTurn(false) // allow a u turn at this stop
                .approchFrom('right'); // only approch this stop from the right

var myRoute = new L.esri.Routing.Route()
                .start(start)
                .end(latlng)
                .addTo(map);
```

**Accessing Raw Route Data**

```js
// like other tasks `Route` has a `run()` method for execution
var myRoute = new L.esri.Routing.Route().start(latlng).end(latlng).run(function(error,route, directions, stops, barriers){
    // do stuff
});
```

**Multiple Stops**

```js
var myRoute = new L.esri.Routing.Route().start(latlng).stop(latlng).end(latlng).addTo(map);
```

**Optimize Multiple Stops**

```js
// if optimize is true start() and end() will preserve first last stops
var myRoute = new L.esri.Routing.Route({
    optimize: true,
}).start(latlng).stop(latlng).stop(latlng).end(latlng).addTo(map);
```

**Live Update**

```js
var myRoute = new L.esri.Routing.Route().start(latlng).end(latlng).end(latlng).addTo(map);

map.on('click', function(e){
    myRoute.stop(e.latlng); // automatically add a new stop and redraw everything! This is cool but what if you want to bind a click handler on your route?
});
```

**Events**

```js
var myRoute = new L.esri.Routing.Route().start(latlng).end(latlng).end(latlng).addTo(map);

myRoute.on('click', function(e){
    // how do you know if you clicked on a route or a marker?
});

myRoute.on('routeclick', function(e){
    // ok which route? More work to propagate events.
});
```

## Service Areas

divide into 2 classes to seperate walking/driving.breaks are defined with either `distance(meters)` or time(minutes) and can be chained.

**WalkArea**

```js
var area = new L.esri.Routing.WalkArea().origin(latlng).distance(5).distance(10).distance(15).addTo(map);
```

**DriveArea**

```js
var area = new L.esri.Routing.DriveArea().origin(latlng).distance(5).distance(10).distance(15).addTo(map);
```

**Barriers**

```js
var area = new L.esri.Routing.DriveArea().origin(latlng).distance(5).barrier(geojson).addTo(map);
```

**Styling**

```js
var area = new L.esri.Routing.DriveArea({
    style: stylingFunction, // path options or function
    barrierStyle: stylingFunction // path options or function
}).origin(latlng).distance(5).barrier(geojson).addTo(map);
```

**Access Raw Polygons**

```js
var area = new L.esri.Routing.DriveArea().origin(latlng).distance(5).distance(10).run(function(error, areas, barriers){
    // console.log(do stuff)
});
```

```js
var area = new L.esri.Routing.DriveArea().origin(latlng).distance(5).distance(10).run(function(error, areas, barriers){
    // console.log(do stuff)
});
```

## Closest Facility

To Simplify the closest facility API we can optimize out API wrapper to `origin()` (incidents) and `destination` (facilities) and always use the `esriNATravelDirectionToFacility` which will route from the origins (incidents) to destinations (facilities). This still covers the following use cases...

* Route closest ambulence from dispatch to accident - 3 origins (dispatch) 1 destination (accident)
* Route ambulence from accident to closest hospital - 1 origin (accident) X destinations (hospitals)

The benifit to this approch fixes some confusion in the API were its hard to figure out what is a facility and what is an incident, e.g. should I make my accident an incident or a facility? should i set the travel direction to or from facilities? what if my facility isnt a place?

This also avoids confusion becuase depending on travel direction params like `Cutoff_WalkTime` can be declared on either the incidents or facilities. We only have to do this once.

**Simple**

```js
new L.esri.Routing.ClosestFacility().origin(latlng).destination(latlng).destinstaion(latlng).addTo(map);
```

**Names and Options**

```js
var hospital1 = new L.esri.routing.Destination(latlng, 'Hospital 1').allowUTurn(false);
var hospital2 = new L.esri.routing.Destination(latlng, 'Hospital 2').allowUTurn(false);
var hospital3 = new L.esri.routing.Destination(latlng, 'Hospital 3').allowUTurn(false);
var accident = new L.esri.routing.Origin('Accident').closest(2).maxTravelTime(60); // choose the 2 closest destinations but ignore destinations that will take over 60 minutes to get to

var route = new L.esri.routing.ClosestFacility().origin(Accident).destination(hospital1).destination(hospital2).destination(hospital2).addTo(map);
```

**Feature Layer as Destination**

```js
var hospitals = L.esri.Tasks.Query(featureLayerURL).where('hasEmergencyRoom=1');
var accident = new L.esri.routing.Origin('Accident').closest(2).maxTravelTime(60); // choose the 2 closest destinations but ignore destinations that will take over 60 minutes to get to

var route = new L.esri.routing.ClosestFacility().origin(Accident).destination(hospitals).addTo(map);
```

**Options**

```js
var hospitals = L.esri.Tasks.Query(featureLayerURL).where('hasEmergencyRoom=1');
var accident = new L.esri.routing.Origin('Accident').closest(2).maxTravelTime(60); // choose the 2 closest destinations but ignore destinations that will take over 60 minutes to get to

var route = new L.esri.routing.ClosestFacility({
    route: stylingFunction, // path options or function
    barrierStyle: stylingFunction, // path options or function
    markerStyle: layerFunction // called for each origin/destination to place it ont he map
}).origin(Accident).destination(hospitals).addTo(map);
```

**Barriers**

```js
var marathonRoute = {
    type: 'Polygon',
    coordinates: [...]
}

var hospitals = L.esri.Tasks.Query(featureLayerURL).where('hasEmergencyRoom=1');
var accident = new L.esri.routing.Origin('Accident').closest(2).maxTravelTime(60); // choose the 2 closest destinations but ignore destinations that will take over 60 minutes to get to

var route = new L.esri.routing.ClosestFacility({
    route: stylingFunction, // path options or function
    barrierStyle: stylingFunction, // path options or function
    markerStyle: layerFunction // called for each origin/destination to place it ont he map
}).origin(Accident).destination(hospitals).barrier(marathonRoute).addTo(map);
```

### Fleet Routing (Vehicle Routing Problem)

@TODO

## Traffic Layer

@TODO

## Open Questions

* Could we do something more interesting with the traffic layer? Automatic popups? Custom icons? Expose individual traffic layers/incidents with `L.esri.FeatureLayer`?
* How much/little should we stray from the REST APIs and or existing implimentations? This was pretty hotly debated in https://github.com/Esri/esri-leaflet/issues/232 and also over what to call the `where` param in `Query`.
* Should this api also have live updates? For example if you add a route to a map should you be able to add stops later with `stop()` and have it automatically update? This might be tough from a event handler standpoint because we would have to copy events from the old routes to the new routes but it could be doable.
