<template>
    <main class="w-full h-fit py-10 flex items-center flex-col">
        <!-- nav -->
        <nav class="mb-16 h-fit w-[375px] border-2 rounded-md p-4 border-[#59d460]">
            <!-- wrapper -->
            <div class="flex justify-around">
                <p :class="editStyle" @click="clickedEdit">Edit</p>
                <p :class="addStyle" @click="clickedAdd">Add</p>
            </div>
        </nav>


        <!-- Edit -->
        <div v-if="currentTab == 'edit'">
            <!-- foreach section -->
            <div v-for="(section, indexS) in data" :key="section._id" class="flex flex-col items-center">
                <!-- section title -->
                <h2 class=" mb-8 text-3xl text-[#59d460] font-semibold w-fit">{{ section.name }}</h2>
                <!-- foreach category -->
                <div v-for="(category, indexC) in section.categories" :key="category._id"
                    class="h-fit w-[375px] border-2 rounded-md p-4 border-[#59d460] mb-[5rem] relative">
                    <!-- category title -->
                    <p class="absolute top-[-30px] left-[-0px] italic">{{ category.name }}</p>
                    <!-- foreach item -->
                    <div v-for="(item, index) in category.items" :key="item._id"
                        class="flex  justify-around items-center mb-[1rem]">
                        <div class="flex justify-between w-[65%] inputs">
                            <input class="w-[70%]" v-model="data[indexS].categories[indexC].items[index].name" @focus="itemEdited(data[indexS].categories[indexC].items[index])" >
                            <div class="">
                                <input class="max-w-[3rem]"  v-model="data[indexS].categories[indexC].items[index].price" @focus="itemEdited(data[indexS].categories[indexC].items[index])">LL
                            </div>
                        </div>
                        <div id="" :data-itemId="item._id"
                            class="px-[1rem] py-[.5rem] rounded-lg bg-red-500 hover:cursor-pointer"
                            @click="deleteItem(section._id, category._id, item._id)">Delete</div>
                    </div>
                </div>
            </div>
        </div>
        <div class="bg-[#59d460] px-[2rem] py-[1rem] rounded-lg cursor-pointer" @click="submitChanges()">Apply Changes</div>
    </main>
</template>


<script>
export default {
    methods: {
        async startPage() {
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
            setTimeout(() => {
                window.scroll(0, window.localStorage.getItem('pos'))
                window.localStorage.setItem('pos', 0)
            }, 100)
        },
        clickedAdd() {
            this.currentTab = 'add'
            this.editStyle = 'cursor-pointer'
            this.addStyle = 'cursor-pointer text-[#59d460]'
        },
        clickedEdit() {
            this.currentTab = 'edit'
            this.addStyle = 'cursor-pointer'
            this.editStyle = 'cursor-pointer text-[#59d460]'
        },
        deleteItem(sectionId, categoryId, itemId) {
            fetch('https://zealous-cyan-katydid.cyclic.app/delete-item', { method: 'DELETE', body: JSON.stringify({ item_id: itemId }), headers: { 'Content-Type': 'application/json' }})
            window.localStorage.setItem('pos', window.scrollY)
            location.reload()
        },
        itemEdited(item){
            let inList = false
            for (let i = 0; i < this.editedItems.length; i++) {
                if (item._id == this.editedItems[i]._id) {
                    this.editedItems[i].name = item.name
                    this.editedItems[i].price = item.price
                    inList = true
                }
            }
            if (inList === false) {
                this.editedItems = [...this.editedItems, item]
            }
            console.log(this.editedItems);
        },
        submitChanges() {
            fetch('https://zealous-cyan-katydid.cyclic.app/edit-items', { method: 'PUT', body: JSON.stringify({ editedItems: this.editedItems }), headers: { 'Content-Type': 'application/json' }})
            location.reload()
        }
    },
    data() {
        return {
            currentTab: 'edit',
            addStyle: 'cursor-pointer',
            editStyle: 'cursor-pointer text-[#59d460]',
            data: undefined,
            items: undefined,
            categories: undefined,
            sections: undefined,
            editedItems: []
        }
    },
    created() {
        this.startPage()
    }
}
</script>

<style>
input:focus {
    outline: none;
}

.inputs {
    font-size: .9rem;
}

.overlay {
    background-color: rgba(0, 0, 0, 0.10);
}
</style>