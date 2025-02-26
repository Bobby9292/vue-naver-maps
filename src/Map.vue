<template>
  <div id="vue-naver-maps" :style="mapStyle">
    <slot> </slot>
  </div>
</template>

<script>
import * as _ from "../lib";

export default {
  name: "Map",
  props: {
    width: Number,
    height: Number,
    markersData: Array,
    mapOptions: {
      type: Object,
      required: true,
    },
    initLayers: Array,
  },
  data() {
    return {
      mapStyle: {
        width: `${this.width}px`,
        height: `${this.height}px`,
      },
      /**
       * @type {naver.maps.Map}
       */
      map: null,
      styleMapLoaded: false,
    };
  },
  watch: {
    mapOptions: {
      deep: true,
      handler(newValue) {
        this.map.setOptions(newValue);
      },
    },
    initLayers: {
      deep: true,
      handler(newValue) {
        const settings = {};
        const layers = {
          BACKGROUND: "bg",
          BACKGROUND_DETAIL: "ol",
          BICYCLE: "br",
          CADASTRAL: "lp",
          CTT: "ctt",
          HIKING_TRAIL: "ar",
          PANORAMA: "pr",
          POI_KOREAN: "lko",
          TRANSIT: "ts",
          KOREAN: "lko",
          ENGLISH: "len",
          CHINESE: "lzh",
          JAPANESE: "lja",
        };
        settings.mapTypes = new window.naver.maps.MapTypeRegistry({
          normal: window.naver.maps.NaverStyleMapTypeOption.getNormalMap({
            overlayType: newValue.map((layer) => layers[layer]).join("."),
          }),
          satellite: window.naver.maps.NaverStyleMapTypeOption.getSatelliteMap({
            overlayType: this.initLayers
              .map((layer) => layers[layer])
              .join("."),
          }),
          hybrid: window.naver.maps.NaverStyleMapTypeOption.getHybridMap({
            overlayType: this.initLayers
              .map((layer) => layers[layer])
              .join("."),
          }),
          terrain: window.naver.maps.NaverStyleMapTypeOption.getTerrainMap({
            overlayType: this.initLayers
              .map((layer) => layers[layer])
              .join("."),
          }),
        });
        this.setOptions("mapTypes", settings.mapTypes);
      },
    },
  },
  methods: {
    /* Normal Method */
    /**
     * @param typeId {string}
     * @returns this
     */
    setLayerTypeId(typeId) {
      this.map.mapTypes.setLayerTypeId(typeId);
      this.map.refresh();
      return this;
    },
    /**
     * @param name {string}
     * @param elementOrZIndex {HTMLElement | number}
     */
    addPane(name, elementOrZIndex) {
      this.map.addPane(name, elementOrZIndex);
      return this;
    },
    /**
     * delete this map which includes all event and dom element.
     * *** Warning! ***
     *  This method will be delete the map object of this component.
     */
    destroy() {
      this.map.destroy();
      this.map = null;
      this.$destroy();
    },
    /**
     * @param {naver.maps.Bounds | naver.maps.BoundsLiteral | naver.maps.ArrayOfCoords | naver.maps.ArrayOfCoordsLiteral} bounds
     * @param {number} margin
     */
    fitBounds(bounds, margin) {
      this.map.fitBounds(bounds, margin);
      return this;
    },
    /**
     * @param coord {naver.maps.Coord | naver.maps.CoordLiteral}
     * @param zoom {number} optional
     * @param transitionOptions {naver.maps.TransitionOptions} optional
     * @returns this
     */
    morph(coord, zoom, transitionOptions) {
      if (zoom) {
        if (transitionOptions) this.map.morph(coord, zoom, transitionOptions);
        else this.map.morph(coord, zoom);
      } else this.map.morph(coord);
      return this;
    },
    /**
     * @param {naver.maps.Coord | naver.maps.CoordLiteral} coord
     * @param {naver.maps.TransitionOptions} transitionOptions
     */
    panTo(coord, transitionOptions) {
      this.map.panTo(coord, transitionOptions);
    },
    /**
     * @param {naver.maps.Bounds | naver.maps.BoundsLiteral} bounds
     * @param {naver.maps.TransitionOptions} transitionOptions
     * @param {number} margin
     */
    panToBounds(bounds, transitionOptions, margin) {
      this.map.panToBounds(bounds, transitionOptions, margin);
    },
    /**
     * @param {number} x
     * @param {number} y
     * @returns this
     */
    panBy(x, y) {
      this.map.panBy(new window.naver.maps.Point(x, y));
      return this;
    },
    /**
     * @param noEffect {boolean}
     * @returns this
     */
    refresh(noEffect = false) {
      this.map.refresh(noEffect);
      return this;
    },
    /**
     * @param name {string}
     * @returns this
     */
    removePane(name) {
      this.map.removePane(name);
      return this;
    },
    /**
     * @returns this
     * @param coord {naver.maps.Coord | naver.maps.CoordLiteral}
     * @param zoom {number}
     */
    updateBy(coord, zoom) {
      this.map.updateBy(name);
      return this;
    },
    /**
     * @returns this
     * @param deltaZoom {number}
     * @param zoomOrigin {naver.maps.Coord | naver.maps.CoordLiteral} default is center
     * @param effect {boolean}
     */
    zoomBy(deltaZoom, zoomOrigin = undefined, effect = false) {
      if (zoomOrigin) this.map.zoomBy(deltaZoom, zoomOrigin, effect);
      else this.map.zoomBy(deltaZoom);
      return this;
    },

    /* Getter Methods */

    /**
     * @returns {naver.maps.Bounds}
     */
    getBounds() {
      return this.map.getBounds();
    },
    /**
     * the center coordinates of a map
     * @returns {naver.maps.Coord}
     */
    getCenter() {
      return this.map.getCenter();
    },
    /**
     * The result of converting the map's center coordinates to the world coordinates.
     * @returns {naver.maps.Coord}
     */
    getCenterPoint() {
      return this.map.getCenterPoint();
    },
    /**
     * @returns {HTMLElement}
     */
    getElement() {
      return this.map.getElement();
    },
    /**
     * returns type id of this map.
     * @returns {string}
     */
    getMapTypeId() {
      return this.map.getMapTypeId();
    },
    /**
     * returns options of this map.
     * @param key {string}
     * @returns any
     */
    getOptions(key = undefined) {
      return key ? this.map.getOptions(key) : this.map.getOptions();
    },
    /**
     * @returns {naver.maps.MapPanes}
     */
    getPanes() {
      return this.map.getPanes();
    },
    /**
     * @returns {naver.maps.Projection}
     */
    getPrimitiveProjection() {
      return this.map.getPrimitiveProjection();
    },
    /**
     * @returns {naver.maps.MapSystemProjection}
     */
    getProjection() {
      return this.map.getProjection();
    },
    /**
     * @returns {naver.maps.Size}
     */
    getSize() {
      return this.map.getSize();
    },
    /**
     * @returns {number}
     */
    getZoom() {
      return this.map.getZoom();
    },

    /* Setter Methods */
    /**
     * @param {number | naver.maps.LatLng | naver.maps.LatLngLiteral} latOrLatLng
     * @param {number} lng
     * @returns this
     */
    setCenter(latOrLatLng, lng = 0) {
      this.map.setCenter(
        isNaN(latOrLatLng)
          ? latOrLatLng
          : new window.naver.maps.LatLng(latOrLatLng, lng)
      );
      return this;
    },
    /**
     * @param {naver.maps.Point | naver.maps.PointLiteral} point
     * @returns this
     */
    setCenterPoint(point) {
      this.map.setCenterPoint(point);
      return this;
    },
    /**
     * @param {string} type NORMAL, TERRAIN, SATELLITE, HYBRID
     * @returns this
     */
    setMapTypeId(type) {
      this.map.setMapTypeId(naver.maps.Position[type]);
      return this;
    },
    /**
     * @param {naver.maps.MapOptions | string} optionsOrKey
     * @param {naver.maps.MapOptions} value optional
     * @returns this
     */
    setOptions(optionsOrKey, value = null) {
      if (this.map) {
        if (value) this.map.setOptions(optionsOrKey, value);
        else this.map.setOptions(optionsOrKey);
      } else throw new Error("setOptions not be available before loaded.");
      return this;
    },
    /**
     * @param {naver.maps.Size | naver.maps.SizeLiteral} size
     * @returns this
     */
    setSize(size) {
      this.map.setSize(size);
      return this;
    },
    /**
     * @param {number} level must be int
     * @param {boolean} useEffect
     * @returns this
     */
    setZoom(level, useEffect = false) {
      this.map.setZoom(level, useEffect);
      return this;
    },
    /**
     * @description load naver maps
     */
    loadNaverMapsComponents() {
      const settings = {
        center: new window.naver.maps.LatLng(
          this.mapOptions.lat,
          this.mapOptions.lng
        ),
        maxZoom: 21,
        minZoom: 0,
      };
      const layers = {
        BACKGROUND: "bg",
        BACKGROUND_DETAIL: "ol",
        BICYCLE: "br",
        CADASTRAL: "lp",
        CTT: "ctt",
        HIKING_TRAIL: "ar",
        PANORAMA: "pr",
        POI_KOREAN: "lko",
        TRANSIT: "ts",
        KOREAN: "lko",
        ENGLISH: "len",
        CHINESE: "lzh",
        JAPANESE: "lja",
      };
      settings.mapTypes = new window.naver.maps.MapTypeRegistry({
        normal: window.naver.maps.NaverStyleMapTypeOption.getNormalMap({
          overlayType: this.initLayers.map((layer) => layers[layer]).join("."),
        }),
        satellite: window.naver.maps.NaverStyleMapTypeOption.getSatelliteMap({
          overlayType: this.initLayers.map((layer) => layers[layer]).join("."),
        }),
        hybrid: window.naver.maps.NaverStyleMapTypeOption.getHybridMap({
          overlayType: this.initLayers.map((layer) => layers[layer]).join("."),
        }),
        terrain: window.naver.maps.NaverStyleMapTypeOption.getTerrainMap({
          overlayType: this.initLayers.map((layer) => layers[layer]).join("."),
        }),
      });
      this.map = new window.naver.maps.Map("vue-naver-maps", {
        zoomControl: true,
        zoomControlOptions: {
          style: window.naver.maps.ZoomControlStyle.LARGE,
          position: window.naver.maps.Position.LEFT_CENTER,
        },
        mapTypeControl: true,
        mapTypeControlOptions: {
          style: window.naver.maps.MapTypeControlStyle.BUTTON,
          position: window.naver.maps.Position.LEFT_TOP,
        },

        ...settings,
        ...this.mapOptions,
      });
      if (this.zoomControlOptions && this.zoomControlOptions.position)
        this.setOptions({
          zoomControlOptions: {
            position: naver.maps.Position.position,
          },
        });
      window.$naverMapsCallback.forEach((v) => v(this.map));
      window.$naverMapsCallback = [];
      window.$naverMapsLoaded = true;
      window.$naverMapsObject = this.map;
      [
        "addLayer",
        "bounds_changed",
        "center_changed",
        "centerPoint_changed",
        "click",
        "dblclick",
        "doubletap",
        "drag",
        "dragend",
        "dragstart",
        "idle",
        "init_stylemap",
        "keydown",
        "keyup",
        "longtap",
        "mapType_changed",
        "mapTypeId_changed",
        "mousedown",
        "mousemove",
        "mouseout",
        "mouseover",
        "mouseup",
        "panning",
        "pinch",
        "pinchend",
        "pinchstart",
        "projection_changed",
        "removeLayer",
        "resize",
        "rightclick",
        "size_changed",
        "tap",
        "tilesloaded",
        "touchend",
        "touchmove",
        "touchstart",
        "twofingertap",
        "zoom_changed",
        "zooming",
      ].forEach((name) => _.addEvent(this, this.map, name));
      this.$emit("load", this);
      //Call the generateMarkers function to display them on the map.
      this.generateMarkers();
    },
    generateMarkers() {
      let markers = [],
        infoWindows = [];
      let map = this.map;
      let updateMarkers = this.updateMarkers;

      for (let i = 0; i < this.markersData.length; i++) {
        let marker = new naver.maps.Marker({
          position: new naver.maps.LatLng(
            this.markersData[i].latitude,
            this.markersData[i].longitude
          ),
          map: map,
        });
        let contentString = [
          '<div class="iw_inner black--text">',
          "   <h2>" + this.markersData[i].name + "</h2>",
          '   <p> <u>Status:</u> <b class="blue--text">' +
            this.markersData[i].status +
            '</b> - <u>Battery:</u> <b class="blue--text">' +
            this.markersData[i].battery +
            "</b><br> <u>MarkerID:</u> " +
            this.markersData[i].id +
            "<br> <u>Address:</u> " +
            this.markersData[i].street +
            ", " +
            this.markersData[i].state +
            ", " +
            this.markersData[i].country +
            ".<br> <u>Location:</u> " +
            this.markersData[i].latitude +
            ", " +
            this.markersData[i].longitude,
          "</div>",
        ].join("");
        let infowindow = new naver.maps.InfoWindow({
          content: contentString,
        });
        markers.push(marker);
        infoWindows.push(infowindow);
      }
      naver.maps.Event.addListener(map, "idle", function() {
        updateMarkers(map, markers);
      });

      for (var i = 0, ii = markers.length; i < ii; i++) {
        naver.maps.Event.addListener(
          markers[i],
          "click",
          this.getClickHandler(markers, infoWindows, i, map)
        );
      }
      // var htmlMarker1 = {
      //     content:
      //       '<div style="cursor:pointer;width:40px;height:40px;line-height:42px;font-size:10px;color:white;text-align:center;font-weight:bold;background:url(/images/cluster-marker-1.png);background-size:contain;"></div>',
      //     size: N.Size(40, 40),
      //     anchor: N.Point(20, 20),
      //   },
      //   htmlMarker2 = {
      //     content:
      //       '<div style="cursor:pointer;width:40px;height:40px;line-height:42px;font-size:10px;color:white;text-align:center;font-weight:bold;background:url(/images/cluster-marker-2.png);background-size:contain;"></div>',
      //     size: N.Size(40, 40),
      //     anchor: N.Point(20, 20),
      //   },
      //   htmlMarker3 = {
      //     content:
      //       '<div style="cursor:pointer;width:40px;height:40px;line-height:42px;font-size:10px;color:white;text-align:center;font-weight:bold;background:url(/images/cluster-marker-3.png);background-size:contain;"></div>',
      //     size: N.Size(40, 40),
      //     anchor: N.Point(20, 20),
      //   },
      //   htmlMarker4 = {
      //     content:
      //       '<div style="cursor:pointer;width:40px;height:40px;line-height:42px;font-size:10px;color:white;text-align:center;font-weight:bold;background:url(/images/cluster-marker-4.png);background-size:contain;"></div>',
      //     size: N.Size(40, 40),
      //     anchor: N.Point(20, 20),
      //   },
      //   htmlMarker5 = {
      //     content:
      //       '<div style="cursor:pointer;width:40px;height:40px;line-height:42px;font-size:10px;color:white;text-align:center;font-weight:bold;background:url(/images/cluster-marker-5.png);background-size:contain;"></div>',
      //     size: N.Size(40, 40),
      //     anchor: N.Point(20, 20),
      //   };

      // var markerClustering = new MarkerClustering({
      //   minClusterSize: 2,
      //   maxZoom: 13,
      //   map: map,
      //   markers: markers,
      //   disableClickZoom: false,
      //   gridSize: 120,
      //   icons: [
      //     htmlMarker1,
      //     htmlMarker2,
      //     htmlMarker3,
      //     htmlMarker4,
      //     htmlMarker5,
      //   ],
      //   indexGenerator: [10, 100, 200, 500, 1000],
      //   stylingFunction: function(clusterMarker, count) {
      //     $(clusterMarker.getElement())
      //       .find("div:first-child")
      //       .text(count);
      //   },
      // });
    },
    updateMarkers(map, markers) {
      let mapBounds = map.getBounds();
      let marker, position;

      for (let i = 0; i < markers.length; i++) {
        marker = markers[i];
        position = marker.getPosition();

        if (mapBounds.hasLatLng(position)) {
          this.showMarker(map, marker);
        } else {
          this.hideMarker(map, marker);
        }
      }
    },
    showMarker(map, marker) {
      if (marker.setMap()) return;
      marker.setMap(map);
    },

    hideMarker(map, marker) {
      if (!marker.setMap()) return;
      marker.setMap(null);
    },

    // Return an event handler storing the marker index as a closure letiable named seq.
    getClickHandler(markers, infoWindows, seq, map) {
      return function(e) {
        let marker = markers[seq],
          infoWindow = infoWindows[seq];

        if (infoWindow.getMap()) {
          infoWindow.close();
        } else {
          infoWindow.open(map, marker);
        }
      };
    },
  },
  mounted() {
    if (this.mapOptions.lat && this.mapOptions.lng) {
      if (window.naver) this.loadNaverMapsComponents();
      else
        document.getElementById("naver-map-load").onload = () =>
          (window.naver.maps.onJSContentLoaded = this.loadNaverMapsComponents);
    } else throw new Error("mapOptions must be included lat and lng.");
  },
  destroyed() {
    window.$naverMapsLoaded = false;
  },
};
</script>
