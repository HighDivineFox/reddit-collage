<template>
  <div id="app">
    <Header :subreddits="reddits" @loadNewSubs="loadNewSubReddits" @modCols="modCols" :searchText="searchText"/>
    <div v-if="error" class="error">
      No results. {{this.errorMessage}}<br /><br />
      <div class="list-style">
        <p>Here some examples to get you started</p>
        <ul>
          <li @click="loadNewSubReddits('aww')">www.reddit.com/r/aww</li>
          <li @click="loadNewSubReddits('eyebleach')">www.reddit.com/r/eyebleach</li>
          <li @click="loadNewSubReddits('earthporn')">www.reddit.com/r/earthporn</li>
          <li @click="loadNewSubReddits('foodporn')">www.reddit.com/r/foodporn</li>
          <li @click="loadNewSubReddits('aww+eyebleach+earthporn+foodporn')">www.reddit.com/r/aww+eyebleach+earthporn+foodporn</li>
        </ul>
      </div>
    </div>
    <div class="imageContainer" v-else>
      <component is="ImageColumn" v-for="(component, index) in myComponents" :ref="'col' + index" :imageWidth="imageWidth" />
    </div>
  </div>
</template>

<script>
import ImageColumn from "./components/ImageColumn"
import Header from "./components/Header"
import { setTimeout } from 'timers';
import { Promise } from 'q';

export default {
  name: 'Reddit-Collage',
  data: function(){
    return{
      screenWidth: 0,
      screenHeight: 0,
      cols: 5,
      fetching: false,
      searchText: "www.reddit.com/r/",
      subreddits: null,
      reddits: null,
      imageData: [],
      myComponents: [],
      totalImages: 0,

      error: false,
      errorMessage: ""
    }
  },
  components: {
    ImageColumn,
    Header
  },
  created(){
    
    if(window.innerWidth < 800) this.cols = 3
    if(window.innerWidth < 800) this.searchText = "/r/"

    this.createComponents()

    window.addEventListener('resize', this.handleResize);

    this.handleResize()

    if(this.reddits == null){
      this.getSubreddits()
    }
    
  },
  destroyed(){
    window.removeEventListener('resize', this.handleResize);
  },
  methods:{
    createComponents(){
      this.myComponents = []
      for(var i = 0; i < this.cols; i++){
        this.myComponents.push(ImageColumn)
      }
    },

    handleResize(){
      this.screenWidth = window.innerWidth
      this.screenHeight = window.innerHeight
    },

    getSubreddits(val){

      if(val){
        this.subreddits = "https://www.reddit.com/r/" + val + ".json?limit=100"
        this.reddits = val
      }else{
        const url = window.location.href

        const re = "[0]\/(.*)"
        const subreddits = url.match(re)
        
        if(subreddits[1] == ""){
          this.error = true
        }else{
          this.subreddits = "https://www.reddit.com/r/" + subreddits[1] + ".json?limit=100"
          this.reddits = subreddits[1]
        }        
      }
    },

    isValid(imageString){
      return imageString.match(/\.(jpg|jpeg|gif|gifv|bmp|png)/)
    },

    getNextImage(){
      let imageURL = this.imageData[0]
      this.imageData.shift()

      return imageURL
    },

    feedImagesToChildren(){

      while(this.imageData.length > 0){
        if(this.getSmallestCol() != undefined){
          this.getSmallestCol().addImageToArray(this.getNextImage())
          this.totalImages++
        }
      }
    },

    getSmallestCol(){
      if(!this.$refs['col0']){
        return
      }

      let smallestCol = this.$refs['col0'][0]

      for(var i = 0; i < this.cols; i++){
        let colToCheck = 'col' + i.toString()

        if(!this.$refs[colToCheck]){
          return
        }

        if(this.$refs[colToCheck][0].getNumOfImages() < smallestCol.getNumOfImages()){
          smallestCol = this.$refs[colToCheck][0]
        }
      }

      return smallestCol
    },

    loadNewSubReddits(newReddits){

      this.error = false
      this.errorMessage = ""

      this.getSubreddits(newReddits)
      

      setTimeout(() => {
        this.clearImages()
      }, 1)      

      this.fetchData()
    },

    clearImages(){
      this.totalImages = 0
      for(var i = 0; i < this.myComponents.length; i++){
        let colToCheck = 'col' + i.toString()
        this.$refs[colToCheck][0].removeImages()
      }
    },

    fetchData(){

      if(this.fetching) return

      this.fetching = true;
      fetch(this.subreddits)
        .then(
          (response) => {

            if(response.status !== 200){
                console.log("Api Problem")
                return
            }

            response.json()
              .then((jsonData) => {
                this.fetching = false;
                //console.log(jsonData.data)

                this.error = true
                this.errorMessage = "No images found"

                for(var i = 1; i < jsonData.data.children.length; i++){
                  if(this.isValid(jsonData.data.children[i].data.url)){
                    this.imageData.push(jsonData.data.children[i].data)

                    this.error = false
                    this.errorMessage = ""
                  }
                }
              })
              .catch((e) => {
                this.error = true
                this.errorMessage = "Invalid subreddit name"
                this.fetching = false;
              })
          }).catch((e) => {
                this.error = true
                this.errorMessage = "Invalid subreddit name"
                this.fetching = false;
              })
    },

    modCols(val){
      this.cols += val[0]

      if(this.cols < 1){
        this.cols = 1
        return
      }
      
      if(val[0] > 0){
        this.myComponents.push(ImageColumn)
      }else{
        this.myComponents.pop()
      }

      this.loadNewSubReddits(val[1])
    }
  },
  computed: {
    imageWidth(){
      return this.screenWidth / this.cols
    }
  },
  mounted: function(){
    this.fetchData()
  },
  watch:{
    imageData(){
      this.feedImagesToChildren()
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

*{
  scrollbar-width: none;
  margin: 0;
  padding: 0;
  border: none;
}

body{
  margin: 0;
  box-sizing: border-box;
}

.imageContainer{
  display: flex;
  flex-flow: row nowrap;
  margin-top: 30px;
}

.error{
  position: absolute;
  width: 100%;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);

  font-size: 2em;
  font-weight: bold;
  text-align: center;
}

.list-style{
  font-size: 0.8em;
}

.list-style ul{
  margin-top: 10px;
  list-style: none;
  font-size: 0.7em;
}

.list-style ul li{
  margin: 5px;
  cursor: pointer;
}
</style>
