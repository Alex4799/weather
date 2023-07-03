<template>
    <header class="sticky top-0 bg-blue-600 shadow-md z-20">
        <nav class=" container flex flex-col sm:flex-row items-center gap-5 py-6 text-white">
            <RouterLink to="/">
                <div class="flex items-center gap-3">
                    <i class="fa-solid fa-sun ml-5 text-2xl"></i>
                    <span class="text-2xl">The Local Weather</span>
                </div>
            </RouterLink>

            <div class="flex gap-4 flex-1 justify-end">
                <i class="fa-solid fa-circle-info text-xl transform hover:scale-150 duration-500" @click="infoClick"></i>
            </div>

            <div v-if="status" class="absolute w-full top-0 left-0 flex justify-center px-8">
                <div class="p-4 bg-gray-200 mt-32 max-w-screen-md rounded-lg">
                    <div class="text-black">
                        <h1 class="text-2xl mb-1">About:</h1>
                        <p class="mb-4">
                            The Local Weather allows you to track the current and
                            future weather of cities of your choosing.
                        </p>
                        <h2 class="text-2xl">How it works:</h2>
                        <ol class="list-decimal list-inside mb-4">
                            <li>
                            Search for your city by entering the name into the
                            search bar.
                            </li>
                            <li>
                            Select a city within the results, this will take
                            you to the current weather for your selection.
                            </li>
                            <li>
                            Track the city by clicking on the "+" icon in the
                            top right. This will save the city to view at a
                            later time on the home page.
                            </li>
                        </ol>

                        <h2 class="text-2xl">Removing a city</h2>
                        <p>
                            If you no longer wish to track a city, simply select
                            the city within the home page. At the bottom of the
                            page, there will be am option to delete the city.
                        </p>
                    </div>
                    <div class="flex justify-end">
                        <button class=" rounded-lg mt-8 text-white bg-blue-600 py-2 px-6 " @click="infoClick">Close</button>
                    </div>
                </div>
            </div>
        </nav>
    </header>
</template>

<script>
    import { RouterLink } from 'vue-router';
    export default {
        name:'NavComponents',
        data () {
            return {
                status: false,
            }
        },
        methods: {
            infoClick () {
                if (this.status) {
                    this.status=false;
                }else{
                    this.status=true;
                }
            }
        },
    }
</script>
