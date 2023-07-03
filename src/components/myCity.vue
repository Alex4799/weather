<template>
    <div>
        <div v-if="myCityStatus">

            <div v-if="!loadingStatus">
                <div v-for="(city,index) in myCity" :key="index" class="flex flex-col gap-y-3" @click="goCityView(city)">
                    <div class="flex bg-black opacity-50 py-4 px-5 rounded-md shadow-md justify-between">
                        <div class="flex flex-col items-center justify-start">
                            <h1 class="text-2xl text-white">{{city.city}}</h1>
                            <span class="text-sm text-white">{{city.state}}</span>
                        </div>
                        <div class="flex flex-col items-center justify-end">
                            <h1 class=" text-2xl">{{Math.round(city.weatherData.main.temp)}} &deg;</h1>
                            <div>
                                <span>Min : {{Math.round(city.weatherData.main.temp_min)}} &deg;</span>
                                <span>Max : {{Math.round(city.weatherData.main.temp_max)}} &deg;</span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <div v-if="loadingStatus">
                <h1 class=" text-black">Loading ......</h1>
            </div>

        </div>

        <div v-if="!myCityStatus">
            <div class=" bg-black opacity-50 py-4 px-5 rounded-md shadow-md">
                There is no save city.
            </div>
        </div>

    </div>
</template>

<script>
  import axios from 'axios'
    export default {
        name:'myCity',
        data () {
            return {
                myCity: {},
                weatherData:[],
                loadingStatus:true,
                myCityStatus:false,
            }
        },
        methods: {
            getMyCity () {
                this.myCity=JSON.parse(localStorage.getItem('mycity'));
            },
            getWeather(){
                if(this.myCity.length!=0){
                    this.myCityStatus=true;
                    this.myCity.forEach((city) => {
                        axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`).then((response)=>{
                            this.weatherData.push(response);
                            city.weatherData=response.data;
                            this.loadingStatus=false;
                        })
                    });
                }else{
                    this.myCityStatus=false;
                }

            },
            goCityView(city){
                this.$router.push({
                    name: "CityView",
                    params: { city: city.city, state: city.state},
                    query: {
                        lat: city.coords.lat,
                        lng: city.coords.lng,
                    }
                }); 
            }
        },
        beforeMount () {
            this.getWeather();
        },
        created () {
            this.getMyCity();
        }
    }
</script>
