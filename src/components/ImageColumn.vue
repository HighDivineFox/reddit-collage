<template>
    <div class="wrapper">
        <ImageBlock v-for="data in imageData" :data="data" :singleImageWidth="imageWidth" />
    </div>
</template>

<script>
import ImageBlock from "./ImageBlock"

export default {
    components:{
        ImageBlock
    },
    props: {
        imageWidth: Number
    },
    data: function(){
        return{
            imageData: []
        }
    },
    methods:{
        getNumOfImages(){
            return this.imageData.length
        },

        addImageToArray(val){
            this.imageData.push(val)
        },

        removeImages(){
            this.imageData = []
        },

        getAllImages(){
            return this.imageData
        }
    }
}
</script>

<style scoped>
    .wrapper{
        display: flex;
        flex-flow: column nowrap;
    }
</style>
