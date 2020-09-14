<template>
  <div id="container">
    <h1 id="app-name">TheMovieDB</h1>
    <div id="search-bar">
      <input id="search-txt" type="text" v-model="search_input" />
      <button id="search-btn" @click="searchMovie">
        <i class="fas fa-search s-icon"></i>
      </button>
    </div>
    <div v-if="mode == 0" id="movie-div">
      <div class="movie-case" v-for="item in movies" :key="item.id" @click="enterMovie(item)">
        <!-- <router-link :to="'/movie/' + item.id"> -->
        <img
          v-if="item.poster_path"
          :src="`https://image.tmdb.org/t/p/original/${item.poster_path}`"
        />
        <img v-else :src="require('../../imgs/movie-poster.jpg')" />
        <h4 id="m-title">{{ tinyTitle(item.title) }}</h4>
        <!-- </router-link> -->
      </div>
    </div>
    <div v-if="mode == 1" id="movie-details">
      <div>
        <button id="backBtn" @click="backHome">
          <i class="fas fa-arrow-circle-left" style="color: white; font-size: 30px"></i>
        </button>
      </div>
      <div id="details-dev">
        <div id="details-img">
          <img
            v-if="movie.poster_path"
            :src="`https://image.tmdb.org/t/p/original/${movie.poster_path}`"
          />
          <img v-else :src="require('../../imgs/movie-poster.jpg')" />
        </div>
        <div id="details-txt">
          <h1>{{ movie.title }}</h1>
          <p>{{ movie.release_date }}</p>
          <h3>{{ movie.overview }}</h3>
        </div>
      </div>
    </div>
    <div v-if="mode == 0 && pages_full.length > 0" id="pagination">
      <button v-if="pages[0] > 3" class="pagination-btn" @click="selectPage(1)">1</button>
      <button v-if="pages[0] > 3" class="pagination-btn" @click="prevPages">
        <i class="fas fa-angle-double-left" style="color: white; font-size: 16px"></i>
      </button>
      <button
        class="pagination-btn"
        v-for="page in pages"
        @click="selectPage(page)"
        :key="page"
      >{{ page }}</button>
      <button v-if="pages_full.length > 3 && !lastPage" class="pagination-btn" @click="nextPages">
        <i class="fas fa-angle-double-right" style="color: white; font-size: 16px"></i>
      </button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      search_input: "",
      mode: 0,
      movie: {}, // Movie object for details view
      movies: [], // Our movie list based off search
      apiKey: "6ac722593e5dba1c8e10c2f8dda07f65",
      baseURL: "https://api.themoviedb.org/3/",
      page: 1,
      grouped: [],
      pages: [],
      lastPage: false,
      set: 0,
      pages_full: [],
    };
  },

  methods: {
    searchMovie() {
      this.mode = 0;
      var url =
        this.baseURL +
        "search/movie?api_key=" +
        this.apiKey +
        "&query=" +
        this.search_input +
        "&page=" +
        this.page;
      fetch(url)
        .then((response) => response.json())
        .then((data) => {
          this.movies = data.results;
          this.pages = [];
          this.page = 1;
          let pagesArr = [];
          let arr = [];
          for (let i = 1, count = 0; count < data.total_pages; count++, i++) {
            pagesArr.push(i);
            arr.push(i);
          }
          this.pages_full = pagesArr;
          if (this.pages.length < 1) {
            this.pages = arr.slice(0, 3);
          }
          let cut = Math.ceil(arr.length / 3);

          for (let i = 0; i < cut; i++) {
            let first = arr.splice(0, 3);
            this.grouped.push(first);
          }
        })
        .catch((err) => {
          alert(err);
        });
    },
    tinyTitle(title) {
      return title.slice(0, 18) + "..";
    },
    selectPage(id) {
      // Researches based off new set page
      this.page = id;
      this.searchMovie();
    },
    // Switches between searchs and details
    backHome() {
      this.mode = 0;
    },
    enterMovie(item) {
      this.mode = 1;
      this.movie = item;
    },
    nextPages() {
      this.set = this.set + 1;
      this.pages = this.grouped[this.set];
      let last = this.pages_full.slice(-1);
      if (this.pages.includes(last[0])) {
        this.lastPage = true;
      } else {
        this.lastPage = false;
      }
    },
    prevPages() {
      this.set = this.set - 1;
      this.pages = this.grouped[this.set];
      let last = this.pages_full.slice(-1);
      if (!this.pages.includes(last[0])) {
        this.lastPage = false;
      }
    },
  },
};
</script>

