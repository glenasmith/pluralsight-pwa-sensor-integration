# Offline


## Testing for Online

First we'll create our property:

    static get properties() {
          return {
              online: {
                  type: Boolean,
                  notify: true,
                  value: false
              },
          };
    }

Then we'll create some icons to handle the display:

    <span>
      <iron-icon id="onlineIcon" icon="cloud" hidden$="[[!online]]"></iron-icon>
      <iron-icon id="offlineIcon" icon="cloud-off" hidden$="[[online]]"></iron-icon>
      <paper-tooltip for="onlineIcon">Online</paper-tooltip>
      <paper-tooltip for="offlineIcon">Offline</paper-tooltip>
    </span>



Add some code to our "ready()" routine - after super.ready();

There are [good Mozilla examples](https://developer.mozilla.org/en-US/docs/Web/API/NavigatorOnLine/Online_and_offline_events):



    this.set('online', navigator.onLine);

    window.addEventListener('online',  () => this.set('online', false));
    window.addEventListener('offline', () => this.set('online', true));

## Check Network Speed

We'll need a property to hold the icon:

    speedIcon: {
        type: String,
        value: ''
    }

And we'll need the matching markup:

    <iron-icon icon="[[ speedIcon ]]" hidden$="[[!online]]"></iron-icon>

Check for the presence of the property in the ready() routine:

    if ('connection' in navigator) {
        this.updateSpeedIcon(navigator.connection);
    }


Add a routine to inspect the enum:

    updateSpeedIcon(networkInfo) {
        switch(networkInfo.effectiveType) {
                case 'slow-2g':
                case '2g': 
                    this.set('speedIcon', 'social:sentiment-dissatisfied');
                    break;
                case '3g':
                    this.set('speedIcon', 'social:sentiment-neutral');
                    break;
                case '4g':
                    this.set('speedIcon', 'social:sentiment-very-satisfied');
                    break;
              }
    }

Inside the `navigator.connection` check, add an event listener to cope with realtime changes:

    navigator.connection.addEventListener('change', (evt) => {
              this.updateSpeedIcon(evt.target);
    });

## Handling Static Assets

You'll find browser sensing code in the root index.html page:

    window.Polymer = {rootPath: '/'};

    // Load and register pre-caching Service Worker
    if ('serviceWorker' in navigator) {
    window.addEventListener('load', function() {
        navigator.serviceWorker.register('service-worker.js', {
        scope: Polymer.rootPath,
        });
    });

Then we browse in build/*/service-worker.js to see generated fetch code circa line 210.

    self.addEventListener('fetch', function(event) {


## SSL and going live

Fire up:
    npm run build
    npm run https

Then we should be fine to head to

## Going Offline with Dynamic App Data

Add link to ss-app.html:

    <link rel="lazy-import" href="gallery/ss-gallery.html">
    (and associated router changes)

Add the tag for firebase:

    <firebase-query app="{{fbApp}}"
        path="/photos"
        data="{{photoList}}">
    </firebase-query>

And some backing properties:
    static get properties() {
        return {
            fbApp: Object,
            photoList: {
                type: Object
            }
            
        };
    }

And then the gallery:

    <sl-gallery>
        <template is="dom-repeat" items="{{photoList}}" as="photoObj">

            <sl-gallery-image src="{{photoObj.url}}"></sl-gallery-image>

        </template>
    </sl-gallery>

Put in some local storage action:

    <app-localstorage-document key='photoDocument' data="{{cachedPhotoList}}"></app-localstorage-document>

And hook that up to your images:

    <template is="dom-repeat" items="{{cachedPhotoList}}" as="photoObj">

Finally, wire up some code to do the sync:

    photoList: {
        type: Object,
        observer: 'photoDataChanged'
    },
    cachedPhotoList: {
        type: Object,
        notify: true
    }

And implement the routine:

    photoDataChanged(newData, oldData) {
        if (newData && newData.length > 0) {
            this.set('cachedPhotoList', newData);
        }
    }



## Add to Home Screen

Good docs [oneline](https://developers.google.com/web/fundamentals/web-app-manifest/) here. Including [App Install Banners](https://developers.google.com/web/fundamentals/app-install-banners/) if that's your thing.


