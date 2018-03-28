
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
                            Lat: ${coords.latitude} Lng: ${coords.longitude}
                            Alt: ${coords.altitude} Accuracy: ${coords.accuracy}
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

    

