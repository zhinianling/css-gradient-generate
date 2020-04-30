<template>
    <div class="gra__box" @mousedown="canWheelPlanting = false">
        <div class="gra__header">
            <h1>css-gradient-generate</h1>
            <div class="gra__header-button-group">
                <button>生成CSS</button>
                <button>生成图片</button>
            </div>
        </div>
        <div class="gra__content"
             @mouseleave="mouseData.isDown = false"
             @mouseup="mouseData.isDown = false"
             @mousemove="mouseMove">
            <uiGradientsList :canWheelPlanting="canWheelPlanting" @change="changeCurrent"></uiGradientsList>
            <div class="gra__gradient-view" :style="{backgroundImage:getGradientColor}">
                <div class="gra__gradient-setting">
                    <div class="gra__gradient-color"
                         @click="addGradientItem"
                         :style="{backgroundImage:getLineColor}"></div>
                    <div class="gra__color-item"
                         :key="index"
                         @mousedown="itemMouseDown($event,item)"
                         :style="{left:item.progress* 600 / 100 - 8 + 'px',background: item.color}"
                         v-for="(item,index) of currentGradientData"></div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import uiGradientsList from './uiGradientsList'
export default {
    name: "cssGradientGenerateNew",
    components:{
        uiGradientsList
    },
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
            angle:0,
            size:40,
            shape:'ellipse',
            canWheelPlanting: true
        }
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
    created(){

    },
    methods:{
        itemMouseDown(e,item){
            this.mouseData.isDown = true;
            this.mouseData.x = e.clientX;
            this.currentItem = item;
        },
        mouseMove(e){
            e.preventDefault();
            if (this.mouseData.isDown){
                this.currentItem.progress += (e.clientX - this.mouseData.x) / 600 * 100;
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
        changeCurrent(e){
            this.currentGradientData = e;
        },
        addGradientItem(e){
            let progress = Math.floor(e.offsetX / 600 * 100);
            let canvas = document.createElement('canvas');
            let ctx = canvas.getContext('2d');
            canvas.width = 100;
            canvas.height = 1;
            let gradient = ctx.createLinearGradient(0,0,100,0);
            for (let i of this.currentGradientData){
                gradient.addColorStop(i.progress / 100,i.color);
            }
            ctx.fillStyle = gradient;
            ctx.fillRect(0,0,100,1);
            let colorData = ctx.getImageData(0,0,100,1).data;
            let color = `rgb(${colorData[progress*4]},${colorData[progress*4+1]},${colorData[progress*4+2]})`;
            let item = {
                progress:progress,
                color:color
            };
            this.currentGradientData.push(item);
            this.currentItem = item;
            this.mouseData.isDown = true;
            this.currentGradientData = this.currentGradientData.sort((a,b)=>{
                return a.progress - b.progress;
            });
        },
    }
}
</script>

<style scoped>
.gra__box{
    width: 100%;
    height: 100vh;
}
.gra__header{
    width: 100%;
    height: 60px;
    padding:0 20px;
    background: #FFFFFF;
    border-bottom: 1px #CCCCCC solid;
    line-height: 60px;
    display: flex;
    justify-content: space-between;
}
.gra__header-button-group{

}
.gra__content{
    width: 100%;
    height: calc(100vh - 60px);
    display: flex;
    flex-direction: row;
}

.gra__gradient-view{
    flex: 1;
    height: 100%;
    position: relative;
}
.gra__gradient-setting{
    width: 600px;
    height: 50px;
    position: absolute;
    bottom: 100px;
    left: calc(50% - 300px);
}
.gra__gradient-color{
    width: 100%;
    margin-top: 5px;
    background: #ffffff;
    border-radius: 5px;
    height: 40px;
    border: 2px #FFFFFF solid;
}
.gra__color-item{
    width: 16px;
    height: 50px;
    border: 2px #FFFFFF solid;
    position: absolute;
    top: 0;
    border-radius: 3px;
    cursor: pointer;
}
</style>
