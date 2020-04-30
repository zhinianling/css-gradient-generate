<template>
<div class="gra__gradient-list">
    <h3>列表</h3>
    <div class="gra__list-y">
        <div class="gra__list-item"
             v-for="(item,index) of gradientList"
             :key="index"
             :style="{backgroundImage:getListBack(item)}"
             @click="changeCurrentGradient(item)">
            <span>{{item.name}}</span>
        </div>
    </div>
</div>
</template>

<script>
import Gradients from '../assets/gradients.json';

export default {
    name: "uiGradientsList",
    data(){
        return {
            gradientList:[]
        }
    },
    props:{
        canWheelPlanting:{
            default:true,
            type:Boolean
        }
    },
    created() {
        this.parseGradients();
        this.wheelPlanting();
    },
    methods:{
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
        wheelPlanting(){
            this.changeCurrentGradient(this.gradientList[Math.floor(Math.random() * this.gradientList.length)]);
            setTimeout(()=>{
                if (this.canWheelPlanting) this.wheelPlanting();
            },3000)
        },
        changeCurrentGradient(item){
            // this.currentGradientData = JSON.parse(JSON.stringify(item.gradientData));
            this.$emit('change',JSON.parse(JSON.stringify(item.gradientData)))
        },
        getListBack(item){
            let color = `linear-gradient(90deg`;
            for (let gradient of item.gradientData){
                color += `,${gradient.color} ${gradient.progress}%`
            }
            return color;
        }
    }
}
</script>

<style scoped>
.gra__gradient-list{
    width: 300px;
    height: 100%;
}
.gra__list-y{
    overflow-y: auto;
    height: calc(100% - 50px);
}
.gra__list-y{

}
.gra__list-item{
    width: 280px;
    height: 80px;
    cursor: pointer;
    margin-bottom: 10px;
    border-radius: 5px;
    text-align: center;
    line-height: 80px;
    color: #CCCCCC;
}
</style>
