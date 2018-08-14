<template>
  <div  id="app">
 
    <div class="app-block">
      <div class="s-panel"  :class="{ active:activePanel }">
        <div class="burger" @click="acPanel()">
          <span></span>
        </div>
        <div class="title">{{lang.podbor}}</div>

        <div class="l-block">
          <div class="title-mini">{{lang.countroom}}</div>
          <div class="rooms">
            <div class="active-mask" :style="pos"></div>
            <a href="#" :class="{ active:isActive(item, room) }" v-for="item in roms" :key="item"  @click.prevent="setRooms(item, $event)">{{item}}</a>
          </div>
        </div>
        

        <div v-if="!isMobile" class="levels l-block">
            <div class="title-mini">{{lang.selectflor}}</div>
            <vue-slider v-model="sl.value" v-bind="sl.options" event-type="touch"></vue-slider>
        </div>

        
        <div v-if="data.count">


          <div class="title-mini">{{lang.dosflats}}</div>
          <div class="list-flats-wrap">
            <div class="list-flats c-sc">
              <div class="item" @click="setFlat(i)" :class="{ active:isActive(i, activef) }" v-for="(item, i) in data.data" :key="i">
                <div class="img">
                  <img :src="item.img_mini" alt="">
                </div>
                <div class="item-title">{{item.type}} {{item.square}} </div>
              </div>
            </div>
          </div>
          <div class="s-panel-info">
            <div class="sq">
              <div class="t">{{lang.square}}</div>
              <div class="v">
               {{lang.ot}}  {{data.min.square}}    до {{data.max.square}} м²
              </div>
            </div>
          </div>
          


        </div>
        <div v-else class="text-center empty">
            пусто
        </div>
          
        

      </div>

      <div class="flat-info">
        <div class="img" v-if="data.count">
         
            <img :src="data.data[activef].img" alt="">
        
          
        </div>
      </div>

    </div>
    
  </div>
</template>

<script>
let isMobile = false;
if (
  navigator.userAgent.match(/Android/i) ||
  navigator.userAgent.match(/webOS/i) ||
  navigator.userAgent.match(/iPhone/i) ||
  navigator.userAgent.match(/iPad/i) ||
  navigator.userAgent.match(/iPod/i) ||
  navigator.userAgent.match(/BlackBerry/i) ||
  navigator.userAgent.match(/Windows Phone/i)
) {
  isMobile = true;
}

import axios from "axios";
import Vue from "vue";
import VueLodash from "vue-lodash";

const options = { name: "lodash" }; // customize the way you want to call it

Vue.use(VueLodash, options); // options is optional
import vueSlider from "vue-slider-component";
//import vueSlider from './vue-slider/'

let lang = {
  ru: {
    podbor: "Подобрать квартиру",
    dosflats: "Доступные планировки",
    square: "Площадь",
    ot: "от",
    countroom: "Количество комнат",
    selectflor: "ВЫБОР ЭТАЖей"
  },
  uk: {
    podbor: "Підібрати квартиру",
    dosflats: "Доступні планування",
    square: "Площа",
    ot: "від",
    countroom: "Кількість кімнат",
    selectflor: "вибір поверхів"
  }
};

let curLang = $("body").data("lang");

export default {
  name: "App",
  components: {
    vueSlider
  },
  data() {
    return {
      flats: {},
      roms: [1, 2, 3],
      room: 1,
      activef: 0,
      lang: lang[curLang],
      activePanel: false,
      pos: {
        left: 0
      },
      isMobile: isMobile,
      sl: {
        value: [1, 22],
        options: {
          width: "100%",
          height: 8,
          dotSize: 20,
          min: 1,
          max: 22,
          interval: 1,
          disabled: false,
          show: true,
          realTime: false,
          tooltip: "always",
          clickable: true,
          tooltipDir: "top",
          piecewise: false,
          lazy: false,
          reverse: false,
          speed: 0.5,
          formatter: null,
          bgStyle: null,
          sliderStyle: null,
          tooltipStyle: null,
          processStyle: null,
          piecewiseStyle: null
        }
      }
    };
  },
  created() {
    axios
      //  .get("http://synergy3.com.ua/api/all-api/?zk=s3p")
      .get("https://greenside.com.ua/api/all-api")
      .then(response => {
        this.flats = response.data;
      })
      .catch(e => {
        this.erros.push(e);
      });
  },
  updated() {},
  computed: {
    data() {
      let data = {};
      data.data = Vue.lodash.filter(this.flats, { rooms: this.room });
      data.data = Vue.lodash.filter(data.data, o => {
        return o.floor >= this.sl.value[0] && o.floor <= this.sl.value[1];
      });

      data.count = Vue.lodash.size(data.data);
      data.min = Vue.lodash.minBy(data.data, "square");
      data.max = Vue.lodash.maxBy(data.data, "square");
      return data;
    }
  },
  updated() {},
  methods: {
    setRooms(room, e) {
      //  console.log(e);
      this.room = room;
      this.activef = 0;
      this.pos.left = e.target.offsetLeft + "px";
      this.pos.width = e.target.clientWidth + "px";
    },
    setFlat(i) {
      this.activef = i;
    },
    isActive: function(menuItem, active) {
      return active === menuItem;
    },
    acPanel() {
      this.activePanel = !this.activePanel;
      setTimeout(() => {
        this.isMobile = false;
      }, 300);
    }
  }
};
</script>

