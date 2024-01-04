<template>
  <div id="map">
    
      <!-- <button class="btn" @click="btn">顯示標點</button>
      <button class="btn1" type="button" @click="position(0)">定位到A</button>
      <button class="btn2" type="button" @click="position(1)">定位到B</button> -->
    <ul class="nav justify-content-end Fbar">
      <li class="nav-item">
         <button class="btn btn-secondary" @click="btn">顯示標點</button>
      </li>
      <!-- <li class="nav-item">
        <button class="btn btn-secondary" type="button" @click="position(0)">定位到A</button>
      </li>
      <li class="nav-item">
        <button class="btn btn-secondary" type="button" @click="position(1)">定位到B</button>
      </li> -->
      <!-- Dropdown -->
      <li class="nav-item dropdown">
        <!-- <a class="nav-link dropdown-toggle" href="#" id="navbardrop" data-toggle="dropdown">
          定位
        </a> -->
         <button class="btn btn-secondary dropdown-toggle" type="button" data-toggle="dropdown">定位</button>
        <div class="dropdown-menu">
          <button class="btn " type="button" @click="position(0)">定位到A</button>
          <button class="btn " type="button" @click="position(1)">定位到B</button>
        </div>
      </li>
    </ul>

      <!-- <label class="checkbox">
        <input type="radio" name="label" value="vector" checked="checked" />
        Vector Label
      </label>
      <label class="checkbox">
        <input type="radio" name="label" value="overlay" />
        Overlay Label
      </label> -->
    
    <div id="popup" class="ol-popup">
      <a href="#" id="popup-closer" class="ol-popup-closer"></a>
      <div id="popup-content"></div>
    </div>
    <div id="label" style="display:none">
      <div id="marker" class="marker" title="Marker">
        <a class="address" id="address" target="_blank" href="http://www.openlayers.org">標註點</a>
      </div>
    </div>
  </div>   
</template>

