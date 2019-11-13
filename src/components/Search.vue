<template>
  <div class="text-left">
    <label for="search-artist">Search artist</label>

    <input
      id="search-artist"
      class="form-input"
      v-model="searchValue"
      v-on:keyup="getSearchResult($event)"
      placeholder="Search..."
      aria-label="Search for..."
      autocomplete="off"
      spellcheck="false"
      autofocus="true"
    />

    <div class="artist-selector">
      <div
        class="artist-selector-item"
        v-for="(artist, index) in computedSearchResult"
        :key="index"
        v-on:click="selectArtist(artist)"
      >{{artist.name}}</div>
    </div>

    <div class="search-history" v-if="searchHistory.length > 0">
      <section>
        <h1 class="h6">Search history</h1>
        <a class="search-history-clear" v-on:click="removeSearchHistory()">clear all</a>
        <div class="search-history-element" v-for="(artist, index) in searchHistory" :key="index">
          <div>{{artist.name}}</div>
          <div>{{artist.currentTime}}</div>
          <i class="fas fa-times-circle pointer" v-on:click="removeSearchHistory(artist)"></i>
        </div>
      </section>
    </div>

    <div v-if="selectedArtist" class="selected-artist">
      <section>
        <div class="selected-artist-body">
          <img
            v-if="selectedArtist.images && selectedArtist.images.length > 0"
            :src="selectedArtist.images[1].url"
            :alt="selectedArtist.name"
            class="selected-artist-image"
          />

          <div class="selected-artist-text">
            <h1 class="h5 artist-mb">{{selectedArtist.name}}</h1>
            <div class="artist-mb">
              <div
                :key="index"
                class="genre"
                v-for="(genre, index) in selectedArtist.genres"
              >{{genre}}</div>
            </div>
            <a
              :href="selectedArtist.uri"
              v-if="selectedArtist.uri"
              target="_blank"
            >{{selectedArtist.name}} on spotify</a>
          </div>
        </div>
      </section>
    </div>

    <notifications position="bottom left" />
  </div>
</template>

<script>
import Vue from "vue";
import VueResource from "vue-resource";
import moment from "moment/src/moment";
import Notifications from "vue-notification";
import token from "../assets/token.js";

Vue.use(VueResource);
Vue.use(Notifications);

export default {
  name: "Search",
  data: function() {
    return {
      searchResultArtists: [],
      searchResultArtistsLimit: 7,
      searchValue: "",
      selectedArtist: null,
      searchHistory: [],
      searchByEnter: false
    };
  },
  computed: {
    computedSearchResult() {
      return this.searchResultArtistsLimit
        ? this.searchResultArtists.slice(0, this.searchResultArtistsLimit)
        : this.searchResultArtists;
    }
  },
  methods: {
    removeSearchHistory: function(artist = null) {
      if (artist) {
        this.searchHistory = this.searchHistory.filter(
          el => el.id !== artist.id
        );
      } else {
        this.searchHistory = [];
      }
    },
    selectArtist: function(artist) {
      this.selectedArtist = artist;
      this.addToSearchHistory(artist);
    },
    addToSearchHistory(artist) {
      artist.currentTime = moment().format("YYYY-MM-DD HH:mm");
      this.searchHistory.push(artist);
      this.searchValue = "";
    },
    getSearchResult: function($event) {
      console.log(token);

      window.searchByEnter = $event.key === "Enter";
      window.searchValue = this.searchValue;

      const url = `https://api.spotify.com/v1/search?q=${this.searchValue}&type=artist&market=US`;

      const auth = { headers: { Authorization: token } };

      window.notify = this.$notify;

      Vue.http.get(url, auth).then(
        response => {
          if (window.searchByEnter) {
            this.selectedArtist = response.data.artists.items.find(
              x => x.name.toLowerCase() === window.searchValue.toLowerCase()
            );

            if (this.selectedArtist) {
              this.addToSearchHistory(this.selectedArtist);
            } else {
              window.notify({
                type: "warn",
                text: "Your search did not match any artists",
                duration: 1000
              });
            }
          } else {
            this.searchResultArtists = response.data.artists.items;
          }
        },
        function(error) {
          let errorMessage = "Ops, something whent wrong. Please try again.";
          if (error.body.error.message === "The access token expired") {
            errorMessage =
              "Spotify needs a new token. Please update the token in this file: 'src/assets/token.js'. Sorry for the inconvenience. -Eric";
          }
          window.notify({
            type: "error",
            text: errorMessage,
            duration: 2000
          });
        }
      );
    }
  }
};
</script>


<style lang="scss">
.artist-selector {
  border-left: 1px solid rgba(0, 0, 0, 0.05);
  border-right: 1px solid rgba(0, 0, 0, 0.05);
  border-bottom: 1px solid rgba(0, 0, 0, 0.05);
  &-item {
    cursor: pointer;
    padding: 0.5rem 1rem;
    font-size: 0.7rem;
    text-align: left;
    &:hover {
      background-color: rgba(0, 0, 0, 0.03);
    }
    &:nth-of-type(odd) {
      background-color: rgba(0, 0, 0, 0.02);
      &:hover {
        background-color: rgba(0, 0, 0, 0.04);
      }
    }
  }
}
.selected-artist {
  position: relative;
  display: -ms-flexbox;
  display: flex;
  -ms-flex-direction: column;
  flex-direction: column;
  min-width: 0;
  word-wrap: break-word;
  background-color: #fff;
  background-clip: border-box;
  border: 1px solid rgba(0, 0, 0, 0.125);
  border-radius: 0.25rem;
  &-image {
    width: 100%;
    border-top-left-radius: calc(0.25rem - 1px);
    border-top-right-radius: calc(0.25rem - 1px);
  }
  &-body {
    display: flex;
    flex-wrap: wrap;
    .selected-artist-image,
    .selected-artist-text {
      @media (min-width: 576px) {
        flex: 0 0 50%;
        max-width: 50%;
      }
    }
    .selected-artist-text {
      padding: 1.2rem;
    }
  }
}

.genre {
  display: inline-block;
  padding: 0.25em 0.4em 0.25rem 0;
  font-size: 75%;
  font-weight: 700;
  line-height: 1;
  text-align: center;
  white-space: nowrap;
  vertical-align: baseline;
  border-radius: 0.25rem;
  transition: color 0.15s ease-in-out, background-color 0.15s ease-in-out,
    border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
}
.artist-mb {
  margin-bottom: 0.4rem;
}

.search-history,
.selected-artist,
.artist-selector {
  margin-bottom: 1.5rem;
}

.search-history {
  border: 1px solid rgba(0, 0, 0, 0.3);
  border-radius: 0.25rem;
  font-size: 0.7rem;
  padding: 0.3rem 0.6rem;
  position: relative;

  &-clear {
    font-style: italic;
    font-size: 0.6rem;
    position: absolute;
    top: 0.3rem;
    right: 0.6rem;
    color: #1c1f22;
    text-decoration: underline;
  }

  &-element {
    border-bottom: 1px solid rgba(0, 0, 0, 0.125);
    position: relative;
    .fas {
      position: absolute;
      right: 0.5rem;
      top: 0.6rem;
    }
    &:last-child {
      border: none;
    }
  }
}
</style>

