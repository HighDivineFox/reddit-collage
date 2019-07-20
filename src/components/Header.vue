<template>
    <div class="wrapper"  :class="{hideStyle: autoHide}">
        <div class="flex-item subSearch">
            <label for="subredditSearch">{{searchText}}</label>
            <input type="text" name="subredditSearch" id="subredditSearch" :value="subreddits" v-on:keydown.enter="submit">
            <input class="button" type="button" value="Refresh" @click="submit">
        </div>
        <!--
        <div class="flex-item">
            <input type="checkbox" name="autohide" id="autohide" v-model="autoHide" >
            <label for="autohide">Autohide</label>
        </div>
        -->
        <div class="flex-item">
            <input type="button" value="-" @click="modCols(-1)">
            Columns
            <input type="button" value="+" @click="modCols(1)">
        </div>
    </div>
</template>

<script>
export default {
    data: function(){
        return {
            autoHide: false,
            hideStyle: {
                backgroundColor: '#000'
            }
        }
    },
    props:{
        subreddits: String,
        searchText: String
    },
    methods:{
        submit(){
            let newVal = document.getElementById("subredditSearch").value
            //console.log(newVal)
            this.$emit('loadNewSubs', newVal)
        },

        modCols(val){
            //this.$emit('loadNewSubs', document.getElementById("subredditSearch").value)
            this.$emit('modCols', [val, document.getElementById("subredditSearch").value])
        }
    }
}
</script>

<style scoped>
    .wrapper{
        padding: 0px 50px;
        background-image: linear-gradient(-180deg, #555, #000);
        color: aliceblue;

        display: flex;
        flex-flow: row nowrap;
        justify-items: flex-start;

        font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
        font-size: 1.1em;
        font-weight: bold;

        position: fixed;
        top: 0%;
        width: 100%;
        z-index: 99;
    }

    .flex-item{
        width: 100%;
        margin: 3px 10px;
    }

    @media screen and (max-width: 800px) {
        .wrapper{
            padding: 0px 5px;
        }

        .flex-item{
            width: 100%;
            margin: 3px 0px;
        }
    }    

    .flex-item input{
        background: #EEE;
        padding: 2px;
        margin: 0px 5px;
    }

    .subSearch{
        display: flex;
    }

    .subSearch input[type="text"]{
        color: #333;
        flex-grow: 1;
        margin: auto 5px;
        border-radius: 3px;
    }

    @media screen and (max-width: 800px){
        .subSearch input[type="text"]{
            width: 100px;
            color: #333;
            margin: auto 5px;
            border-radius: 3px;
        }
    }

    .flex-item input[type="button"]{
        margin: 2px;
        padding: 2px 8px;
        border-radius: 3px;
        font-size: 0.8em;
        font-weight: bold;
        font-family: inherit;
    }
</style>
