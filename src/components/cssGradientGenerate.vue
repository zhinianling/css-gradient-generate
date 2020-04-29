<template>
  <div class="gradient"
       @mousedown="canWheelPlanting = false"
       @mouseleave="mouseData.isDown = false"
       @mouseup="mouseData.isDown = false">
    <div class="gradient-left">
      <div class="setting-box"
           :style="{padding:styleData.settingBoxPadding+'px'}">
        <span>渐变类型：</span>
        <select v-model="gradientType">
          <option value="line">线性渐变</option>
          <option value="path">径向渐变</option>
        </select>
        <div @mousemove="mouseMove" style="height: 70px;">
          <div class="color-line"
               :style="{width:styleData.colorLineWidth+'px',backgroundImage:getLineColor}"></div>
          <div class="mousedown-box"
               @mousedown="addGradientItem"
               :style="{width:styleData.colorLineWidth+'px'}"></div>
          <div class="slider-item"
               v-for="(item,index) of currentGradientData"
               :key="index"
               @mousedown="itemMouseDown($event,item)"
               :style="{left:item.progress*styleData.colorLineWidth / 100 + styleData.settingBoxPadding - styleData.gradientItemWidth / 2 + 'px',width:styleData.gradientItemWidth+'px'}">
            <div class="triangle"></div>
            <div class="item-color-box"
                 @dblclick="showColorSelect(item)"
                 :style="{background: item.color}"></div>
          </div>
        </div>
        <div class="line-gradient-setting" v-show="gradientType == 'line'">
          <span>角度</span>
          <input type="range" min="0" max="360" v-model="angle">
          <span>{{ angle }}</span>
        </div>
        <div class="path-gradient-setting" v-show="gradientType == 'path'">
          <span>大小</span>
          <input type="range" min="0" max="200" v-model="size">
          <span>{{ size }}%</span>
          <p>形状</p>
          <label><input name="Fruit" type="radio" v-model="shape" value="circle" @focus="shape = 'circle'" />圆形</label>
          <label><input name="Fruit" type="radio" v-model="shape" value="ellipse" @focus="shape = 'ellipse'" />椭圆形</label>
        </div>
        <button>生成图片</button>
        <button @click="generateCss">生成CSS</button>
      </div>
      <div class="gradient-list">
        <p>渐变列表</p>
        <div class="gradient-list-item"
             v-for="(item,index) of gradientList"
             :key="index"
             :style="{backgroundImage:getListBack(item)}"
             @click="changeCurrentGradient(item)">
          <span>{{item.name}}</span>
        </div>
      </div>
    </div>
    <div class="gradient-right" :style="{backgroundImage:getGradientColor}"></div>
    <input type="color"
           ref="colorSelect"
           style="display: none"
           :value="currentItem ? currentItem.color : '#000000'"
           @change="colorChange">
  </div>
</template>

