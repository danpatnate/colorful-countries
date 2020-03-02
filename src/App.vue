<template>
  <div id="app">
    <div class="title-bar" :class="{ 'dark': theme === 'dark' }">
      <h1>{{ title }}</h1>
      <button
        class="theme-button"
        :class="{ 'dark': theme === 'dark' }"
        @click="toggleTheme"
      >
        <ion-icon v-if="theme === 'light'" name="moon-outline"></ion-icon>
        <ion-icon v-else name="moon"></ion-icon>
        {{ theme === 'light' ? 'dark' : 'light' }} Mode
      </button>
    </div>
    <div v-if="!showCountryDetails" class="main-container" :class="{ 'dark': theme === 'dark' }">
      <div class="search-header">
        <div class="search-bar" :class="{ 'dark': theme === 'dark' }">
          <ion-icon name="search-outline"></ion-icon>
          <input
            type="text"
            class="search-input"
            placeholder="Search for a country..."
            v-model="query"
            @keypress="fetchCountries"
            :class="{ 'dark': theme === 'dark' }"
          />
        </div>
        <div class="filter-bar" :class="{ 'dark': theme === 'dark' }">
    			<button
            class="filter-button"
            :class="{ 'dark': theme === 'dark' }"
            @click="toggleShowFilter"
          >
            Filter by Region
          </button>
    			<div class="filter-menu" v-if="showFilter" :class="{ 'dark': theme === 'dark' }">
    				<div class="menu-item" :class="{ 'dark': theme === 'dark' }" v-for="filter in this.filterItems" @click="filterItemClicked(filter)">
              {{filter}}
            </div>
    			</div>
          <ion-icon name="chevron-down-outline"></ion-icon>
    		</div>
      </div>
      <div class="countries-container" v-if="countries">
        <div
          class="country-card"
          v-for="country in countries"
          :key="country.name"
          @click="viewCountryDetails(country)"
          :class="{ 'dark': theme === 'dark' }"
        >
          <img v-bind:src="country.flag" v-bind:alt="`${country.name}'s Flag'`"/>
          <div class="country-details" :class="{ 'dark': theme === 'dark' }">
            <h2>{{ country.name }}</h2>
            <div><span>Population:</span> {{ country.population.toLocaleString() }}</div>
            <div><span>Region:</span> {{ country.region }}</div>
            <div v-if="country.capital"><span>Capital:</span> {{ country.capital }}</div>
          </div>
        </div>
      </div>
    </div>
    <div v-else class="main-container" :class="{ 'dark': theme === 'dark' }">
      <button
        class="back-button"
        :class="{ 'dark': theme === 'dark' }"
        @click="goBack"
      >
        <ion-icon name="arrow-back-outline"></ion-icon>
        Back
      </button>
      <Country v-bind:country_data="countries[0]" v-bind:theme="theme"></Country>
    </div>
  </div>
</template>

<script>
import Country from './Country.vue'
export default {
  name: 'app',
  components: {
   'Country': Country,
  },
  data () {
    return {
      title: 'Where in the world?',
      query: '',
      countries: [],
      allCountries: [],
      api_base_url: 'https://restcountries.eu/rest/v2',
      showCountryDetails: false,
      showFilter: false,
      filterItems: ["Africa", "Americas", "Asia", "Europe", "Oceania", "All"],
      theme: 'light'
    }
  },
  mounted () {
    fetch(`${this.api_base_url}/all`)
      .then(res => (
        res.json()
      )).then(this.setCountries)
  },
  methods: {
    fetchCountries (e) {
      if (e.keyCode === 13) {
        fetch(`${this.api_base_url}/name/${this.query}`)
          .then(res => (
            res.json()
          )).then(this.setSearchResults)
      }
    },
    setCountries (searchResults) {
      this.allCountries = searchResults;
      this.countries = searchResults;
    },
    goBack () {
      this.countries = this.allCountries;
      this.query = '';
      this.showCountryDetails = false;
    },
    setSearchResults (searchResults) {
      this.showCountryDetails = !this.showCountryDetails;
      this.countries = searchResults;
    },
    toggleShowFilter () {
      this.showFilter = !this.showFilter;
    },
    filterItemClicked (filter) {
      if (filter === "All") {
        this.countries = this.allCountries;
        this.showFilter = false;
        return;
      }

      this.countries = this.allCountries.filter(country => country.region === filter);
      this.showFilter = false;
    },
    toggleTheme () {
      this.theme = this.theme === 'light' ? 'dark' : 'light';
    },
    viewCountryDetails (country) {
      this.countries = [country];
      this.showCountryDetails = true;
    }
  }
}
</script>

<style lang="scss">

html {
  height: 100%;
}

#app {
  font-family: 'Nunito Sans';
  text-align: center;
  font-size: 14px;
  height: 100%;
}

