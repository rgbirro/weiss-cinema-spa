<template>
  <div>
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
  </div>
</template>

<script>
import Poster from "./Poster.vue"
export default {
  components: {
    Poster
  },
  name: "MovieDetails",
  props: ['modalData', 'wishlist'],
  data() {
    return {      
      localStorageSize: 0
    };
  },  
  methods: {
    addWishlist(result) {
      this.$emit('addWishlist', result)
    },
    removeWishlist(result) {
      this.$emit('removeWishlist', result)
    },
    async openModal(imdbID) {
      this.$emit('openModal', imdbID)
    },
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
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
</style>
