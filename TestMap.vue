<template>
        <button @click="onLeafletReady('../20220622.json')">pic1</button>
        <button @click="onLeafletReady('../20220501.json')">pic2</button>
        <button @click="onLeafletReady('../20220502.json')">pic3</button>
        <button @click="onLeafletReady('../20220503.json')">pic4</button>



  <l-map
    ref="map"
    :max-zoom="18"
    :zoom="zoom"
    :center="{ lat:25.06108073603067, lng: 121.56835445788387 }"
    @ready="onLeafletReady"
   >
    <template v-if="leafletReady">
      <l-tile-layer :url="url" />
      <marker-cluster
        :options="{ showCoverageOnHover: false, chunkedLoading: true }"
      >

      <template v-for="data in datas" > 

        <l-marker :lat-lng="[`${data.latitude}`, `${data.longitude}`]" ></l-marker>

       </template>  
     
      </marker-cluster>
    </template>
  </l-map>
</template>
<script>
import "leaflet/dist/leaflet.css";
import axios from 'axios'

import { LMap, LMarker, LTileLayer,LPopup} from "@vue-leaflet/vue-leaflet/src/lib";
import MarkerCluster from "./MarkerCluster.vue";

export default {
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LPopup,
    // LControl,
    // LDomUtil,
    MarkerCluster,
  },

  data() {
    return {
      url: "https://{s}.tile.osm.org/{z}/{x}/{y}.png",
      zoom: 13,

      leafletReady: false,
      leafletObject: null,
      json:'../20220622.json',

      visible: false,


      datas:null,


      info:null,
    };
  },

  methods: {
   
    async onLeafletReady(api) {
      await this.$nextTick();
      this.leafletObject = this.$refs.map.leafletObject;
      this.leafletReady = true;
this.json=api
console.log(api)
       axios.get(api)
          .then((response)=>{
            let data=response.data;
            let newData={}
            // console.log(this.info)
            // console.log(this.info[0].latitude)
            for ( let i = 0; i < data.length; i++) {
        const longitude = data[i].longitude
        const latitude = data[i].latitude
        const key = longitude + '-' + latitude

        // 如果新資料中還沒有
        if (!newData[key]) {
          newData[key] = []
          newData[key].push(data[i])
        } else {
          newData[key].push(data[i])
        }
      }
      // console.log('newData', newData)
      return newData
            })
            .then((newData)=>{
      const array = Object.values(newData)
      const averageArray = []

      for (let i = 0; i < array.length; i++) {

        const placeArray = array[i]
        //placeArray是依照位置把它們分組
        const averageReq = placeArray.reduce((total, item) => Number(item.request_processing_time) + total, 0) / array.length
        const sumReq = placeArray.reduce((total, item) => Number(item.request_processing_time) + total, 0)
        const averageTarget = placeArray.reduce((total, item) => Number(item.target_processing_time) + total, 0) / array.length
        const sumTarget = placeArray.reduce((total, item) => Number(item.target_processing_time) + total, 0)
        const averageRes = placeArray.reduce((total, item) => Number(item.response_processing_time) + total, 0) / array.length
        const sumRes = placeArray.reduce((total, item) => Number(item.response_processing_time) + total, 0)

        const result = {
          longitude: placeArray[0].longitude,
          latitude: placeArray[0].latitude,
          averageReq: averageReq,
          sumReq: sumReq,
          averageTarget: averageTarget,
          sumTarget: sumTarget,
          averageRes: averageRes,
          sumRes: sumRes
        }
        averageArray.push(result)
      }
      // console.log('averageArray', averageArray)
      this.datas= averageArray
      // console.log(this.datas)
            })
 
    },
    changePic(api){
      this.json=api
    }
    
//     onClick(){
// this.info=LControl
//      this.info.onAdd = function (map) {
//     this._div = LDomUtil.create('div', 'info'); // create a div with a class "info"
//     this.update();
//     return this._div;
// };
// info.update = function (props) {
//     this._div.innerHTML = '<h4>總資料筆數：</h4>' +  (`
//     request_processing_time_average: ${averageArray[j].averageReq} <br> 
//     request_processing_time_sum: ${averageArray[j].sumReq} <br>
//     target_processing_time_average: ${averageArray[j].averageTarget} <br> 
//     target_processing_time_sum: ${averageArray[j].sumTarget} <br> 
//     response_processing_time_average: ${averageArray[j].averageRes}<br>
//     response_processing_time_sum: ${averageArray[j].sumRes}<br>
//     `);
// };
// console.log(`${averageArray[j].averageReq}`)
// // var marker = Lmarker([51.5, -0.09]).addTo(map);
// info.addTo(map);
//     }
  },
 
};
</script>