<style scoped>
#container {
  margin: 0 15%;
}
#app-name {
  font-size: 60px;
  font-family: "Comic Neue", cursive;
  color: #fff;
}
#search-bar {
  position: relative;
  width: 100%;
  display: grid;
  height: 34px;
  grid-template-columns: 11fr 1fr;
  background-color: white;
}
#search-txt {
  border: none;
  background-color: inherit;
  height: inherit;
  padding: 0 2%;
  font-size: 20px;
  font-family: "Comic Neue", cursive;
}
#search-btn {
  border: none;
  background-color: inherit;
  height: 100%;
  width: 100%;
}
.s-icon {
  color: #424242;
  font-size: 22px;
}
#movie-details {
  margin-top: 20px;
}
#movie-div {
  position: relative;
  width: 99.82%;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  margin-top: 20px;
  grid-gap: 0.5em;
}
.movie-case {
  width: 100%;
  height: fit-content;
  border: 1px solid #fff;
  color: #222222;
  background-color: #fff;
  cursor: pointer;
}
.movie-case > img {
  width: 100%;
  height: 260px;
}
.movie-case > h4 {
  font-size: 14px;
  margin: 0;
  overflow-y: hidden;
}
#pagination {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}
.pagination-btn {
  background-color: dodgerblue;
  width: 50px;
  height: 50px;
  margin: 0 5px;
  border: 1px solid dodgerblue;
  border-radius: 50%;
  color: #fff;
  cursor: pointer;
}
#backBtn {
  display: block;
  border: none;
  background-color: inherit;
  cursor: pointer;
}
#details-dev {
  position: relative;
  width: 100%;
  color: #fff;
  display: grid;
  grid-template-columns: 3fr 5fr;
  margin-top: 16px;
}

#details-img {
  position: relative;
  width: 100%;
  height: auto;
}
#details-img > img {
  width: 100%;
  height: auto;
}
#details-txt {
  position: relative;
  display: inline-block;
  float: left;
  width: 92%;
  padding: 5%;
}
/* desktop */
@media (max-width: 780px) {
  #container {
    margin: 0 10%;
  }
  #app-name {
    font-size: 45px;
  }
  .movie-case > img {
    width: 100%;
    height: 240px;
  }
  #movie-div {
    grid-template-columns: 1fr 1fr 1fr;
  }

  #details-txt > h1 {
    font-size: 18px;
  }
  #details-txt > h3 {
    font-size: 16px;
  }
  #details-txt > p {
    font-size: 14px;
  }
}
/* mobile */
@media (max-width: 425px) {
  #container {
    margin: 0 5%;
  }
  #app-name {
    font-size: 30px;
  }
  #search-bar {
    height: 28px;
  }
  .s-icon {
    font-size: 16px;
  }
  #movie-div {
    grid-template-columns: 1fr 1fr;
  }
  .movie-case > img {
    width: 100%;
    height: 220px;
  }
  .movie-case > h4 {
    font-size: 12px;
  }
  #details-dev {
    grid-template-columns: 1fr;
  }
  #details-txt {
    margin-top: 5px;
  }
  #details-txt > h1 {
    font-size: 16px;
  }
  #details-txt > h3 {
    font-size: 14px;
  }
  #details-txt > p {
    font-size: 12px;
  }
  .pagination-btn {
    width: 30px;
    height: 30px;
  }
}
</style>
