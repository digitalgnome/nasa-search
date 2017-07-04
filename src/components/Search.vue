<template>
  <div class="row">
    <div class="col-xs-12">
      <h4 id="instructions">
        Click Submit/Press Enter for a random image
      </h4>
      <form @submit.prevent>
        <input type="text" :placeholder="placeholderValue" v-model="query" maxlength="30" autofocus>
        <button class="btn btn-primary" @click="getResult(query)">Submit</button>
      </form>
      <div class="row border-style" v-if="imageURL">
        <div class="col-md-6">
          <h4 v-html="imageTitle"></h4>
          <a class="inline-block" href="#search-image">
            <img class="img-format" :src="imageURL" :alt="imageTitle">
          </a>
          <p class="original-image">
            <small>
              <a :href="imageLarge" v-html="imageLarge" target="_blank"></a>
            </small>
          </p>
          <!-- lightbox container hidden with CSS -->
          <a href="#" class="lightbox" id="search-image">
            <img :src="imageURL" :alt="imageTitle">
          </a>
        </div>
        <div class="description col-md-6">
          <p v-html="imageDesc"></p>
        </div>
      </div>
      <div class="row border-style" v-else-if="imageOfDayMedia === 'video'">
        <div class="col-xs-12">
          <h4 v-html="imageOfDayTitle"></h4>
          <div class="video-container">
            <iframe width="853" height="480" src="https://www.youtube.com/embed/dtY44sPNHcU" frameborder="0" allowfullscreen></iframe>
          </div>
          <p class="original-image" style="text-align: center;">
            <small>
              <a :href="imageOfDayURL" v-html="imageOfDayURL" target="_blank"></a>
            </small>
          </p>
        </div>
        <div class="description col-xs-12">
          <p v-html="imageOfDayDesc"></p>
        </div>
      </div>
      <div class="row border-style" v-else>
        <div class="col-md-6">
          <h4 v-html="imageOfDayTitle"></h4>
          <a class="inline-block" href="#daily-image">
            <img class="img-format" :src="imageOfDayURL" :alt="imageOfDayTitle">
          </a>
          <p class="original-image">
            <small>
              <a :href="imageOfDayURL" v-html="imageOfDayURL" target="_blank"></a>
            </small>
          </p>
          <!-- lightbox container hidden with CSS -->
          <a href="#" class="lightbox" id="daily-image">
            <img :src="imageOfDayURL" :alt="imageOfDayTitle">
          </a>
        </div>
        <div class="description col-md-6">
          <p v-html="imageOfDayDesc"></p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'search',
  data() {
    return {
      placeholderValue: 'Type in your search',
      query: '',
      searchQuery: '',
      imageOfDayMedia: '',
      imageOfDayTitle: '',
      imageOfDayDesc: '',
      imageOfDayURL: '',
      imageTitle: '',
      imageDesc: '',
      imageURL: '',
      imageLarge: ''
    }
  },
  methods: {
    startingImage() {
      axios({
        method: 'get',
        url: 'https://api.nasa.gov/planetary/apod?api_key=j0QQvQTSuvEXdi3twtzKgJHTxpSr3UkpH5b5rQno'
      }).then(response => {
        var imageStartData = JSON.stringify(response.data);
        var imageStartParsed = JSON.parse(imageStartData);
        this.imageOfDayMedia = imageStartParsed.media_type;
        this.imageOfDayTitle = imageStartParsed.title;
        this.imageOfDayURL = imageStartParsed.url;
        this.imageOfDayDesc = imageStartParsed.explanation;
        console.log(response.data);
        console.log(this.imageOfDayURL);
      });
    },
    getResult(query) {
      this.searchQuery = String(query.trim()).replace(/(\s+)/g, '+');
      axios({
        method: 'get',
        url: "https://images-api.nasa.gov/search?q=" + this.searchQuery + "&media_type=image",
        responseType: 'json'
      }).then(response => {
        let responseLength = response.data.collection.items.length;
        let noResultBool = false;
        if (responseLength === 0) {
          this.query = '';
          this.searchQuery = '';
          if (this.placeholderValue === 'Type in your search') {
            this.placeholderValue = 'No search results';
          }
        } else if (responseLength > 0) {
          var imagesData = JSON.stringify(response.data.collection.items);
          var imageParsed = JSON.parse(imagesData);
          var imageIndex = Math.floor(Math.random() * imageParsed.length);
          var imageData = imageParsed[imageIndex];
          this.imageTitle = imageData.data[0].title;
          this.imageDesc = imageData.data[0].description;
          this.imageURL = imageData.links[0].href;
          this.imageLarge = this.imageURL.match(/(h.*?~)/)[1] + 'orig.jpg';
          this.placeholderValue = 'Type in your search';
          this.query = '';
          this.searchQuery = '';
          this.responseLength = 0;
        }
      });
    }
  },
  beforeMount() {
    this.startingImage()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h4 {
  color: #004040;
}

h4 {
  font-size: 1.2em;
  line-height: 1.2em;
  margin: 15px auto 0;
  padding: 0;
}

#instructions {
  margin: 0 auto;
}

input[type="text"] {
  line-height: 2em;
  margin: 1em 0.5em 1em 0;
  padding: 0 0 0 0.5em;
}

p {
  font-size: 1.3em;
  text-align: justify;
}

.original-image {
  margin: 0 0 10px;
  padding: 0;
}

a:focus {
  color: #337ab7;
  text-decoration: none;
}

.text-center {
  text-align: center;
}

.inline-block {
  display: inline-block;
}

img {
  margin: 20px auto 5px;
  max-width: 100%;
}

.img-format {
  background: rgba(0, 64, 64, 0.25);
  border: 2px solid rgba(0, 64, 64, 0.5);
  border-radius: 5px;
  display: block;
  padding: 5px;
  height: 400px;
  min-width: 300px;
}

.video-container {
  position: relative;
  padding-bottom: 56.25%;
  padding-top: 30px;
  height: 0;
  overflow: hidden;
}

.video-container iframe,
.video-container object,
.video-container embed {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.border-style {
  border: 2px solid rgba(0, 64, 64, 0.25);
  border-radius: 10px;
}

.description {
  padding: 0.5em 1em 0.75em;
}

@media only screen and (min-width: 992px) {
  .description {
    padding-top: 3em;
  }
}


/** LIGHTBOX MARKUP **/

.lightbox {
  /** Default lightbox to hidden */
  display: none;
  /** Position and style */
  position: fixed;
  z-index: 999;
  width: 100%;
  height: 100%;
  text-align: center;
  vertical-align: middle;
  top: 0;
  left: 0;
  /*background: rgba(0, 0, 0, 0.8);*/
  background-color: #c0c0c0;
}

.lightbox img {
  /** Pad the lightbox image */
  max-width: auto;
  height: 99%;
  margin: 0.25% auto;
}

.lightbox:target {
  /** Remove default browser outline */
  outline: none;
  /** Unhide lightbox **/
  display: block;
}
</style>