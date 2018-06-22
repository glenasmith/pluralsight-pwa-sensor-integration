# Handset Integration

## Battery Manager

Add a property for our battery level (which will be between 0.0 and 1.0).

    batteryLevel: Number

And read the battery status in onReady:

        readBatteryStatus() {
                
                if ('getBattery' in navigator) {
                   navigator.getBattery().then( (batteryManager) => {
                       console.log(batteryManager);
                       this.set('batteryLevel', batteryManager.level);
                       batteryManager.onlevelchange = () => {
                            this.set('batteryLevel', batteryManager.level);
                       }
                   });
                } else {
                    this.set('batteryLevel', 50);
                }

            }

Add a google chart

    <google-chart
                type='gauge'
                data='[[batteryChartData]]'>
                </google-chart>

Watch for value changes:

    batteryChartData: {
                        type: Object,
                        computed: 'computeChartData(batteryLevel)'
                    }

And create the data element for the chart:

    computeChartData(batteryLevel) {
                console.log("Computing based on battery level of ", batteryLevel);
                return [
                    ["Label", "Value"], 
                    ["Battery", batteryLevel * 100]
                ];
            }

Demo it.

Then we can add a bunch more handlers:

    charging: Boolean,
    chargingTimeMinutes: Number,
    dischargingTimeMinutes: Number,

    this.set('charging', batteryManager.charging);
    this.set('chargingTimeMinutes', Number.isFinite(batteryManager.chargingTime) ?     batteryManager.chargingTime / 60 : 0);
    this.set('dischargingTimeMinutes', Number.isFinite(batteryManager.dischargingTime) ? batteryManager.dischargingTime / 60 : 0);

    batteryManager.onchargingchange = () => {
        this.set('charging', batteryManager.charging);
    };
    batteryManager.onchargingtimechange = () => {
        this.set('chargingTimeMinutes', batteryManager.chargingTime / 60);
    };
    batteryManager.ondischargingtimechange = () => {
        this.set('dischargingTimeMinutes', batteryManager.dischargingTime / 60);
    };

And make the display conditional: 

    <div hidden$="[[!charging]]">Charged in [[chargingTimeMinutes]] minutes</div>

    <div hidden$="[[charging]]">Discharged in [[dischargingTimeMinutes]] minutes</div>

 ## Vibration

 Inside our discharge event listener:

        if ('vibrate' in navigator) {
            if (batteryManager.dischargingTime < 30 * 60) {
                navigator.vibrate([100,50,100,50,200]);
            }
        }

## Local Notifications

Start with writing the notification routine:

    showBatteryLifeNotification(minutes) {
        const title = `Battery Low`;
        const options = {
            body: `Only ${minutes} minutes of battery remaining`,
            icon: '/sights-sounds-pwa/images/manifest/icon-512x512.png',
            vibrate: [100,50,100,50,200]
        };
        
        Notification.requestPermission(result => {
            if (result === 'granted') {
                navigator.serviceWorker.ready.then(reg => {
                    reg.showNotification(title, options);
                });
            }
        });
    }

Then invoke it when battery level falls below threshold:

    batteryManager.ondischargingtimechange = () => {
        let minutesLeft = batteryManager.dischargingTime / 60;
        this.set('dischargingTimeMinutes', minutesLeft);
        if (minutesLeft < 30) {
            this.showBatteryLifeNotification(minutesLeft);
        }
    };

Then add a button to trip it right away:

    <paper-button raised class="pink" on-click="notifyBatteryLife" >
        <iron-icon icon="icons:alarm"></iron-icon>
        Notify Battery Life
    </paper-button>

And a matching click handler to invoke the notify:

    notifyBatteryLife() {
        const minutesLeft = this.get('dischargingTimeMinutes');
        this.showBatteryLifeNotification(minutesLeft);
    }

## Where to from here

