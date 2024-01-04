<template>
  <div id="map">   
  </div>   
</template>

<script>
import "ol/ol.css";
import { Map, View } from "ol";
import {fromLonLat} from 'ol/proj';
// import ol from "ol";
import TileLayer from "ol/layer/Tile";
// import OSM from "ol/source/OSM";
import {XYZ} from "ol/source";
// import OlStyleStyle from 'ol/style/Style';
// import OlStyleIcon from 'ol/style/Icon';
// // import OlStyleStroke from 'ol/style/Stroke';
// // import Text from 'ol/style/Text';
// // import Fill from 'ol/style/Fill';
// import Feature from 'ol/Feature';
// // import OlSourceVector from 'ol/source/Vector';
// // import OlLayerVector from 'ol/layer/Vector';
// import OlGeomPoint from 'ol/geom/Point';
// import Overlay from 'ol/Overlay';
// import {toStringHDMS} from 'ol/coordinate';
// import {defaults as defaultControls} from 'ol/control';
// import ZoomSlider from 'ol/control/ZoomSlider';
// import Point from 'ol/geom/Point';

export default {
  data() {
    return {
      map: null,
      deviceInfo:[],
      vectorSource:[],
      // beijing : [121.5647688,25.0374865],

    };
  },
  methods:{
    getNormalizedCoord:function(coord, zoom)
		{
      var y = coord.y;
      var x = coord.x;
      var tileRange = 1 << zoom;
      if (y < 0 || y >= tileRange) { return null; }
      if (x < 0 || x >= tileRange)
      {
        x = (x % tileRange + tileRange) % tileRange;  
      }
      return { x: x, y: y };
    },
    tileUrlFunction: function (coordinate) {
                console.log(coordinate[0],coordinate[1],coordinate[2]);
                var normalizedCoord=this.getNormalizedCoord({x:coordinate[1],y:coordinate[2]},coordinate[0]);
                console.log(normalizedCoord);
                var z = coordinate[0];
                var x = normalizedCoord.x;
                var y = normalizedCoord.y;
                return "http://localhost/OfflineMap/Z" + z + "/" + y + "/" + x + ".jpg";
              }
  },
  mounted() {
    var that=this;
     //var beijing = [121.5647688,25.0374865];//toLonLat.fromLonLat([121.5647688,25.0374865]);
    // var testpmLonLat=[121.5647688,25.0374865];
    //var mapcontainer = this.$refs.rootmap;
     new Map({
      target: "map",
      layers: [
        new TileLayer({
          // source: new OSM()
          source: new XYZ({
              // url:'http://127.0.0.1:8080/Taiwan/Z{z}/{x}/{y}.jpg'
              tileUrlFunction:that.tileUrlFunction,
              maxZoom: 14,
              minZoom: 0,
              projection: 'EPSG:3857'					
          })
        })
      ],
      view: new View({
        projection: "EPSG:3857",    //使用这个座標系
        center: fromLonLat([121.5647688,25.0374865]),  //台北
        zoom: 14,
      }),
      // keyboardEventTarget: document,
      //controls: defaultControls().extend([new ZoomSlider()])
    });
  }
};
</script>

<style type="text/css">
  /* #map{height:100%;} */
  隐藏ol的一些自带元素
  /* .ol-attribution,.ol-zoom { display: none;} */
  .ol-attribution { display: none;}
  /* body, html
  {
    border:none;
    padding:0;
    margin:0;
  } */
  body, #map {
			/* border: 0px;
			margin: 0px;
			padding: 0px;
			width: 100%; */
			height: 100%;
			/* font-size: 13px; */
		}
    .Fbar{
      position: absolute;
			top: 10pt;
			right: 20pt;
			z-index: 99;
    }
  /* .btn{
			position: absolute;
			top: 10pt;
			right: 20pt;
			z-index: 99;
		}
		.btn1{
			position: absolute;
			top: 10pt;
			right: 70pt;
			z-index: 99;
		}
		.btn2{
			position: absolute;
			top: 10pt;
			right: 120pt;
			z-index: 99;
		} */

  /* #menu
  {
    width:100%;
    height:20px;
    padding:5px 10px;
    font-size:14px;
    font-family:"微软雅黑";
    left:10px;
  } */
  .ol-popup {
        position: absolute;
        background-color: white;
        box-shadow: 0 1px 4px rgba(0,0,0,0.2);
        padding: 15px;
        border-radius: 10px;
        border: 1px solid #cccccc;
        bottom: 12px;
        left: -50px;
        min-width: 280px;
      }
      .ol-popup:after, .ol-popup:before {
        top: 100%;
        border: solid transparent;
        content: " ";
        height: 0;
        width: 0;
        position: absolute;
        pointer-events: none;
      }
      .ol-popup:after {
        border-top-color: white;
        border-width: 10px;
        left: 48px;
        margin-left: -10px;
      }
      .ol-popup:before {
        border-top-color: #cccccc;
        border-width: 11px;
        left: 48px;
        margin-left: -11px;
      }
      .ol-popup-closer {
        text-decoration: none;
        position: absolute;
        top: 2px;
        right: 8px;
      }
      .ol-popup-closer:after {
        content: "✖";
      }
</style>

