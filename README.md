# Esri Leaflet Routing

The Esri Leaflet Routing is a small series of API helpers and UI controls to interact with the ArcGIS Online routing and directions services.

**Currently Esri Leaflet Routing is in the design stage. Please feel free to propose new ideas or implementation in the [issues](https://github.com/Esri/esri-leaflet-routing/issues)**

## Proposed Capabilities

Esri Leaflet Routing should launch with support for the following routing features:

### Routing

http://resources.arcgis.com/en/help/arcgis-rest-api/#/Route_service_with_synchronous_execution/02r300000036000000/

* L.esri.Routing.Route - Build a route
* L.esri.Routing.Stop - Represents a stop in the route
* L.esri.Routing.Barrier - Represents a barrier that prohibits or slows travel
* L.esri.Routing.TravelMode - Represents a set of restrictions to place on travel

### Closest Facility

http://resources.arcgis.com/en/help/arcgis-rest-api/#/Closest_Facility_service_with_synchronous_execution/02r3000000n7000000/

* `L.esri.Routing.Route` - Build a route
* `L.esri.Routing.Origin` - Represents places to route from
* `L.esri.Routing.Destination` - Represents places to route to
* `L.esri.Routing.Barrier` - Represents a barrier that prohibits or slows travel
* `L.esri.Routing.TravelMode` - Represents a set of restrictions to place on travel

### Drive Times (Service Area)

http://resources.arcgis.com/en/help/arcgis-rest-api/#/Service_Area_service_with_synchronous_execution/02r3000000n2000000/

* `L.esri.Routing.Route` - Build a route
* `L.esri.Routing.Place` - Represents places to start routing from
* `L.esri.Routing.Barrier` - Represents a barrier that prohibits or slows travel
* `L.esri.Routing.TravelMode` - Represents a set of restrictions to place on travel

### Fleet Routing (Vehicle Routing Problem)

http://resources.arcgis.com/en/help/arcgis-rest-api/#/Vehicle_Routing_Problem_service/02r3000000n4000000/

Quite possibly Esri's largest and most complex API. This optimizes large fleets of vehicles across multiple destinations and can handle restrictions such as cargo volumes, turn restrictions, operating costs, etc.

Probably best to support this in later versions of the library.

### Traffic Layer

* http://resources.arcgis.com/en/help/arcgis-rest-api/#/Traffic_service/02r3000000n6000000/

The traffic layer would really just be an instance of `L.esri.DynamicMapLayer` with a predefined URL.

Probably best to support this in later versions of the library and show consuming it in examples.

## Proposed API

### Routing

See [L.esri.Routing.Route](Route.md)...

## Service Areas

See [L.esri.Routing.TravelArea](TravelArea.md)...

## Closest Facility

See [L.esri.Routing.ClosestDestination](Closest.md)...

### Fleet Routing (Vehicle Routing Problem)

@TODO

## Traffic Layer

@TODO

## Open Questions

* Could we do something more interesting with the traffic layer? Automatic popups? Custom icons? Expose individual traffic layers/incidents with `L.esri.FeatureLayer`?