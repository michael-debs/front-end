<template>
    <div class=" flex justify-center items-center w-full h-[100vh]">
        <form @submit.prevent="submit" class="w-[350px] flex justify-start flex-col items-center relative ">
            <h2 class="mb-8 text-3xl text-[#59d460] font-semibold">
                Login
            </h2>
            <div class="mt-1 border-2 rounded-md border-[#59d460] w-full flex items-center flex-col gap-y-16 p-[60px]">
                <!-- email -->
                <div class="flex flex-col">
                    <label class="mb-2" for="email">Email:</label>
                    <input class="border-2 px-2 py-1 border-[#59d460]" type="text" v-model="email">
                </div>
                <!-- password -->
                <div class="flex flex-col">
                    <label class="mb-2" for="password">Password:</label>
                    <input class="border-2 px-2 py-1 border-[#59d460]" type="text" v-model="pass">
                </div>
                <!-- submit button -->
                <div class="">
                    <button type="submit" class="bg-[#59d460] p-3 rounded-lg px-6 text-white"
                        @click="submit()">Login</button>
                </div>
            </div>
        </form>
    </div>
</template>


<script>

export default {
    data() {
        return {
            email: undefined,
            pass: undefined,
        }
    },
    methods: {
        submit() {
            if (this.email == 'admin' && this.pass == '123') {
                window.localStorage.setItem('isAuthenticated', 'true')
            }
            else {
                alert('Wrong Password')
            }
        }
    }
}
</script>