<script>
import "ol/ol.css";
import { Map, View } from "ol";
import {fromLonLat,toLonLat} from 'ol/proj';
// import ol from "ol";
import TileLayer from "ol/layer/Tile";
import OSM from "ol/source/OSM";
import OlStyleStyle from 'ol/style/Style';
import OlStyleIcon from 'ol/style/Icon';
// import OlStyleStroke from 'ol/style/Stroke';
// import Text from 'ol/style/Text';
// import Fill from 'ol/style/Fill';
import Feature from 'ol/Feature';
import OlSourceVector from 'ol/source/Vector';
import OlLayerVector from 'ol/layer/Vector';
import OlGeomPoint from 'ol/geom/Point';
import Overlay from 'ol/Overlay';
import {toStringHDMS} from 'ol/coordinate';
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
    position:function(device){
      console.log(this.deviceInfo[device].coordinate);
      this.map.getView().setCenter(this.deviceInfo[device].coordinate)
			this.map.getView().setZoom(10);
    },
    addDeviceInfo:function(info){
      this.deviceInfo.push({
        //地理位置
        coordinate: info.coordinate,
        //属性信息
        att: {
          //标题
          title: info.title,
          //超链接
          titleURL: "http://www.google.com",
          //文本内容
          text: info.text,
          Lng:info.Lng,
          Lat:info.Lat,
          Height:info.Height,
          Course:info.Course,
          Speed:info.Speed,
          //图像地址
          imgURL: "../img/logo.png"//"https://img.icons8.com/fluent/48/000000/map-pin.png"
        }
      });
    },
     //建立標籤的樣式
    //var createLabelStyle = function (feature) {
    createLabelStyle : function () {
      //返回一個樣式
      return new OlStyleStyle({
        //把點的樣式換成ICON圖示
        image: new OlStyleIcon({
            //控制標註圖片和文字之間的距離
            anchor: [0.5, 60],
            //標註樣式的起點位置
            anchorOrigin: 'top-right',
            //X方向單位：分數
            anchorXUnits: 'fraction',
            //Y方向單位：畫素
            anchorYUnits: 'pixels',
            //偏移起點位置的方向
            // offsetOrigin: 'top-right',
            //透明度
            opacity: 1,
            //圖片路徑
            src: 'https://img.icons8.com/fluent/48/000000/map-pin.png'
        }),
        //文字樣式
        // text: new Text({
          //     //對齊方式
          //     textAlign: 'center',
          //     //文字基線
          //     textBaseline: 'middle',
          //     //字型樣式
          //     font: 'normal 14px 微軟雅黑',
          //     //文字內容
          //     text: feature.get('name'),
          //     //填充樣式
          //     fill: new Fill({
          //         color: '#aa3300'
          //     }),
          //     //筆觸
          //     stroke: new OlStyleStroke({
          //         color: '#ffcc33',
          //         width: 2
          //     })
          // })
      });
    },
    //新增向量標籤
    addVectorLabel:function(info) {
      //初始化一個新的點要素
      var newFeature = new Feature({
      geometry: new OlGeomPoint(fromLonLat(info.coordinate, 'EPSG:4326')),
        //名稱屬性
        name: info.title,
        //人口屬性
        //population: 2115
      });
      //設定點的樣式
      newFeature.setStyle(this.createLabelStyle(newFeature));
      //將當前要素新增到向量資料來源中
      this.vectorSource.addFeature(newFeature);          
    },
    btn:function(){
      // this.vectorSource = new OlSourceVector({
      //   //指定要素
      //   features:[]
      // });
      this.deviceInfo=[]; 
      this.addDeviceInfo({
        coordinate:[121.52580166743165, 25.039546436523434],
        title:'設備A',
        text:'經緯度、高度、航向、速度',
        Lng:121.52580166743165,
        Lat: 25.039546436523434,
        Height:3500,
        Course:'',
        Speed:''
      });
      this.addDeviceInfo({
        coordinate:[121.50451565668946, 25.001094288085934],
        title:'設備B',
        text:'測試點B',
        Lng:121.50451565668946,
        Lat: 25.001094288085934,
        Height:3500,
        Course:'',
        Speed:''
      });
      this.vectorSource.clear();//清空向量圖層
      this.deviceInfo.forEach(item=>{
        this.addVectorLabel(item);  
      });
    },
  },
  mounted() {
    var that=this;
     //var beijing = [121.5647688,25.0374865];//toLonLat.fromLonLat([121.5647688,25.0374865]);
      var testpmLonLat=[121.5647688,25.0374865];
    //var mapcontainer = this.$refs.rootmap;
    this.map = new Map({
      target: "map",
      layers: [
        new TileLayer({
          source: new OSM()
        })
      ],
      view: new View({
        projection: "EPSG:4326",    //使用这个座標系
        center: testpmLonLat,  //台北
        zoom: 12,
      }),
      keyboardEventTarget: document,
      //controls: defaultControls().extend([new ZoomSlider()])
    });
    // var featureInfo = {
    //   //地理位置
    //   geo: testpmLonLat,
    //   //属性信息
    //   att: {
    //     //标题
    //     title: "台北市",
    //     //超链接
    //     titleURL: "http://www.google.com",
    //     //文本内容
    //     text: "台北市",
    //     //图像地址
    //     imgURL: "../img/logo.png"//"https://img.icons8.com/fluent/48/000000/map-pin.png"
    //   },
    // };
   
    // //初始化要素
    // var iconFeature = new Feature({
    //   //點要素
    //   geometry: new OlGeomPoint(fromLonLat([121.5647688,25.0374865], 'EPSG:4326')),
    //   //名稱屬性
    //   name: '台北市',
    //   //人口屬性
    //   population: 2115
    // });
    // //為點要素新增樣式
    // iconFeature.setStyle(this.createLabelStyle(iconFeature));
    //初始化向量資料來源
    this.vectorSource = new OlSourceVector({
      //指定要素
      features:[]//[iconFeature]
    });      
               
    this.map.on('click', function (evt) {
      //獲取單選按鈕的選項
      //var type = $('input[name="label"]:checked').val();
      //獲取位置座標
      var point = evt.coordinate;
        console.log(point);
        //如果型別是向量標註則新增向量標籤，否則新增覆蓋標籤
        //addVectorLabel(point);
    });
    
    //初始化向量圖層
    var vectorLayer = new OlLayerVector ({
      //資料來源
      source:this.vectorSource
    });
    //將向量圖層新增到map中
    this.map.addLayer(vectorLayer); 


    //获取id为popup的div标签
    var container = document.getElementById('popup');
    //获取id为popup-content的div标签
    var content = document.getElementById('popup-content');
    //获取id为popup-closer的a标签
    var closer = document.getElementById('popup-closer');
    closer.onclick = function() {
      popup.setPosition(undefined);
      closer.blur();
      return false;
    };
    //初始化一个覆盖层
    var popup = new Overlay({
      //元素内容
      element: container,
      //If set to true the map is panned when calling setPosition, 
      //so that the overlay is entirely visible in the current viewport. 
      //The default is false.
      autoPan: true,
      ////覆盖层如何与位置坐标匹配
      //positioning: 'bottom-center',
      //事件传播到地图视点的时候是否应该停止
      //stopEvent: false,
      //The animation options used to pan the overlay into view. 
      //This animation is only used when autoPan is enabled. 
      autoPanAnimation: {
        //动画持续时间
        duration:250
      }
    });
    //将覆盖层添加到map中
    this.map.addOverlay(popup);
        
    //为要素添加信息的函数
    function addFeatureInfo(info) {
      //创建一个a标签元素
      var elementA = document.createElement('a');
      //设置a标签的样式类
      //elementA.className = 'markerInfo';
      //设置a标签的超链接地址
      elementA.href = info.att.titleURL;
      //设置a标签的文本内容
      setInnerText(elementA, info.att.title);
      //将a标签元素添加到内容div标签中
      content.appendChild(elementA);
      //创建一个div标签元素
      var elementDiv = document.createElement('div');
      //设置div标签的内容
      setInnerText(elementDiv, '經度:'+info.att.Lng);
      //将div标签加入到内容div标签中
      content.appendChild(elementDiv);
      elementDiv = document.createElement('div');
      //设置div标签的内容
      setInnerText(elementDiv, '緯度:'+info.att.Lat);
      //将div标签加入到内容div标签中
      content.appendChild(elementDiv);
      //创建一个div标签元素
       elementDiv = document.createElement('div');
      //设置div标签的内容
      setInnerText(elementDiv, '高度:'+info.att.Height);
      //将div标签加入到内容div标签中
      content.appendChild(elementDiv);
      //创建一个div标签元素
       elementDiv = document.createElement('div');
      //设置div标签的内容
      setInnerText(elementDiv, '速度:'+info.att.Speed);
      //将div标签加入到内容div标签中
      content.appendChild(elementDiv);
      //创建一个div标签元素
      elementDiv = document.createElement('div');
      //设置div标签的内容
      setInnerText(elementDiv, '航向:'+info.att.Course);
      //将div标签加入到内容div标签中
      content.appendChild(elementDiv);

      // //创建一个图像标签
      // var elementImg = document.createElement('img');
      // //指定图像标签的URL
      // elementImg.src = info.att.imgURL;
      // //将img标签加入到内容div标签中
      //   content.appendChild(elementImg);
    }
 
    //设置文本函数
    function setInnerText(element,text) {
      if (typeof element.textContent == 'string') {
        element.textContent = text;
      } else {
        element.innerText = text;
      }
    }           
            
    this.map.on('singleclick', function(evt) {
      //var point = evt.coordinate;
     var flatCoordinatesInfo;
      var coordinate = evt.coordinate;
      var hdms = toStringHDMS(toLonLat(coordinate));
      content.innerHTML = '<p>You clicked here:</p><code>' + hdms +'</code>';
      var feature = that.map.forEachFeatureAtPixel(evt.pixel, function (feature, layer) {
        //在视口中遍历所有具有像素颜色的图层，如果图层存在，则返回
        console.log( layer);
        console.log( feature);
        console.log( evt.pixel);
        flatCoordinatesInfo=that.deviceInfo.find(item=>{
          var flatCoordinates=feature.values_.geometry.flatCoordinates;
          // console.log(feature.values_.geometry.flatCoordinates+"=="+item.coordinate);
          return flatCoordinates[0]==item.coordinate[0] && flatCoordinates[1]==item.coordinate[1];
        });
        return feature;
      });
      if (feature) {
        //将内容div的内容清空
        content.innerHTML = '';
        //添加要素信息
        // var flag=false;
        // if(flag)
        
        console.log(flatCoordinatesInfo);
        addFeatureInfo(flatCoordinatesInfo);
        //如果当前popup覆盖层没有坐标，则设置坐标
        if (popup.getPosition() == undefined) {
          popup.setPosition(coordinate);
        }
      }
      popup.setPosition(coordinate);
    });

    //为map注册一个pointermove事件的监听
    //pointermove事件
    //Triggered when a pointer is moved. 
    //Note that on touch devices this is triggered when the map is panned, so is not the same as mousemove. 
    this.map.on('pointermove', function (e) {
      //Returns the map pixel position for a browser event relative to the viewport.
      //获取map的像素位置信息
      //console.log(e.originalEvent);
      var pixel = that.map.getEventPixel(e.originalEvent);
      //hasFeatureAtPixel(pixel, opt_options)
      //Detect if features intersect a pixel on the viewport.
      //map视口中是否包含某个要素
      var hit = that.map.hasFeatureAtPixel(pixel);
      //设置符合当前条件的鼠标样式
      that.map.getTargetElement().style.cursor = hit ? 'pointer' : '';
    });    
  }
};
</script>

<style type="text/css">
  /* #map{height:100%;} */
  /*隐藏ol的一些自带元素*/
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

