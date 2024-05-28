<template>
    <div>
      <input v-model="searchQuery" placeholder="Search by country name">
      <button @click="sortAsc">Sort Asc</button>
      <button @click="sortDesc">Sort Desc</button>
      <table>
        <thead>
          <tr>
            <th>Flag</th>
            <th @click="sort('name.official')">Country Name</th>
            <th>Country Code (2)</th>
            <th>Country Code (3)</th>
            <th>Native Name</th>
            <th>Alternative Names</th>
            <th>Calling Codes</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="country in paginatedCountries" :key="country.cca3" @click="selectCountry(country)">
            <td><img :src="country.flags.png" :alt="country.name.official + ' flag'"></td>
            <td>{{ country.name.official }}</td>
            <td>{{ country.cca2 }}</td>
            <td>{{ country.cca3 }}</td>
            <td>{{ country.nativeName }}</td>
            <td>{{ country.altSpellings.join(', ') }}</td>
            <td>{{ country.idd.root }}{{ country.idd.suffixes.join(', ') }}</td>
          </tr>
        </tbody>
      </table>
      <button @click="prevPage" :disabled="page === 1">Previous</button>
      <button @click="nextPage" :disabled="page === pageCount">Next</button>
  
      <CountryModal v-if="selectedCountry" :country="selectedCountry" @close="selectedCountry = null" />
    </div>
  </template>
  
  <script>
  import api from '@/services/api';
  import CountryModal from './CountryModal.vue';
  
  export default {
    components: {
      CountryModal
    },
    data() {
      return {
        countries: [],
        searchQuery: '',
        sortOrder: 'asc',
        page: 1,
        perPage: 25,
        selectedCountry: null
      };
    },
    computed: {
      filteredCountries() {
        return this.countries.filter(country =>
          country.name.official.toLowerCase().includes(this.searchQuery.toLowerCase())
        );
      },
      sortedCountries() {
        return this.filteredCountries.sort((a, b) => {
          let modifier = this.sortOrder === 'asc' ? 1 : -1;
          if (a.name.official < b.name.official) return -1 * modifier;
          if (a.name.official > b.name.official) return 1 * modifier;
          return 0;
        });
      },
      paginatedCountries() {
        const start = (this.page - 1) * this.perPage;
        const end = start + this.perPage;
        return this.sortedCountries.slice(start, end);
      },
      pageCount() {
        return Math.ceil(this.filteredCountries.length / this.perPage);
      }
    },
    methods: {
      fetchCountries() {
        api.getAllCountries().then(response => {
          this.countries = response.data.map(country => {
            return {
              flags: country.flags,
              name: country.name,
              cca2: country.cca2,
              cca3: country.cca3,
              nativeName: Object.values(country.name.nativeName).map(n => n.common).join(', '),
              altSpellings: country.altSpellings,
              idd: country.idd
            };
          });
        });
      },
      sort(order) {
        this.sortOrder = order;
      },
      sortAsc() {
        this.sortOrder = 'asc';
      },
      sortDesc() {
        this.sortOrder = 'desc';
      },
      selectCountry(country) {
        this.selectedCountry = country;
      },
      nextPage() {
        if (this.page < this.pageCount) {
          this.page++;
        }
      },
      prevPage() {
        if (this.page > 1) {
          this.page--;
        }
      }
    },
    created() {
      this.fetchCountries();
    }
  };
  </script>
  