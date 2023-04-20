<template>
  <div>
    <h1>Daftar Film Girls</h1>
    <div id="app">
      <div class="container-filter">
        <div>
          <label for="genre">Genre:</label>
          <select id="genre" v-model="selectedGenre" @change="filterShows">
            <option value="">All Genres</option>
            <option v-for="genre in genres" :key="genre" :value="genre">
              {{ genre }}
            </option>
          </select>
        </div>
        <div>
          <label for="rating">Rating:</label>
          <select id="rating" v-model="selectedRating" @change="filterShows">
            <option value="">All Ratings</option>
            <option v-for="rating in ratings" :key="rating" :value="rating">
              {{ rating }}
            </option>
          </select>
        </div>
      </div>
      <div class="card-container">
        <div v-for="show in filteredShows" :key="show.show.id" class="card">
          <img :src="show.show.image.medium" :alt="show.show.name" />
          <h2>{{ show.show.name }}</h2>
          <p v-html="show.show.summary"></p>
          <p>Genre: {{ show.show.genres.join(', ') }}</p>
          <p>Rating: {{ show.show.rating.average }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      shows: [],
      filteredShows: [],
      genres: [],
      ratings: [],
      selectedGenre: '',
      selectedRating: ''
    }
  },
  mounted() {
    fetch('http://api.tvmaze.com/search/shows?q=girls')
      .then((response) => response.json())
      .then((data) => {
        this.shows = data
        this.filteredShows = data
        this.genres = [...new Set(data.flatMap((show) => show.show.genres))]
        this.ratings = [...new Set(data.flatMap((show) => show.show.rating.average))]
      })
  },
  methods: {
    filterShows() {
      let filteredShows = this.shows
      if (this.selectedGenre !== '') {
        filteredShows = filteredShows.filter((show) =>
          show.show.genres.includes(this.selectedGenre)
        )
      }
      if (this.selectedRating !== '') {
        filteredShows = filteredShows.filter(
          (show) => show.show.rating.average === this.selectedRating
        )
      }
      this.filteredShows = filteredShows
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=PT+Serif&family=Sedgwick+Ave+Display&display=swap');

h1 {
  text-align: center;
  padding: 10px;
  font-family: 'PT Serif', serif;
  font-family: 'Sedgwick Ave Display', cursive;
}

.container-filter {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  grid-gap: 10px;
  justify-content: center;
  align-items: center;
  margin: 20px;
}

select {
  padding: 0.5rem;
  font-size: 1rem;
  border: 1px solid #ccc;
  border-radius: 0.25rem;
  background-color: #fff;
  margin-right: 1rem;
  margin-bottom: 1rem;
  width: 100%;
}

select option {
  background-color: #fff;
  color: #000;
}

.card-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  grid-gap: 20px;
  margin: 20px;
}

.card {
  display: flex;
  flex-direction: column;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
  padding: 1rem;
}

.card img {
  max-width: 100%;
  height: auto;
  margin-bottom: 1rem;
}

.card h2 {
  font-size: 1.25rem;
  margin-bottom: 0.5rem;
}

.card p {
  font-size: 1rem;
  line-height: 1.5;
}

@media only screen and (max-width: 600px) {
  .container-filter {
    display: flex;
    flex-direction: row;
    margin: 20px 0;
  }

  select {
    width: 100%;
    margin-right: 0;
    margin-bottom: 10px;
  }

  .card-container {
    display: flex;
    flex-direction: column;
    margin: 20px 0;
  }
}
</style>
