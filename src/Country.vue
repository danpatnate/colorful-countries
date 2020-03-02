<template>
  <div class="country-container" :class="{ 'dark': theme === 'dark' }">
    <img v-bind:src="country.flag"  v-bind:alt="`${country.name}'s Flag'`" />
    <div class="country-details">
      <h2>{{ country.name }}</h2>
      <div class="country-details--info">
        <div class="country-details--info-block">
          <div><span>Native Name:</span> {{ country.name }}</div>
          <div><span>Population:</span> {{ country.population.toLocaleString() }}</div>
          <div><span>Region:</span> {{ country.region }}</div>
          <div><span>Sub Region:</span> {{ country.subregion }}</div>
          <div><span>Capital:</span> {{ country.capital }}</div>
        </div>
        <div class="country-details--info-block">
          <div><span>Top Level Domain:</span> {{ country.topLevelDomain[0] }}</div>
          <div><span>Currencies:</span> {{ currencies }}</div>
          <div><span>Languages:</span> {{ languages }}</div>
        </div>
      </div>
      <div v-if="country.borders.length > 0" class="country-details--borders">
        <span>Border Countries:</span>
        <div v-for="borderCountry in borderNames" :key="borderCountry.alpha2Code">
          <div
            class="country-details--borders-pill"
            :class="{ 'dark': theme === 'dark' }"
            @click="fetchBorderingCountry(borderCountry.alpha3Code)"
          >
            {{ borderCountry.name }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'country',
  props: ['country_data', 'theme'],
  data () {
    return {
      country: this.country_data,
      currentCountry: {},
      borderNames: [],
      currencies: "",
      languages: ""
    }
  },
  created () {
    this.fetchBorderCodes();
    this.formatDataStrings();
  },
  methods: {
    setBorderNames (borderingCountries) {
      this.borderNames = borderingCountries;
    },
    fetchBorderCodes () {
      const borderCodes = this.country.borders.join(';');
      fetch(`https://restcountries.eu/rest/v2/alpha?codes=${borderCodes}`)
        .then(res => (
          res.json()
        )).then(this.setBorderNames)
    },
    fetchBorderingCountry (borderCode) {
      fetch(`https://restcountries.eu/rest/v2/alpha?codes=${borderCode}`)
        .then(res => (
          res.json()
        )).then(this.setCountry)
          .then(this.fetchBorderCodes)
          .then(this.formatDataStrings)
    },
    setCountry (response) {
      this.country = response[0];
    },
    formatDataStrings () {
      this.currencies = this.country.currencies.map(cur => cur.name).join(", ");
      this.languages = this.country.languages.map(lng => lng.name).join(", ");
    }
  }
}
</script>

<style lang="scss">
.country-container {
  display: flex;

  h2, span {
    font-weight: 800;
  }

  img {
    height: 500px;
  }

  &.dark {
    color: white;
  }

  .country-details {
    text-align: left;
    padding-left: 40px;
    width: 50%;

    div {
      margin: 6px 0;
    }

    &--info, &--borders {
      display: flex;
      justify-content: space-between;
    }

    &--info {
      &-currencies,
      &-languages {
        display: flex;
        align-items: center;
      }
    }

    &--borders {
      align-items: center;
      flex-wrap: wrap;

      &-pill {
        box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
        border-radius: 5px;
        padding: 2px 10px;
        width: 100px;
        margin: 0 2px !important;
        cursor: pointer;

        &.dark {
          background-color: #2B3945;
          border: none;
        }
      }
    }
  }
}

@media only screen
  and (min-device-width: 320px)
  and (max-device-width: 480px)
  and (-webkit-min-device-pixel-ratio: 2) {
    .country-container {
      display: block;
      margin: 75px 25px;

      img {
        width: 100%;
      }
    }

    .country-details {
      display: flex;
      flex-direction: column;
      width: 100% !important;
      padding-left: 0px !important;

      &--info {
        display: block !important;

        &-block {
          margin-bottom: 50px !important;
        }
      }

      &--borders {
        margin-right: 40px;
      }
    }
}

</style>
