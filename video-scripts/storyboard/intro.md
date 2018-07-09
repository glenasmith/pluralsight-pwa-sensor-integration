# Intro Script

First we'll see an empty file:

Add the logo to the page:

    #logo {
        width: 100%;
    }

    <img id="logo" src="./images/sights-sounds-logo.svg"/>

Import the l2t-paper-rating:

    <link rel="import" href="../../bower_components/l2t-paper-rating/l2t-paper-rating.html">

Add the star component to the page:

    <l2t-paper-rating rating="{{ starRating }}"></l2t-paper-rating>

Basic property syntax:

    starRating: Number

What is this magic syntax:

    starRating: {
        type: Number,
        value: 5
    },

Let's add a reset button:

    <paper-button raised class="indigo" on-click="resetRating">
            <iron-icon icon="icons:star"></iron-icon>
            Reset Rating
    </paper-button>

And code behind to do the reset:

    resetRating() {
        this.set('starRating', 5);
    }

    