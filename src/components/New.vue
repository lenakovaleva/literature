<template>
    <v-app>
      <v-main>
        <v-container>
          <v-row>
            <v-col cols="12">
              <v-card>
                <v-card-title>
                  <v-row>
                    <v-col cols="12" sm="6">
                      <v-select
                        v-model="selectedCountries"
                        :items="availableCountries"
                        label="Фильтр по странам"
                        multiple
                        chips
                        :value="defaultCountries"
                      ></v-select>
                    </v-col>
                    <v-col cols="12" sm="6" class="d-flex align-center">
                      <v-btn
                        @click="resetFilters"
                        color="primary"
                        class="ml-auto"
                      >
                        Сбросить фильтры
                      </v-btn>
                    </v-col>
                  </v-row>
                </v-card-title>
                <v-data-table
                  :headers="headers"
                  :items="filteredWorks"
                  :items-per-page="10"
                  class="elevation-1"
                  :sort-by="['creation_date']"
                  :sort-desc="[true]"
                >
                  <template v-slot:item.creation_date="{ item }">
                    {{ formatDate(item.creation_date) }}
                  </template>
                </v-data-table>
              </v-card>
            </v-col>
          </v-row>
        </v-container>
      </v-main>
    </v-app>
  </template>
  
  <script>
  export default {
    data() {
      return {
        works: [],
        headers: [
          { text: 'Дата создания', value: 'creation_date', sortable: true },
          { text: 'Название', value: 'title', sortable: true },
          { text: 'Автор', value: 'author', sortable: true },
          { text: 'Страна', value: 'country', sortable: true },
          { text: 'Жанр', value: 'genre', sortable: true },
          { text: 'Эпоха', value: 'era', sortable: true },
        ],
        selectedCountries: [],
        defaultCountries: ['Англия', 'Франция', 'Германия', 'Россия', 'США', 'Италия', 'Испания'],
        availableCountries: [],
      };
    },
    computed: {
      filteredWorks() {
        if (this.selectedCountries.length === 0) {
          return this.works.filter(work => 
            this.defaultCountries.includes(work.country)
          );
        }
        return this.works.filter(work => 
          this.selectedCountries.includes(work.country)
        );
      },
    },
    methods: {
      formatDate(dateString) {
        const date = new Date(dateString);
        const year = date.getFullYear();
        
        if (year === NaN) {
          return `${Math.abs(year)} год до н.э.`;
        }
        return `${year} год`;
      },
      resetFilters() {
        this.selectedCountries = [...this.defaultCountries];
      },
        async loadData() {
            const response = await fetch('data.json');
            const data = await response.json();
            this.works = data.list_of_works;

            this.availableCountries = [...new Set(this.works.map(work => work.country))];
            this.selectedCountries = [...this.defaultCountries];
        }
    },
    mounted() {
      this.loadData();
    }
  };
  </script>