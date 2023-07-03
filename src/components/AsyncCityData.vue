<template>
        <div class=" flex flex-col items-center flex-1" v-if="!loadingState">
            <div class=" text-center py-5 bg-blue-900 w-full">
                <p>You are currently previewing this city, click the "+"icon to start tracking this city.</p>
            </div>

            <div class=" flex flex-col items-center text-white py-12">
                <h1 class=" text-4xl mb-2">{{ this.city }}</h1>
                <p class="text-sm mb-12">
                    {{ 
                        new Date(this.weatherData.data.currentTime).toLocaleDateString(
                            'en-us',
                            {
                                weekday: "short",
                                day: "2-digit",
                                month: "long",
                            }
                        )
                    }}
                    {{ 
                        new Date(this.weatherData.data.currentTime).toLocaleTimeString(
                            'en-us',
                            {
                                timeStyle:'short'
                            }
                        )
                    }}
                </p>
            </div>
        </div>
</template>

<script>
  import axios from 'axios'
    export default {
        name:'AsyncCityData',
        data () {
            return {
                city: this.$route.params.city,
                state:this.$route.params.state,
                lat:this.$route.query.lat,
                lng:this.$route.query.lng,
                weatherData:'',
            }
        },
        methods: {
            getWeatherData () {
                axios.get(`https://api.openweathermap.org/data/2.5/onecall?lat=${this.lat}&lon=${this.lng}&exclude={part}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`).then((response)=>{
                    this.weatherData=response;
                    
                    const localOffset = new Date().getTimezoneOffset() * 60000;
                    const utc = this.weatherData.data.current.dt * 1000 + localOffset;
                    this.weatherData.data.currentTime = utc + 1000 * this.weatherData.data.timezone_offset; 

                    // cal hourly weather offset
                    this.weatherData.data.hourly.forEach((hour) => {
                        const utc = hour.dt * 1000 + localOffset;
                        hour.currentTime =utc + 1000 * this.weatherData.data.timezone_offset;
                    });
                })
            }
        },
        mounted () {
            this.getWeatherData();
        }
    }
</script>
