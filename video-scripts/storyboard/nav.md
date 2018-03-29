
# Geo and Nav

First set your location:



Add a button to call the API:

    <paper-button raised class="green" on-click="getLocation">
                <iron-icon icon="maps:my-location"></iron-icon>
                Locate Me
            </paper-button>

            <div>
                [[ positionInfo ]]
            </div>

And some backing code:

    getLocation() {
                navigator.geolocation.getCurrentPosition( 
                    (pos) => {
                        let coords = pos.coords;
                        let info = `
                            Lat: ${coords.latitude} 
                            Lng: ${coords.longitude}
                            Alt: ${coords.altitude} 
                            Accuracy: ${coords.accuracy}
                        `;
                        this.set('positionInfo', info);
                    }
                )
            }    

## Working with Maps

Add google maps to the page:

    <google-map id='gmap' api-key="AIzaSyDMFQM5PWXsokAe7BfKSNKD_KJz5uWzyEk">

    </google-map>

Add a button to find our current location:

    <paper-button raised class="indigo" on-click="getLocation">
        <iron-icon icon="maps:my-location"></iron-icon>
            Locate Me
    </paper-button>

Move to current location:

    let mapsApi = this.$.gmap;
    mapsApi.latitude = coords.latitude;
    mapsApi.longitude = coords.longitude;
    mapsApi.zoom = 15;

Working with Markers:

    var marker = new google.maps.Marker({
                position: {lat: coords.latitude, lng: coords.longitude},
                title: 'Here you are!',
                label: 'X',
                map: mapsApi.map
    });    

Add an info window:

        let richContent = `<div><h1>Welcome!</h1><pre>${info}</pre></div>`;
        var infowindow = new google.maps.InfoWindow({
                    content: richContent
                });
                marker.addListener('click', function() {
                    infowindow.open(mapsApi.map, marker);
                });

##  Location Changes over Time

I'll add a bunch of markup to do polymer mapsy things:

    <div class="card">

        <h1>Find Coffee</h1>

        <google-map id='coffeeMap' api-key="AIzaSyDMFQM5PWXsokAe7BfKSNKD_KJz5uWzyEk" map="{{map}}">
                <template is="dom-repeat" items="{{results}}" as="marker">
                        <google-map-marker latitude="{{marker.latitude}}"
                                        longitude="{{marker.longitude}}"
                                        on-click="goToCoffee">
                            <h2>{{marker.name}}</h2>
                            <p>{{marker.formatted_address}}</p>
                            
                        </google-map-marker>
                </template>

        </google-map>
        <google-map-directions id='gmd' map="[[map]]"
                    api-key="AIzaSyDMFQM5PWXsokAe7BfKSNKD_KJz5uWzyEk">
        </google-map-directions>
        <google-map-search id='gms' map="[[map]]" results="{{results}}">

        </google-map-search>

        <paper-button raised class="pink" on-click="findCoffee">
            <iron-icon icon="maps:local-cafe"></iron-icon>
            Find Coffee
        </paper-button>
    </div>


Then I'll implement "Find Coffee" nearby. I'll start by implementing my current location, but watching:

    findCoffee() {

        var currentPosMarker;
        let mapsApi = this.$.coffeeMap;

        navigator.geolocation.watchPosition( (pos) => {

            if (!currentPosMarker) {

                mapsApi.latitude = pos.coords.latitude;
                mapsApi.longitude = pos.coords.longitude;
                mapsApi.zoom = 15;

                currentPosMarker = new google.maps.Marker({
                    position: { lat: pos.coords.latitude,
                    lng: pos.coords.longitude},
                    label: 'U',
                    map: mapsApi.map
                });

               
                
            } else {
                var latlng = new google.maps.LatLng(pos.coords.latitude, pos.coords.longitude);
                currentPosMarker.setPosition(latlng);
            }
        }, (err) => {
            console.error(err);
        });
    }                   

THen I'll add the coffee house stuff (after marker creation):

        setTimeout( () => {
                    var mapsSearch = this.$.gms;
                    mapsSearch.types = "cafe";
                    mapsSearch.query = "coffee";
                    mapsSearch.search();
                }, 1000);

                