<template>
  <b-container fluid class="bg-black">
    <b-row class="justify-content-center">
      <b-col sm="4">
        <div style="float: left" class="p-1 pr-2">
          <b-icon icon="search" class="h3" variant="light"></b-icon>
        </div>
        <div style="display: flex">
          <b-form-input
            v-model="search"
            class="weiss-cinema"
            type="search"
            placeholder="Search for movies"
            @keyup="searchApi(search)"
          ></b-form-input>
        </div>
      </b-col>
    </b-row>
    <p v-if="search" class="serach-result-label">
      Search Results for "{{ search }}"
    </p>
    <div v-if="searchResult">
      <hr class="solid" />
      <div
        v-for="result in searchResult"
        :key="result.imdbID"
        class="container"
      >
        <b-img fluid :src="result.Poster" class="p-1 image"></b-img>
        <div class="middle">
          <b-icon
            v-if="!inWishlist(result.imdbID)"
            icon="heart"
            class="h3"
            variant="danger"
            @click="addWishlist(result)"
          ></b-icon>
          <b-icon
            v-else
            icon="heart-fill"
            class="h3"
            variant="danger"
            @click="removeWishlist(result)"
          ></b-icon>
          <div class="text">
            {{ result.Title }} <br /><br />
            {{ result.Year }}
          </div>
        </div>
      </div>
    </div>
    <div v-if="search && !searchResult">
      <hr class="solid" />
      <p class="serach-result-label">No results for this search</p>
    </div>
    <div v-if="wishlist.length !== 0">
      <p class="serach-result-label">Your Wishlist</p>
      <hr class="solid" />
      <div v-for="result in wishlist" :key="result.imdbID" class="container">
        <b-img fluid :src="result.Poster" class="p-1 image"></b-img>
        <div class="middle">
          <b-icon
            icon="heart-fill"
            class="h3"
            variant="danger"
            @click="removeWishlist(result)"
          ></b-icon>
          <div class="text">
            {{ result.Title }} <br /><br />
            {{ result.Year }}
          </div>
        </div>
      </div>
    </div>
  </b-container>
</template>

<script>
import axios from "axios";
export default {
  name: "MovieSearch",
  data() {
    return {
      search: undefined,
      searchResult: undefined,
      wishlist: [],
    };
  },
  methods: {
    searchApi() {
      axios
        .get("http://localhost:8080/movies?search=" + this.search)
        .then((response) => {
          if (response.data.Response === "True") {
            this.searchResult = response.data.Search;
          } else {
            this.searchResult = null;
          }
        });
    },
    addWishlist(result) {
      console.log(result);
      if (!localStorage.getItem(result)) {
        this.wishlist.push(result);
        localStorage.setItem(result.imdbID, JSON.stringify(result));
      }
    },
    removeWishlist(result) {
      console.log(result);
      if (localStorage.getItem(result.imdbID)) {
        localStorage.removeItem(result.imdbID);
        var test = this.wishlist.findIndex((w) => w.imdbID === result.imdbID);
        console.log(test);
        this.wishlist.splice(test, 1);
      }
    },
    inWishlist(result) {
      return localStorage.getItem(result);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.weiss-cinema {
  background-color: black;
  color: white;
  border: 1px solid yellow;
}
.weiss-cinema:focus {
  background-color: black;
  color: white;
  border: 1px solid yellow;
}
.bg-black {
  height: 100vh;
  background-color: black;
  overflow: auto;
}
hr.solid {
  border-top: 2px solid gray;
}
.serach-result-label {
  color: white;
  font-size: 20px;
}
.container {
  width: 300px;
  display: inline-block;
  position: relative;
}
.image {
  display: block;
  width: 100%;
  height: auto;
}
.middle {
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  height: 100%;
  width: 100%;
  opacity: 0;
  transition: 0.5s ease;
}
.text {
  color: yellow;
  font-size: 20px;
  position: absolute;
  top: 50%;
  left: 50%;
  -webkit-transform: translate(-50%, -50%);
  -ms-transform: translate(-50%, -50%);
  transform: translate(-50%, -50%);
  text-align: center;
}
.container:hover .image {
  opacity: 0.3;
}

.container:hover .middle {
  opacity: 1;
}
</style>
