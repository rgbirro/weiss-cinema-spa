<template>
  <div>
    <b-img :src="posterImage" class="image"></b-img>
    <div class="middle">
      <div>
        <b-icon v-if="!inWishlist" icon="heart" class="h3" variant="danger" @click="$emit('addWishlist', movie)">
        </b-icon>
        <b-icon v-else icon="heart-fill" class="h3" variant="danger" @click="$emit('removeWishlist', movie)">
        </b-icon>
      </div>
      <div class="text" @click="$emit('openModal', movie.imdbID)">
        {{ movie.Title }} <br />
        {{ movie.Year }}
      </div>
    </div>

  </div>
</template>

<script>
export default {
  name: "Poster",
  props: ['movie', 'wishlist'],
  data() {
    return {
      modalData: undefined,
      localStorageSize: 0
    };
  },
  computed: {
    inWishlist() {
      return Boolean(this.wishlist ? this.wishlist.find(m => m.imdbID === this.movie.imdbID) : false)
    },
    posterImage() {
      return this.movie.Poster === 'N/A' ? require('../../public/thumb-not-available.png') : this.movie.Poster;
    }
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.image {
  max-width: 100%;
  max-height: 100%;
  vertical-align: top;
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
  text-align: center;
  width: 100%;
  height: 80%;
  display: flex;
  align-items: center;
  justify-content: center;

}

.text p {
  display: inline-block;
  line-height: normal;
  vertical-align: middle;
}

.container:hover .image {
  opacity: 0.3;
}

.container:hover .middle {
  opacity: 1;
}
</style>
