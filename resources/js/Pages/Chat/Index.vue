<template>
    <div class="flex items-start">
        <div class="w-1/2 p-4 mr-4 bg-white border border-gray-200">
            <h3 class="text-gray-700 mb-4 text-lg">Chats</h3>
            <div v-if="chats" class="text-gray-700 mb-4 text-lg">
                <div v-for="chat in chats" class="pb-4 mb-4 border-b border-gray-300">
                    <Link :href="route('chats.show', chat.id)">
                        <div>
                            <div>
                                <div class="flex">
                                    <p class="mr-2">{{ chat.id }}</p>
                                    <p class="mr-2">{{ chat.title ?? 'Your chat' }}</p>
                                </div>
                                <div :class="['p-2 flex justify-between items-center',
                                    chat.unreadable_count !== 0 ? 'bg-sky-50' : ''
                                ]">
                                    <div class="text-sm">
                                        <p class="text-gray-600 ">{{ chat.last_message.user_name }}</p>
                                        <p class="mb-2 text-gray-500">{{ chat.last_message.body }}</p>
                                        <p class="italic text-gray-400">{{ chat.last_message.time }}</p>
                                    </div>
                                    <div v-if="chat.unreadable_count !== 0">
                                        <p class="text-xs rounded-full bg-sky-500 text-white px-2 py-1">
                                            {{ chat.unreadable_count }}</p>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </Link>
                </div>
            </div>
        </div>
        <div class="w-1/2 p-4 bg-white border border-gray-200">
            <div class="flex items-center mb-4 justify-between">
                <h3 class="text-gray-700 mb-4 text-lg">Users</h3>
                <a v-if="!isGroup" @click.prevent="isGroup = true" href="#"
                   class="inline-block bg-indigo-600 text-white text-xs px-3 py-2 rounded-lg">Make group</a>
                <div v-if="isGroup" class="flex items-center">
                    <input class="mr-4 h-8 border border-gray-300 rounded-full" type="text" placeholder="group title"
                           v-model="title">
                    <a @click.prevent="storeGroup" href="#"
                       :class="['mr-2 inline-block bg-green-600 text-white text-xs px-3 py-2 rounded-lg',
                       this.userIds.length > 1 ? 'bg-green-600' : 'bg-green-300'
                       ]">Go chat
                    </a>
                    <a @click.prevent="refreshUserIds" href="#"
                       class="inline-block bg-red-600 text-white text-xs px-3 py-2 rounded-lg">X</a>

                </div>
            </div>
            <h3 class="text-gray-700 mb-4 text-lg">Users</h3>
            <div v-if="users" class="text-gray-700 mb-4 text-lg">
                <div v-for="user in users" class="flex justify-between items-center pb-4 mb-4 border-b border-gray-300">
                    <div class="flex items-center">
                        <p class="mr-2">{{ user.id }}</p>
                        <p class="mr-4">{{ user.name }}</p>
                        <a @click.prevent="store(user.id)"
                           class="inline-block bg-sky-400 text-white text-xs px-3 py-2 rounded-lg" href="#">Message</a>
                    </div>
                    <div v-if="isGroup">
                        <input @click="toggleUsers(user.id)" type="checkbox">
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import {Link} from "@inertiajs/vue3";
import Main from "@/Layouts/Main.vue";

export default {
    name: "Index",

    props: [
        'users',
        'chats'
    ],

    data() {
        return {
            isGroup: false,
            userIds: [],
            title: null,
        }
    },

    created() {
        window.Echo.channel(`users.${this.$page.props.auth.user.id}`)
            .listen('.store-message-status', res => {
                this.chats.filter(chat => {
                    if (chat.id === res.chat_id) {
                        chat.unreadable_count = res.count
                        chat.last_message = res.message
                    }
                })
            })
    },

    components: {
        Link,
    },

    layout: Main,

    methods: {
        store(id) {
            this.$inertia.post('/chats', {title: null, users: [id]})
        },

        storeGroup(id) {
            if (this.isGroup.length < 2) return;
            this.$inertia.post('/chats', {title: this.title, users: this.userIds})
        },

        toggleUsers(id) {
            let index = this.userIds.indexOf(id)
            if (index === -1) {
                this.userIds.push(id)
            } else {
                this.userIds.splice(index, 1)
            }
        },

        refreshUserIds() {
            this.userIds = []
            this.isGroup = false
        }
    }
}
</script>

<style scoped>

</style>
