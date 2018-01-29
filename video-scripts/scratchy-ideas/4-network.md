# Network module

* Handling Online / Offline
* Network Throughput
* Serviceworker for offline caching
* Working with offline data / localstorage? / firebase?
* Handling Sync when you get back online


## Handling Offline Sync

[This article](https://github.com/firebase/polymerfire/issues/115) was helpful for strategy:

> Firebase currently lacks built-in support for offline-to-online synchronization and conflict resolution. So, you must first design your own conventions for synchronization and conflict resolution. If you are willing to design your own synchronization and conflict resolution schemes (or if you don't care that a client returning online will always blindly overwrite values in Firebase), then one reasonable strategy is:
>
> 1. Observe changes to a data object that should be synchronized with Firebase.
> 1. When offline, cache a serialized version of each such change in a local storage layer such as localStorage or IndexedDB.
> 1. Re-play all recorded changes when the user returns online so that Firebase attempts to save it.
> 1. Remove the cached, serialized version of each change as it successfully saves to Firebase.
>
> Please note that without any conventions for synchronization and conflict resolution, the above approach can be problematic when applied to data that multiple users might be editing.