<style lang="scss">
.app-block {
  padding: 30px;
  background: #e6e9ec;
  height: 100vh;
  display: flex;
}

.s-panel {
  background: #fff;
  padding: 30px;
  min-width: 360px;
  max-width: 360px;
  border-radius: 3px;
  .title {
    font-weight: 500;
    font-size: 24px;
    color: #000;
    margin-bottom: 26px;
  }
  .title2 {
    margin-bottom: 20px;
  }
}

.flat-info {
  width: 80%;
  display: flex;
  align-items: center;
  justify-content: center;
  .img {
    text-align: center;
    img {
      max-width: 90%;
      max-height: calc(100vh - 60px);
    }
  }
}

.image-compare-wrapper {
  background: #e6e9ec;
  // height: 100%;
  padding: 30px;
}

.image-compare {
  // height: 100%;
}

.rooms {
  display: flex;
  flex-flow: row nowrap;
  justify-content: space-around;
  align-items: stretch;
  border: 1px solid #dbdbdb;
  border-radius: 4px;
  background: #fcfcfc;

  position: relative;
  .active-mask {
    position: absolute;
    border: 2px solid #f49e47;
    border-radius: 4px;
    transition: all 0.3s ease;
    pointer-events: none;
    width: 100px;
    bottom: -1px;
    top: -1px;
  }
  a {
    flex: 1 1 100%;
    text-align: center;
    font-size: 14px;
    color: #000;
    letter-spacing: 0;
    transition: -webkit-transform 0.4s ease;
    transition: transform 0.4s ease;
    transition: transform 0.4s ease, -webkit-transform 0.4s ease;
    text-decoration: none;
    padding-top: 15px;
    padding-bottom: 15px;
    &.active {
      color: #f49e47;
      font-weight: bold;
    }
  }
}

.l-block {
  margin-bottom: 30px;
}

.list-flats {
  display: flex;
  flex-wrap: wrap;
  margin-left: -10px;
  margin-right: -10px;
  transition: opacity 0.2s ease;
  max-height: 40vh;
  overflow-y: scroll;
  .item {
    width: 25%;
    max-width: 25%;
    padding-left: 5px;
    padding-right: 5px;
    margin-bottom: 20px;
    text-align: center;
    cursor: pointer;
    &.active {
      .img:before {
        border: 2px solid #f49e47;
      }
      .item-title {
        color: #f49e47;
      }
    }
  }
  .img {
    width: 40px;
    height: 40px;
    margin: 10px auto 10px;
    position: relative;
    &:before {
      position: absolute;
      content: "";
      left: -7px;
      bottom: -7px;
      top: -7px;
      right: -7px;
      border: 2px solid #fff;
      border-radius: 4px;
      transition: all 0.3s ease;
    }
    img {
      display: block;
      width: 40px;
    }
  }
  .item-title {
    font-size: 12px;
    transition: all 0.3s ease;
  }
}

.list-flats-wrap {
  padding-right: 10px;
  position: relative;
  &:after {
    pointer-events: none;
    height: 30px;
    content: "";
    position: absolute;
    left: 0;
    right: 5px;
    bottom: 0;
    background: linear-gradient(
      to bottom,
      rgba(255, 255, 255, 0) 0%,
      rgba(255, 255, 255, 1) 100%
    );
  }
}
/* width */
.c-sc::-webkit-scrollbar {
  width: 5px;
}