body {
  margin: 0;
  height: 100%;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

ion-icon {
  font-size: 16px;
}

.main-container {
  background-color: #FAFAFA;
  height: inherit;
  padding: 30px 100px;

  &.dark {
    background-color: #202C37;
    color: #FFFFFF;
  }
}

.title-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 100px;
  font-size: 14px;
  font-weight: 600;
  padding: 0 125px;

  &.dark {
    background-color: #2B3945;
    color: #FFFFFF;
  }
}

.theme-button {
  display: flex;
  align-content: center;
  justify-content: space-between;
  font-size: 16px;
  text-transform: capitalize;
  background: none;
  height: 50px;
  width: 120px;
  border-radius: 5px;
  border-style: none;
  cursor: pointer;

  &.dark {
    color: #FFFFFF;
  }
}

.search-bar, .filter-bar {
  display: flex;
  align-items: center;
  background-color: #FFFFFF;
  height: 60px;
  box-sizing: border-box;
  padding-left: 30px;
  padding-right: 30px;
  max-width: 650px;
  border-radius: 5px;
  margin-top: 20px;
  margin-left: 20px;
  margin-right: 20px;

  &.dark {
    background-color: #2B3945;
    color: #FFFFFF;
  }
}

.filter-bar {
  display: flex;
}

.filter-button {
  border: none;
  font-size: 16px;
  cursor: pointer;
  background-color: #FFFFFF;

  &.dark {
    background-color: #2B3945;
    color: #FFFFFF;
  }
}

.filter-menu {
  position: absolute;
  padding: 0 20px;
  margin-top: 10px;
  top: 185px;
  right: 125px;
  background-color: #FFFFFF;
  border-radius: 5px;
  width: 152px;
  text-align: left;

  &.dark {
    background-color: #2B3945;
    color: #FFFFFF;

    &:hover {
      color: #FFFFFF;
      cursor: pointer;
    }
  }

  .menu-item {
    margin: 6px 0;

    &:hover {
      color: #2B3945;
      cursor: pointer;
    }

    &.dark {
      &:hover {
        color: #FFFFFF;
      }
    }
  }
}

.search-header {
  display: flex;
  justify-content: space-between;
  align-items: center;

  input {
    border-style: none;
    font-size: 16px;
    line-height: 20px;
    padding: 20px;
    padding-left: 25px;
    padding-right: 50px;
    border: 0;
    outline: 0;
    border-radius: 6px;

    &.dark {
      background-color: #2B3945;
      color: #FFFFFF;
    }
  }
}

input[type="text"].dark::-webkit-input-placeholder {
  color: #FFFFFF;
}

.countries-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}

.country-card {
  display:  block;
  flex: 1 1 220px;
  margin: 50px 20px;
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
  transition: 0.3s;
  border-radius: 5px;
  height: 400px;
  cursor: pointer;

  &.dark {
    background-color: #2B3945;
    color: #FFFFFF;
  }

  img {
    border-radius: 5px 5px 0 0;
    max-width: 100%;
  }

  .country-details {
    padding: 5px 25px;
    text-align: left;

    div {
      margin: 5px 0;

      span {
        font-weight: 800;
      }
    }

    &.dark {
      color: #FFFFFF;
    }
  }
}

.back-button {
  display: flex;
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
  transition: 0.3s;
  border-radius: 5px;
  width: 150px;
  padding: 10px 20px;
  margin: 10px 0 35px 0;
  font-size: 16px;
  cursor: pointer;
  border-style: none;
  background-color: #FFFFFF;

  ion-icon {
    margin-right: 10px;
  }

  &.dark {
    background-color: #2B3945;
    border: none;
    color: #FFFFFF;
  }
}

@media only screen
  and (min-device-width: 320px)
  and (max-device-width: 480px)
  and (-webkit-min-device-pixel-ratio: 2) {
    .main-container {
      padding: 10px 50px;
      font-size: 16px;
    }

    .countries-container {
      margin: 40px 40px;
    }

    .country-card {
      flex: 1 1 400px;
      max-width: none;
      height: 100%;
      width: 80%;
      box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
      transition: 0.3s;
      border-radius: 15px;

      img {
        border-radius: 15px 15px 0 0;
        width: 100%;
      }

      .country-details {
        height: 250px;
      }
    }

    .search-header {
      display: block;
    }

    .search-bar {
      width: 100%;
      height: 100px;
      max-width: none;
      border-radius: 12px;
    }

    .title-bar {
      padding: 0 45px;
    }

    .filter-bar {
      border-radius: 12px;
      width: 60%;
      height: 100px;
    }

    .back-button {
      background-color: #FFFFFF;
      box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
      margin: 45px 25px;
      width: 110px;
    }
}

</style>
