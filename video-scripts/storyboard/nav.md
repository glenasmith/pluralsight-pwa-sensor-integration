
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

                
And handle the cancel of the timer:

    <paper-button raised class="cyan" on-click="cancelFindCoffee" disabled="[[!watchId]]">
        <iron-icon icon="maps:local-cafe"></iron-icon>
        I'm done
    </paper-button>

With matching method:

    cancelFindCoffee() {
                let watchId = this.get('watchId');
                navigator.geolocation.clearWatch(watchId);
                this.set('watchId', 0);
            }


## Error handling

Test for the presence of the API itself:

    if (!navigator.geolocation) {
        alert("Sorry, Geo is not supported on this device");
        return;
    }

Update getLocation with a second arg:

    (err) => {
        switch(err.code) {
            case 0:  // unknown error
                console.log(err);
                alert("Unknown Geo Internal Error");
                break;
            case 1: // no permission yet
                alert("Please allow permission to use geo services");
                break;
            case 2: // no signal
            case 3: // timeout on geo lookup
                alert("No GPS signal available at this time");
                break;
                
        }
    }




 ## Compass stuff

 I'll add a card for it:

    <div class="card">
            <h1>Simple Compass</h1>

            <h3>[[ currentDirection ]]</h3>

            <pre>
    [[ deviceOrientation ]]                
            </pre>

            <paper-button raised class="green" on-click="startCompass">
                <iron-icon icon="maps:navigation"></iron-icon>
                Start Compass
            </paper-button>


        </div>       

Then implement the callback:

startCompass() {

                if (window.DeviceOrientationEvent) {
                    window.addEventListener('deviceorientation', (evt) => {
                        console.log('Hope', evt);
                        let orientation = ` Alpha: ${evt.alpha} \n Beta: ${evt.beta} \n Gamma: ${evt.gamma}`;
                        this.set('deviceOrientation', orientation)
                    });
                };                

Put in the compass stuff:

    let direction = '';
    let deg = evt.alpha;
    if (deg < 15) {
        direction = 'North'
    } else if (deg < 60) {
        direction = 'North East'
    } else if (deg < 105) {
        direction = 'East'
    } else if (deg < 150) {
        direction = 'South East'
    } else if (deg < 195) {
        direction = 'South'
    } else if (deg < 240) {
        direction = 'South West'
    } else if (deg < 285) {
        direction = 'West'
    } else if (deg < 320) {
        direction = 'North West'
    } else {
        direction = 'North'
    }
    this.set('currentDirection', direction);
                                        