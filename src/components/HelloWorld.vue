<template>
  <div id="container">
    <input id="enable" type="button" value="Enable/disable" />
    <form class="form-inline">
      <label for="type">Geometry type &nbsp;</label>
      <select id="type">
        <option value="Point">Point</option>
        <option value="LineString">LineString</option>
        <option value="Polygon">Polygon</option>
        <option value="Circle">Circle</option>
      </select>
    </form>
    <div id="map"></div>
  </div>
</template>

<script>
import "ol/ol.css";
import Map from "ol/Map";
import View from "ol/View";
import TileLayer from "ol/layer/Tile";
import OSM from "ol/source/OSM";
import OLCesium from "olcs/OLCesium.js";

//Draw specific
import { Draw, Modify, Snap } from "ol/interaction";
import { Vector as VectorSource } from "ol/source";
import { Vector as VectorLayer } from "ol/layer";
import { Circle as CircleStyle, Fill, Stroke, Style } from "ol/style";

var draw, snap;
var source = new VectorSource();
let ol2dMap;
let typeSelect;

export default {
  name: "HelloWorld",
  mounted: function () {
    console.log("vue_olcs mounted event fired");

    //Draw Specific
    typeSelect = document.getElementById("type");
    var modify = new Modify({ source: source });

    var vector = new VectorLayer({
      source: source,
      style: new Style({
        fill: new Fill({
          color: "rgba(255, 255, 255, 0.2)",
        }),
        stroke: new Stroke({
          color: "#ffcc33",
          width: 2,
        }),
        image: new CircleStyle({
          radius: 7,
          fill: new Fill({
            color: "#ffcc33",
          }),
        }),
      }),
    });

    // const OLCesium = require("olcs/OLCesium");
    ol2dMap = new Map({
      target: "map",
      layers: [
        new TileLayer({
          source: new OSM(),
        }),
        vector,
      ],
      view: new View({
        center: [0, 0],
        zoom: 0,
      }),
    });
    ol2dMap.addInteraction(modify);
    const ol3d = new OLCesium({ map: ol2dMap });
    ol3d.setEnabled(true);

    var setEnabled = function () {
      ol3d.setEnabled(!ol3d.getEnabled());
    };
    document.getElementById("enable").addEventListener("click", setEnabled);

    var addInteractions = function () {
      draw = new Draw({
        source: source,
        type: typeSelect.value,
      });
      ol2dMap.addInteraction(draw);
      snap = new Snap({ source: source });
      ol2dMap.addInteraction(snap);
    };
    typeSelect.onchange = function () {
      ol2dMap.removeInteraction(draw);
      ol2dMap.removeInteraction(snap);
      addInteractions();
    };
    addInteractions();
  },
};
</script>

<style scoped>
#map {
  height: 100vh;
  width: 100%;
  position: absolute;
}
</style>
