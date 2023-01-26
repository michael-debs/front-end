<template>
    <div>
        <header
            class="h-[100vh] header-bg bg-no-repeat bg-cover grid grid-rows-[80%_20%] grid-cols-[15%_70%_15%] text-center relative">
            <div class="row-start-1 col-start-2 flex items-center justify-center flex-col">
                <h1 class="text-5xl font-serif mb-3">Mint & Jade</h1>
                <h2 class="mb-5">Open from 9 AM till 1 AM</h2>
                <p>~By Y.K.</p>
            </div>
            <div class="absolute right-6 bottom-4 flex items-center justify-end w-fit gap-6">
                <div v-if="isAuth" @click="this.$router.push('/dashboard')" class="text-xl font-bold text-black/50 cursor-pointer">
                Dashboard
                </div>
                <a href="https://www.instagram.com/mintandjade/" target="_blank" class=""><img class="w-8 opacity-50"
                        src="../assets/instagram-brands.svg" alt="Instagram Logo"></a>
            </div>
        </header>



        <main>
            <div class="w-full h-fit py-10 px-20 flex justify-start flex-col items-center">
                <div class="flex justify-start flex-col items-center" v-for="section in data">

                    <h2 class=" mb-8 text-3xl text-[#59d460] font-semibold">{{ section.name }}</h2>
                    <div class="mb-14" v-for="category in section.categories">

                        <div class="w-[325px] h-fit">
                            <div class="relative flex">
                                <img class="w-10 absolute left-[-2.5rem]" src="../assets/icon.svg" alt="Leaf">
                                <h2 class="ml-1">{{ category.name }}</h2>
                            </div>
                            <div class="mt-1 border-2 rounded-md p-4 border-[#59d460]">
                                <div class="flex flex-col flex-wrap leading-7">
                                    <div class="relative max-h-[28px]" v-for="item in category.items">
                                        <div class="flex justify-between">
                                            <!-- wrapper -->
                                            <div class="w-[75%]">
                                                <img src="../assets/dot.svg" alt=""
                                                    class="w-[8px] rounded-full absolute top-[.6rem]">
                                                <p class="ml-5"> {{ item.name }}</p>
                                            </div>
                                            <div class="w-[75px]">
                                                {{ item.price }} LL
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="mt-4 italic" v-if="category.note">
                                    Note: {{ category.note }}
                                </div>
                            </div>
                        </div>

                    </div>
                </div>
            </div>
        </main>



    </div>
</template>


<script>
import Data from '../Json/Data.json' assert {type: 'json'};

export default {
    data() {
        return {
            data: undefined,
            isAuth: false
        }
    },
    mounted() {
        if (window.localStorage.getItem('isAuthenticated') === 'true') {
            this.isAuth = true;
        }
    },
    methods: {
        async fetchData() {
            await fetch('https://zealous-cyan-katydid.cyclic.app/get-items', { method: 'GET' })
                .then((result) => {
                    return result.json()
                })
                .then(items => {
                    this.items = items
                })
                .catch((err) => {
                    console.log(err);
                });

            await fetch('https://zealous-cyan-katydid.cyclic.app/get-sections', { method: 'GET' })
                .then((result) => {
                    return result.json()
                })
                .then(sections => {
                    this.sections = sections
                })
                .catch((err) => {
                    console.log(err);
                });

            await fetch('https://zealous-cyan-katydid.cyclic.app/get-categories', { method: 'GET' })
                .then((result) => {
                    return result.json()
                })
                .then(categories => {
                    this.categories = categories
                })
                .catch((err) => {
                    console.log(err);
                });

            this.data = [...this.sections]

            for (let i = 0; i < this.data.length; i++) {
                let section = this.data[i]
                let result = this.categories.filter((category) => category.section_id == section._id)
                section.categories = result

                for (let j = 0; j < section.categories.length; j++) {
                    const category = section.categories[j];
                    let items = this.items.filter(item => item.category_id == category._id)
                    category.items = items
                }
            }
        },
    },
    created() {
        this.fetchData()
    }

}
</script>
