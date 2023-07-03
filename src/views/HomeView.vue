<template>
  <main class="container text-white">
  <div class="relative pt-4 mb-8">
      <input v-model="searchKey" @input="search" type="text" placeholder="Search for a city or state" class=" w-full px-3 py-2 bg-transparent border-b focus:border-blue-900 focus:outline-none focus:shadow-md">
      <ul class=" absolute px-2 bg-blue-950 w-full top-14 z-20">
        <li v-if="searchError" class="border-b py-3">There is no place.</li>
        <li v-for="item in result" :key="item.id" class="border-b py-3" @click="toCityView(item)">{{ item.place_name }}</li>
      </ul>
    </div>
    <div class="">
      <myCity :city='myCity'></myCity>
    </div>
  </main>
</template>

<script>
  import myCity from '../components/myCity.vue';
  import axios from 'axios'
  export default {
    name: "HomeView",
    data() {
        return {
            apiKey: "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q",
            searchKey: null,
            queryTimeOut: null,
            result: {},
            searchError: false,
            myCity: {},
        };
    },
    methods: {
        search() {
            if (this.searchKey != null && this.searchKey != "") {
                this.searchError = false;
                clearTimeout(this.queryTimeOut);
                this.queryTimeOut = setTimeout(() => {
                    axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${this.searchKey}.json?access_token=${this.apiKey}&types=place`).then((response) => {
                        this.result = response.data.features;
                        if (this.result.length == 0 && this.searchKey != null && this.searchKey != "") {
                            this.searchError = true;
                        }
                    });
                }, 300);
            }
        },
        toCityView(item) {
            const [city, state] = item.place_name.split(",");
            this.$router.push({
                name: "CityView",
                params: { city: city, state: state.replaceAll(" ", "") },
                query: {
                    lat: item.geometry.coordinates[1],
                    lng: item.geometry.coordinates[0],
                }
            });
        },
    },
    components: { 
      myCity
     }
}
</script>