<script>
import Gradients from '../assets/gradients.json';
export default {
  name: 'cssGradientGenerate',
  data(){
    return {
      currentGradientData:[
        {
          progress:0,
          color:'#000000'
        },
        {
          progress:100,
          color:'red'
        }
      ],
      currentItem:null,
      gradientType:'line',
      mouseData:{
        isDown:false,
        x:0
      },
      styleData:{
        colorLineWidth:270,
        gradientItemWidth:18,
        settingBoxPadding:15
      },
      gradientList:[],
      angle:0,
      size:40,
      shape:'ellipse',
      canWheelPlanting: true
    }
  },
  created(){
    this.parseGradients();
    this.wheelPlanting();
  },
  computed:{
    getLineColor(){
      let color = `linear-gradient(90deg`;
      for (let item of this.currentGradientData){
        color += `,${item.color} ${item.progress}%`
      }
      return color;
    },
    getGradientColor(){
      let color = "";
      if (this.gradientType === 'line'){
        color = `linear-gradient(${90 + parseFloat(this.angle)}deg`;
        for (let item of this.currentGradientData){
          color += `,${item.color} ${item.progress}%`
        }
      } else{
        color = `radial-gradient(${this.shape} at center ${this.size}`;
        for (let item of this.currentGradientData){
          color += `,${item.color} ${item.progress}%`
        }
      }
      return color;
    }
  },
  methods:{
    getListBack(item){
      let color = `linear-gradient(90deg`;
      for (let gradient of item.gradientData){
        color += `,${gradient.color} ${gradient.progress}%`
      }
      return color;
    },
    itemMouseDown(e,item){
      this.mouseData.isDown = true;
      this.mouseData.x = e.clientX;
      this.currentItem = item;
    },
    mouseMove(e){
      e.preventDefault();
      if (this.mouseData.isDown){
        this.currentItem.progress += (e.clientX - this.mouseData.x) / this.styleData.colorLineWidth * 100;
        if (this.currentItem.progress > 100){
          this.currentItem.progress = 100;
        }
        if (this.currentItem.progress < 0){
          this.currentItem.progress = 0;
        }
        this.currentGradientData = this.currentGradientData.sort((a,b)=>{
          return a.progress - b.progress;
        });
        this.mouseData.x = e.clientX;
      }
    },
    addGradientItem(e){
      let progress = e.offsetX / this.styleData.colorLineWidth * 100;
      this.currentGradientData.push({
        progress:progress,
        color:'#000'
      });
      this.currentGradientData = this.currentGradientData.sort((a,b)=>{
        return a.progress - b.progress;
      });
    },
    parseGradients(){
      this.gradientList = Gradients.map(item=>{
        let gradientData = [];
        let progress = parseFloat((100 / (item.colors.length - 1)).toFixed(2));
        for (let i = 0; i < item.colors.length;i++){
          gradientData.push({
            progress: i == 0 ? 0 : (i == item.colors.length) ? 100 : progress * i,
            color: item.colors[i]
          })
        }
        return {
          name:item.name,
          colors:item.colors,
          gradientData
        }
      })
    },
    changeCurrentGradient(item){
      this.currentGradientData = JSON.parse(JSON.stringify(item.gradientData));
    },
    showColorSelect(item){
      this.currentItem = item;
      this.$refs['colorSelect'].click();
    },
    colorChange(e){
      this.currentItem.color = e.target.value;
    },
    wheelPlanting(){
      this.changeCurrentGradient(this.gradientList[Math.floor(Math.random() * this.gradientList.length)]);
      setTimeout(()=>{
        if (this.canWheelPlanting) this.wheelPlanting();
      },3000)
    },
    generateCss(){
      console.log(this.getGradientColor);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.gradient{
  width: 100%;
  height: 100vh;
  display: flex;
}
.gradient-left{
  width: 300px;
  height: 100%;
  background: #FFFFFF;
}
.setting-box{
  width: 300px;
  height: 300px;
  padding: 15px;
  position: relative;
}
.color-line{
  width: 270px;
  height: 30px;
  background: blue;
  margin-top: 20px;
}
.mousedown-box{
  width: 270px;
  height: 30px;
  position: absolute;
}
.slider-item{
  width: 18px;
  height: 27px;
  position: absolute;
  cursor: pointer;
}
.triangle{
  width: 0;
  height: 0;
  border-left: 9px solid transparent;
  border-right: 9px solid transparent;
  border-bottom: 9px solid #000;
}
.item-color-box{
  width: 18px;
  height: 18px;
  border: 1px #000 solid;
  border-radius: 0 0 3px 3px;
  box-shadow: inset 0 0 0 1px #FFF;
}
.gradient-right{
  flex: 1;
  height: 100%;
}
.gradient-list{
  width: 100%;
  height: calc(100vh - 300px);
  overflow-y: auto;
}
.gradient-list-item{
  width: 250px;
  height: 80px;
  margin-left: 15px;
  margin-bottom: 15px;
  text-align: center;
  line-height: 80px;
  color: #CCC;
  cursor: pointer;
}
.gradient-list-item span{
  cursor: pointer;
}
</style>
