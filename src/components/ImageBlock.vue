<template>
    <div class="wrapper" @mouseenter="overlay = true" @mouseleave="overlay = false" @click="goToLink">
        <div 
            v-if="!loaded && !isVideo"
            class="loading"
            :style="{
                width: singleImageWidth + 'px',
                height: '300px'
            }"
        >
                <img src="../assets/loading.gif" />
        </div>
        <video 
            v-if="isVideo && !isGfycat"
            :style="{
                width: singleImageWidth +'px'
            }"
            autoplay="true"
            loop="true"
            muted="true"
            
        >
                <source :src="convertLink()" type="video/mp4">
        </video>
        <img 
            v-if="!isVideo && !isGfycat"
            :src="link"
            :style="{
                width: singleImageWidth +'px'
            }"
            v-on:load="onLoaded"
            v-show="loaded"
        />
    </div>
</template>

<script>
import { setTimeout } from 'timers';

export default {
    props: {
        data: Object,
        singleImageWidth: Number
    },
    data: function () {
        return {
            link: 'loading',
            isVideo: false,
            isGfycat: false,
            loaded: false
        }
    },
    created(){
        this.getLink()
    },
    methods: {
        getLink() {
            this.link = this.data.url

            if(this.link.search(".gifv") != -1){
                this.isVideo = true
            }

            if(this.link.search(".gfycat") != -1){
                this.isGfycat = true
            }
        },

        convertLink(){
            return this.data.url.replace(".gifv", ".mp4")
        },

        onLoaded(){
            this.loaded = true
        },

        goToLink(){
            window.open('https://www.reddit.com' + this.data.permalink, "_blank")
        }
    }
}
</script>

<style scoped>
    .wrapper{
        opacity: 1;
        transition: opacity 0.5s;
        position: relative;
    }

    .wrapper:hover{
        opacity: 1;
        transition: opacity 0.5s;
    }

    .loading{
        text-align: center;
        position: relative;
        background-image: linear-gradient(-180deg, #BBB, #FFF, #BBB)
    }

    .loading img{
        position: absolute;
        top: 50%;
        transform: translate(-50%, -50%)
    }
</style>
