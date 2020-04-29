<template>
    <div class="gra__box">
        <div class="gra__header">
            <h1>css-gradient-generate</h1>
            <div class="gra__header-button-group">
                <button>生成CSS</button>
                <button>生成图片</button>
            </div>
        </div>
        <div class="gra__content">
            <div class="gra__gradient-list"></div>
            <div class="gra__gradient-view"></div>
        </div>
    </div>
</template>

<script>
export default {
    name: "cssGradientGenerateNew"
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
.gra__gradient-list{
    width: 300px;
    height: 100%;
    background: aquamarine;
}
.gra__gradient-view{
    flex: 1;
    height: 100%;
    background: aqua;
}
</style>
