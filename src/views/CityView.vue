<template>
    <div class=" relative">
        <div class="text-center my-3" v-if="loadingState">
            loaing.......
        </div>

        <div class=" flex flex-col items-center flex-1" v-if="!loadingState">
            <div class=" flex gap-10 justify-center py-5 bg-blue-900 w-full" v-if="!saveStatus">
                <p>You are currently previewing this city, click the "+"icon to start tracking this city.</p>
                <i class="fa-solid fa-plus text-xl transform hover:scale-150 duration-500 text-white" @click="addCity"></i>
            </div>

            <div class=" flex flex-col items-center text-white pt-4">
                <h1 class=" text-4xl mb-2">{{ city }}</h1>
                <p class="text-sm mb-4">
                    {{ 
                        new Date(weatherData.data.currentTime).toLocaleDateString(
                            'en-us',
                            {
                                weekday: "short",
                                day: "2-digit",
                                month: "long",
                            }
                        )
                    }}
                    {{ 
                        new Date(weatherData.data.currentTime).toLocaleTimeString(
                            'en-us',
                            {
                                timeStyle:'short'
                            }
                        )
                    }}
                </p>
                <p v-if="fahrenheitStatus" class=" text-8xl mb-6">{{ Math.round(weatherData.data.current.temp) }}&deg;F</p>
                <p v-if="fahrenheitStatus">Feel Like {{ Math.round(weatherData.data.current.feels_like) }}&deg;F</p>

                <p v-if="!fahrenheitStatus" class=" text-8xl mb-6">{{ Math.round((weatherData.data.current.temp-32) *(5/9)) }}&deg;C</p>
                <p v-if="!fahrenheitStatus">Feel Like {{ Math.round((weatherData.data.current.feels_like-32) *(5/9)) }}&deg;C</p>

                <p class="capitalize">{{ weatherData.data.current.weather[0].description }}</p>
                
                <img class=" w-60 h-auto" :src='`http://openweathermap.org/img/wn/${this.weatherData.data.current.weather[0].icon}@2x.png`' alt="">

            </div>

            <hr class=" border-white w-full border">
        
            <div class=" max-w-screen-md w-full py-10 text-white">
                <div class="mx-4">
                    <h2 class=" mb-3">Hourly Weather</h2>
                    <div class=" overflow-x-scroll flex flex-row gap-10">
                        <div v-for="hourWeather in weatherData.data.hourly" :key="hourWeather.dt" class=" flex flex-col items-center">
                            <p class=" whitespace-nowrap text-md">
                                {{ 
                                    new Date(hourWeather.currentTime).toLocaleTimeString(
                                        'en-us',
                                        {
                                            hour:'numeric'
                                        }
                                    )
                                }}
                            </p>

                            <img class="w-auto h-[50px] object-cover" :src='`http://openweathermap.org/img/wn/${hourWeather.weather[0].icon}@2x.png`' alt="">

                            <p class="text-xl" v-if="fahrenheitStatus">{{ Math.round(hourWeather.temp) }}&deg;F</p>

                            <p class="text-xl" v-if="!fahrenheitStatus">{{ Math.round((hourWeather.temp-32) *(5/9)) }}&deg;C</p>


                        </div>
                    </div>
                </div>
            </div>

            <hr class=" border-white w-full border">

            <div class=" max-w-screen-md w-full py-10">
                <div class=" text-white mx-4">
                    <h2 class="mb-4">7 Day Forecast</h2>

                    <div class="flex flex-col gap-5">
                        <div v-for="day in weatherData.data.daily" :key="day.dt" class="flex items-center justify-around gap-4">
                            <p class="">
                                {{ 
                                    new Date(day.dt *1000).toLocaleDateString(
                                        'en-us',
                                        {
                                            weekday:'long'
                                        }
                                    )
                                }}

                            </p>
                            <img :src="`http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`" class="" alt="">
                            <div>
                                <p v-if="fahrenheitStatus">Max - {{ Math.round(day.temp.max) }}&deg;F</p>
                                <p v-if="fahrenheitStatus">Min - {{ Math.round(day.temp.min) }}&deg;F</p>

                                <p v-if="!fahrenheitStatus">Max - {{ Math.round((day.temp.max-32) *(5/9)) }}&deg;C</p>
                                <p v-if="!fahrenheitStatus">Min - {{ Math.round((day.temp.min-32) *(5/9)) }}&deg;C</p>

                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <div class=" flex justify-center py-8" v-if="saveStatus">
                <h1 class=" text-2xl text-red-700 fw-bolder" @click="removeCity">Remove City from Save List</h1>
            </div>

            <div class=" absolute top-0 right-0">
                <span v-if="fahrenheitStatus" @click="changeTempUnit" class=" text-2xl text-white py-3 px-1 bg-gray-800 rounded-md m-3">To &deg;C</span>
                <span v-if="!fahrenheitStatus" @click="changeTempUnit" class=" text-2xl text-white py-3 px-1 bg-gray-800 rounded-md m-3">To &deg;F</span>
            </div>
        </div>

    </div>
</template>

<script>
  import axios from 'axios'
    export default {
        name:'CityView',
        data () {
            return {
                city: this.$route.params.city,
                state:this.$route.params.state,
                lat:this.$route.query.lat,
                lng:this.$route.query.lng,
                weatherData:'',
                loadingState:true,
                currentWIcon:'',
                hi:0,
                di:0,
                hourWIcon:[],
                dayWIcon:[],
                mycity:[],
                saveStatus:false,
                fahrenheitStatus:true,
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

                    this.loadingState=false;
                })

                if(JSON.parse(localStorage.getItem('mycity'))!=null){
                    this.mycity=JSON.parse(localStorage.getItem('mycity'));
                    this.mycity.forEach((city)=>{
                        if (city.city==this.city) {
                            this.saveStatus=true;
                        }
                    })
                }
            },
            addCity(){
                this.mycity.push({
                    state: this.state,
                    city: this.city,
                    coords: {
                    lat: this.lat,
                    lng: this.lng,
                    },
                })
                localStorage.setItem('mycity', JSON.stringify(this.mycity));
                this.saveStatus=true;
            },
            removeCity(){
                this.mycity=this.mycity.filter((city)=>{
                    return city.city!=this.city
                });
                localStorage.setItem('mycity', JSON.stringify(this.mycity));
                this.$router.push({
                    name:'home'
                });
            },
            changeTempUnit(){
                if(this.fahrenheitStatus){
                    this.fahrenheitStatus=false;
                }else{
                    this.fahrenheitStatus=true;
                }
            }
        },
        mounted () {
            this.getWeatherData();
        }
    }
</script>
