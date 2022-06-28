# Proof of concept using Leaflet.markercluster with vue-leaflet in Vue 3

A note of warning: this works by patching leaflet.markercluster in your node_modules folder using [patch-package](https://www.npmjs.com/package/patch-package). Basically it makes it use ESM Exports rather than depending on and extending a global Leaflet instance.

Inspired by jperelli/vue2-leaflet-markercluster
