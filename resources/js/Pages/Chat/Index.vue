<template>
    <div class="flex">
        <div class="w-1/2 mr-4 border border-gray-400">
            lorem ipsum dolor
        </div>
        <div class="w-1/2 p-2 border border-gray-400">
            <h3>Users</h3>
            <div v-if="users" class="text-gray-700 mb-4 text-lg">
                <div v-for="user in users" class="flex items-center pb-4 mb-4 border-b border-gray-300">
                    <p class="mr-2">{{ user.id}}</p>
                    <p class="mr-4">{{ user.name}}</p>
                    <a class="inline-block bg-sky-400 text-white text-xs px-3 py-2 rounded-lg" href="#">Message</a>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import Main from "@/Layouts/Main.vue";

export default {
    name: "Index",

    props: [
      'users'
    ],

    layout: Main,
}
</script>

<style scoped>

</style>
