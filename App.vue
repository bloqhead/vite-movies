<template>
  <main>
    <div class="controls">
      <div>
        <select v-model="selected">
          <option
            v-for="(category, index) in categories"
            :key="index"
            :value="category"
            :selected="selected"
          >
            {{ category.replace('-',' / ') }}
          </option>
        </select>
      </div>
      <div>
        <Pagination
          :has-prev="page > 0"
          :has-next="page < pageCount -1"
          @next="nextPage"
          @prev="prevPage"
        />
      </div>
    </div>
    <div v-if="loading">
      <p>Loading Movies&hellip;</p>
    </div>
    <div v-else>
      <header>
        <h1>{{ label }} Movies</h1>
      </header>
      <ul class="movie-list">
        <li v-for="movie in paginatedData" :key="movie.id">
          <figure>
            <img
              v-if="movie.posterURL && movie.posterURL.length > 0"
              :src="movie.posterURL"
              :alt="`Movie poster for ${movie.title}`"
              loading="eager"
            >
            <img
              v-else
              src="/assets/image-placeholder@2x.png"
              :alt="`Movie poster for ${movie.title}`"
            >
            <figcaption>
              <h3>{{ movie.title }}</h3>
              <a
                v-if="movie.imdbId"
                :href="`https://www.imdb.com/title/${movie.imdbId}/`">
                  View on IMDB
              </a>
            </figcaption>
          </figure>
        </li>
      </ul>
    </div>
  </main>
</template>

<script>
  import Pagination from './components/Pagination.vue'
  import VLazyImage from 'v-lazy-image'
  import { defineComponent } from 'vue'

  export default defineComponent({
    components: {
      Pagination,
      VLazyImage
    },
    data: () => ({
      loading: true,
      movies: [],
      size: 3,
      page: 0,
      categories: [
        'action-adventure',
        'animation',
        'classic',
        'comedy',
        'drama',
        'horror',
        'family',
        'mystery',
        'scifi-fantasy',
        'western'
      ],
      selected: 'action-adventure'
    }),
    computed: {
      pageCount () {
        const count = Object.entries(this.movies).length
        const size = this.size

        return Math.ceil(count / size)
      },
      paginatedData () {
        const data = this.movies
        const start = this.page * this.size
        const end = start + this.size
        const filtered = data.slice(start, end)

        return filtered
      },
      label () {
        const title = this.selected.replace('-', '/')
        return title.charAt(0).toUpperCase() + title.slice(1);
      }
    },
    mounted () {
      this.fetchMovies()
    },
    watch: {
      selected () {
        this.fetchMovies()
        this.page = 0
      }
    },
    methods: {
      fetchMovies () {
        this.loading = true

        fetch(`https://sampleapis.com/movies/api/${this.selected}`)
          .then(res => res.json())
          .then(data => this.movies = data)
          .catch(err => console.log(err))
          .finally(() => {
            setTimeout(() => {
              this.loading = false
            }, 5000)
          });
      },
      prevPage () {
        this.page--
      },
      nextPage () {
        this.page++
      }
    }
  });
</script>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;700&display=swap');

  :root {
    --color-1: hotpink;
    --color-2: yellow;
    --color-3: #333;
  }

  *, *:before, *:after {
    margin: 0;
  }

  html {
    font-size: 62.5%;
  }

  body {
    background: black;
    color: white;
    display: flex;
    align-items: center;
    justify-content: center;
    min-height: 100vh;
    font-size: 1.8rem;
    font-family: 'Open Sans', sans-serif;
  }

  /* basics */

  a {
    color: var(--color-1);
    transition: color 150ms ease-in-out;
  }

  a:hover, a:active, a:focus {
    color: var(--color-2);
  }

  /* app */

  main {
    margin: 2rem auto;
    width: 100%;
    max-width: 85rem;
  }

  /* controls */

  .controls {
    display: flex;
    margin: 0 0 1rem 0;
  }

  .controls > div:first-of-type {
    margin-right: 1rem;
  }

  .controls select,
  .controls button {
    height: 4rem;
    background-color: var(--color-1);
    border: 0;
    color: #fff;
    border-radius: 3px;
  }

  .controls button {
    text-transform: uppercase;
    padding: 0.5rem 2rem;
  }

  .controls select {
    text-transform: capitalize;
    padding-right: 1rem;
    padding-left: 1rem;
  }

  .controls button:first-of-type {
    margin-right: 1rem;
  }

  /* movie list */

  .movie-list {
    list-style: none;
    padding: 0;
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    gap: 2rem;
    grid-template-areas: ". . .";
  }

  .movie-list li {
    margin: 0;
    padding: 0;
    position: relative;
    border: 3px solid var(--color-3);
    border-radius: 6px;
    overflow: hidden;
    min-height: 421px;
  }

  .movie-list figure {
    display: block;
  }

  .movie-list img {
    width: 100%;
    height: auto;
    object-fit: cover;
  }

  .movie-list h3 {
    font-size: 1.875rem;
    font-weight: 400;
    margin: 0 0 1rem 0;
  }

  figure {
    
  }

  figcaption {
    display: block;
    position: absolute;
    bottom: 0;
    left: 0;
    width: calc(100% - 2rem);
    padding: 1rem;
    background: linear-gradient(to top, #000 25%, rgba(0,0,0,0.35) 100%);
    backdrop-filter: blur(5px);
    box-shadow: 0 0 10px 5px rgba(0,0,0,0.85);
  }

  figure a {
    display: block;
    font-size: 1.4rem;
  }
</style>
