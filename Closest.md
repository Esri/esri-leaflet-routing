# Closest Destination


To simplify the closest facility API we can optimize our API wrapper to `origin()` (incidents) and `destination` (facilities) and always use the `esriNATravelDirectionToFacility` which will route from the origins (incidents) to destinations (facilities). This still covers the following use cases...

* Route closest ambulance from dispatch to accident - 3 origins (dispatch) 1 destination (accident)
* Route ambulance from accident to closest hospital - 1 origin (accident) X destinations (hospitals)

This will resolve the confusion over what is a facility and what is an incident. For example should I make my accident an incident or a facility? Should i set the travel direction to or from facilities? What if my facility isn't a place?

This also avoids confusion because depending on travel direction parameters like `Cutoff_WalkTime` can be declared on either the incidents or facilities. We only have to do this once.

**Simple**

```js
new L.esri.Routing.ClosestFacility().origin(latlng).destination(latlng).destination(latlng).addTo(map);
```

**Names and Options**

```js
var hospital1 = new L.esri.routing.Destination(latlng, 'Hospital 1').allowUTurn(false);
var hospital2 = new L.esri.routing.Destination(latlng, 'Hospital 2').allowUTurn(false);
var hospital3 = new L.esri.routing.Destination(latlng, 'Hospital 3').allowUTurn(false);
var accident = new L.esri.routing.Origin('Accident').closest(2).maxTravelTime(60); // choose the 2 closest destinations but ignore destinations that are further than 60 minutes away

var route = new L.esri.routing.ClosestDestination().origin(Accident).destination(hospital1).destination(hospital2).destination(hospital2).addTo(map);
```

**Feature Layer as Destination**

```js
var hospitals = L.esri.Tasks.Query(featureLayerURL).where('hasEmergencyRoom=1');
var accident = new L.esri.routing.Origin('Accident').closest(2).maxTravelTime(60); // choose the two closest destinations and ignore destinations over 60 minutes away.

var route = new L.esri.routing.ClosestDestination().origin(Accident).destination(hospitals).addTo(map);
```

**Options**

```js
var hospitals = L.esri.Tasks.Query(featureLayerURL).where('hasEmergencyRoom=1');
var accident = new L.esri.routing.Origin('Accident').closest(2).maxTravelTime(60); // choose the two closest destinations and ignore destinations over 60 minutes away.

var route = new L.esri.routing.ClosestDestination({
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
var accident = new L.esri.routing.Origin('Accident').closest(2).maxTravelTime(60); // choose the two closest destinations and ignore destinations over 60 minutes away.

var route = new L.esri.routing.ClosestDestination({
    route: stylingFunction, // path options or function
    barrierStyle: stylingFunction, // path options or function
    markerStyle: layerFunction // called for each origin/destination to place it ont he map
}).origin(Accident).destination(hospitals).barrier(marathonRoute).addTo(map);
```