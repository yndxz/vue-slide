
<template>
    <div class="imageList" ref="image_list" @mouseenter="stop" @mouseleave="start">
        <img src="../assets/button_1.jpg"/>
        <img src="../assets/button_2.jpg"/>
        <img src="../assets/button_3.jpg"/>
        <img src="../assets/button_4.jpg"/>
        <img src="../assets/button_5.jpg"/>
        <ul class="btnList" ref="btn_list">
          <li v-for="(item,i) in pages" @click="scrollTop(i);" :class="{'active':i === index}">
            <a href="javascript:void(0);" v-if="!numberPage"></a>
            <a href="javascript:void(0);" v-if="numberPage">{{i + 1}}</a>
          </li>
        </ul>
        <a href="javascript:void(0);" v-if="arrow" class="arrow left-arrow" @click="prev"></a>
        <a href="javascript:void(0);" v-if="arrow" class="arrow right-arrow" @click="next"></a>
    </div>
</template>
<style scoped>
    *{
        margin: 0;
        padding: 0;
    }
    a{
        color: black;
        text-decoration: none;
    }
    .imageList{
        position: relative;
        margin: 100px auto 100px auto;
        width: 980px;
        height: 100px;
        overflow: hidden;
    }
    .imageList img{
        position: absolute;
        top:0;
        left: 0;
        display: block;
        opacity: 0;
        width: 980px;
        height: 100px;
    }
    .btnList{
        position: absolute;
        bottom: 5px;
        left: 50%;
        margin-left: -75px;
    }
    .btnList li{
        float: left;
        list-style: none;
    }
    .btnList li{
        margin: 0 5px 0 5px;
        border-radius: 50%;
        text-align: center;
        background: #CCCCCC;
    }
    .btnList li a{
        display: block;
        text-align: center;
        width:10px;
        height: 10px;
        line-height: 10px;
        font-size:12px;
    }
    .btnList li:hover,.btnList li.active{
        background: #E1DB18;
    }
    .arrow{
        display:block;
        width:20px;
        height:20px;
        position:absolute;
        top:50%;
        margin-top:-10px;
    }
    .left-arrow{
        left:20px;
        background:url("./../assets/left_arrow.png") no-repeat;
        background-size: contain;
    }
    .right-arrow{
        right:20px;
        background:url("./../assets/right_arrow.png") no-repeat;
        background-size: contain;
    }
</style>
<script scoped>
    import Vue from "vue";
    export default {
        mounted(){
            this.scrollImage();
        },
        data(){
            return {
                imageList:null,
                interval:null,
                index:0,
                status:0,
                current:0,
                pages:''
            }
        },
        props:{
            speed:{
                type:Number,
                default:10
            },
            spend:{
                type:Number,
                default:2000
            },
            numberPage:{
                type:Boolean,
                default:false
            },
            arrow:{
                type:Boolean,
                default:true
            }
        },
        methods:{
            scrollImage(){
                this.init();
                this.start();
            },
            init(){
                this.imageList = this.$refs.image_list.getElementsByTagName('img');
                this.pages = this.imageList.length;
                this.imageList[0].style.opacity = '1';
            },
            getStyle(obj,attr){
                var styleList = getComputedStyle(obj,false) || obj.currentStyle;
                return styleList[attr];
            },
            scrollTop(index){
                var current = index;
                if(this.index == 0){
                    this.index = this.pages;
                }
                if(this.index == this.pages){
                    this.index = 0;
                }
                if(this.index == current){
                    return;
                }
                this.index = current;
                this.animation(this.imageList[this.current],0);
                this.animation(this.imageList[this.index],100);
                this.current = current;
            },
            start(){
                this.interval = setInterval(() => {
                    this.index++;
                    if(this.index >= this.pages){
                        this.index = 0;
                    }
                    var current = this.index;
                    this.animation(this.imageList[this.current],0);
                    this.animation(this.imageList[this.index],100);
                    this.current = current;
                },this.spend);
            },
            animation(obj,offset){
                if(obj.interVal){
                    clearInterval(obj.interVal);
                }
                obj.interVal = setInterval(() => {
                    var value = Math.round(parseFloat(this.getStyle(obj,'opacity') * 100));
                    var step = (parseFloat(offset) - value) / this.speed;
                    step = step > 0 ? Math.ceil(step) : Math.floor(step);
                    if(value == offset){
                        clearInterval(obj.interVal);
                    }else{
                        obj.style.opacity = (value + step) / 100;
                    }
                },50);

            },
            stop(){
                clearInterval(this.interval);
            },
            prev(){
                this.scrollTop(--this.index);
            },
            next(){
                this.scrollTop(++this.index);
            }
        }
    };
</script>