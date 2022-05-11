<template>
  <b-container fluid class="bg-black">
    <b-row class="py-5">
      <b-col sm="4" class="justify-content-center">
        <div style="float: left" class="p-1 pr-2">
          <b-icon icon="camera-reels-fill" class="h1" style="color:red"></b-icon>
        </div>
        <div>
          <h1 style="color:red">WEISS CINEMA</h1>
        </div>
      </b-col>
      <b-col sm="4">
        <div style="float: left" class="p-1 pr-2">
          <b-icon icon="search" class="h3" variant="light"></b-icon>
        </div>
        <div style="display: flex">
          <b-form-input v-model="search" class="weiss-cinema" type="search" placeholder="Search for movies"
            @keyup="searchApi(search)"></b-form-input>
        </div>
      </b-col>
    </b-row>
    <p v-if="search" class="serach-result-label">
      Search Results for "{{ search }}"
    </p>
    <b-overlay :show="showModal" rounded="sm" variant="light" opacity="0.20">
      <div v-if="searchResult">
        <hr class="solid" />
        <div v-for="movie in searchResult" :key="movie.imdbID" class="container p-2">
          <Poster :movie="movie" @openModal="openModal" @addWishlist="addWishlist" @removeWishlist="removeWishlist"
            :wishlist="wishlist" />
        </div>
        <hr class="solid" />
      </div>
      <div v-if="search && !searchResult">
        <hr class="solid" />
        <p class="serach-result-label">No results for this search. Error: {{ errorMessage }}</p>
        <hr class="solid" />
      </div>
      <div v-if="wishlist.length !== 0">
        <p class="serach-result-label">Your Wishlist</p>
        <hr class="solid" />
        <div v-for="movie in wishlist" :key="movie.imdbID" class="container">
          <Poster :movie="movie" @openModal="openModal" @addWishlist="addWishlist" @removeWishlist="removeWishlist"
            :wishlist="wishlist" />
        </div>
      </div>

      <b-modal v-if="modalData" id="modal-xl" size="xl" hide-header hide-footer body-bg-variant="dark">
        <b-row>
          <b-col sm="3" class="p-2">
            <div class="container">
              <Poster :movie="modalData" @openModal="openModal" @addWishlist="addWishlist"
                @removeWishlist="removeWishlist" :wishlist="wishlist" />
            </div>
          </b-col>
          <b-col>
            <div class="movie-details">
              <p class="h2">
                {{ modalData.Title }}<br />
                {{ modalData.Year }}
              </p>
              <p>
                <label>Release Date:</label> <span>{{ modalData.Released }}</span>
              </p>
              <p>
                <label>Genres:</label> <span>{{ modalData.Genre }}</span>
              </p>
              <p>
                <label>Director:</label> <span>{{ modalData.Director }}</span>
              </p>
              <p>
                <label>Actors:</label> <span>{{ modalData.Actors }}</span>
              </p>
              <p>
                <label>Plot:</label> <span>{{ modalData.Plot }}</span>
              </p>
              <p>
                <label>IMDB Rating:</label> <span>{{ modalData.imdbRating }}</span>
              </p>
              <p>
                <label>Website:</label><span> {{ modalData.Website }}</span>
              </p>
            </div>
          </b-col>
        </b-row>
      </b-modal>
    </b-overlay>
  </b-container>
</template>

<script>
import axios from "axios";
import Poster from "./Poster.vue"
export default {
  components: {
    Poster
  },
  name: "MovieSearch",
  data() {
    return {
      search: undefined,
      searchResult: undefined,
      wishlist: [],
      modalData: undefined,
      errorMessage: undefined,
      showModal: false,
      requests: []
    };
  },
  methods: {
    searchApi() {
      this.requests.forEach(element => {
        element.cancel();
      });
      this.requests = []
      var newRequest = axios.CancelToken.source();
      this.requests.push(newRequest);
      axios
        .get("http://localhost:8080/movies?search=" + this.search, { cancelToken: newRequest.token })
        .then((response) => {
          if (response.data.Response === "True") {
            this.searchResult = response.data.Search;
          } else {
            this.searchResult = null;
            this.errorMessage = response.data.Error
          }
        }).finally(() => {
          this.requests.splice(this.requests.findIndex(a => a.token === newRequest.token));
        });
    },
    addWishlist(result) {
      if (!localStorage.getItem(result)) {
        this.wishlist.push(result);
        localStorage.setItem(result.imdbID, JSON.stringify(result));
      }
    },
    removeWishlist(result) {
      if (localStorage.getItem(result.imdbID)) {
        localStorage.removeItem(result.imdbID);
        var index = this.wishlist.findIndex((w) => w.imdbID === result.imdbID);
        this.wishlist.splice(index, 1);
      }
    },

    async openModal(imdbID) {
      this.modalData = null
      this.showModal = true
      await axios
        .get("http://localhost:8080/movies/" + imdbID + "/details")
        .then((response) => {
          if (response.data.Response === "True") {
            this.modalData = response.data;
          } else {
            this.modalData = null;
          }
        });
      this.$bvModal.show('modal-xl')
      this.showModal = false
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

.container:hover .image {
  opacity: 0.3;
}

.container:hover .middle {
  opacity: 1;
}

.movie-details {
  color: white;
}

label {
  color: yellow;
  font-weight: bold;
  font-size: larger;
  display: inline;
}

span {
  font-size: larger;
}
</style>