/* Track */
.c-sc::-webkit-scrollbar-track {
  background: #f1f1f1;
}

/* Handle */
.c-sc::-webkit-scrollbar-thumb {
  background: rgb(250, 198, 134);
}

/* Handle on hover */
.c-sc::-webkit-scrollbar-thumb:hover {
  background: #f6ac52;
}

.s-panel-info {
  margin-top: 10px;
  border-bottom: 1px solid #dbdbdb;
  > div {
    border-top: 1px solid #dbdbdb;
    display: flex;
    justify-content: space-between;
    padding: 20px 0;
    font-size: 15px;
  }
  .v {
    font-weight: bold;
  }
}

.s-panel {
  .burger {
    display: none;
  }
  .vue-slider-component .vue-slider-tooltip {
    border: 1px solid #f49e47;
    background-color: #f49e47;
  }
  .vue-slider-component .vue-slider-process {
    background-color: #f49e47;
  }
}

.title-mini {
  margin-bottom: 8px;
  text-transform: uppercase;
  font-size: 11px;
}

.levels {
  .title-mini {
    margin-bottom: 38px;
  }
}

@media (max-width: 1300px) {
  .s-panel {
    padding: 20px;
    min-width: 280px;
    max-width: 280px;
  }
  .list-flats .item {
    width: 33.33%;
    max-width: 33.33%;
  }
  .s-panel .title {
    font-size: 20px;
  }
  .rooms {
    margin-bottom: 25px;
  }
  .rooms a {
    padding-top: 10px;
    padding-bottom: 10px;
  }
  .s-panel .title2 {
    margin-bottom: 15px;
  }
  .rooms .active-mask {
    transform: translate(-2px, 0);
    width: 73px;
  }
  .s-panel-info > div {
    padding: 15px 0;
    font-size: 14px;
  }
  .app-block {
    padding: 20px;
  }
}

@media (max-width: 991px) {
  .s-panel {
    position: absolute;
    top: 20px;
    bottom: 20px;
    border-radius: 3px 0 3px 3px;
    box-shadow: 0 0 20px 0 rgba(0, 0, 0, 0.1);
    transition: all 0.4s ease;
    // transform: translate(-100%, 0);
    &.active {
      left: 0;
      .burger span {
        left: 54%;
        transform: translate(-60%, -45%) rotate(135deg);
      }
    }
    .burger {
      display: block;
      position: absolute;
      background: #fff;
      width: 50px;
      height: 50px;
      top: 0px;
      right: -50px;
      border-radius: 0 3px 3px 0px;
      box-shadow: 0 0 20px 0 rgba(0, 0, 0, 0.1);
      cursor: pointer;
      &:before {
        content: "";
        position: absolute;
        top: 0;
        bottom: 0;
        left: -25px;
        width: 30px;
        background: #fff;
      }
      span {
        position: absolute;
        width: 18px;
        height: 18px;
        border-bottom: 5px solid #f49e47;
        border-right: 5px solid #f49e47;
        top: 50%;
        left: 42%;
        transform: translate(-60%, -45%) rotate(-45deg);
        transition: all 0.4s ease;
      }
    }
  }
  .flat-info {
    width: 100%;
  }
  .rooms .active-mask {
  }
}
@media (max-width: 768px) {
  .app-block {
    padding-left: 0;
    padding-right: 0;
  }
  .s-panel {
    min-width: 250px;
    max-width: 250px;
  }
  .s-panel .title {
    font-size: 17px;
  }
  .s-panel .burger {
    width: 40px;
    height: 40px;
    right: -40px;
  }
  .s-panel .burger span {
    width: 12px;
    height: 12px;
    border-right-width: 4px;
    border-bottom-width: 4px;
  }
  .s-panel {
    left: -240px;
  }
}

@media (max-height: 700px) {
  .s-panel {
    padding-top: 15px;
  }
  .s-panel .title {
    font-size: 18px;
  }
  .title-mini {
    font-size: 10px;
  }
  .list-flats {
    max-height: 38vh;
  }
  .l-block {
    margin-bottom: 20px;
  }
}

@media (max-height: 630px) {
  .s-panel-info > div {
    font-size: 12px;
  }
  .list-flats .item {
    margin-bottom: 15px;
  }
  .s-panel .title {
    margin-bottom: 20px;
  }
}
@media (max-height: 580px) {
  .title-mini {
    margin-bottom: 5px;
  }
  .list-flats {
    max-height: 36vh;
  }
}
</style>
