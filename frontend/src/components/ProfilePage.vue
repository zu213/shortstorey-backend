<template>
  <div class="storyContainer">
    <div v-if="user?.name">
     Change name : {{ user.name }}
     <form @submit.prevent="submitUserForm">
      <label for="fname">Nmae:</label><br>
      <input type="text" v-model="name" name="fname" />
      <input type="submit" value="Update" />
    </form> 
    </div>
    <div>
      change password
    </div>
    <div>
      Your stories:
      <ul>
        <li v-for="(story, i) in stories" :key="i">
          <StoryCard :story="story" />
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import StoryCard from './StoryCard.vue'
</script>

<script>
import { getUser, updateUser } from '../bridge/bridge.js'
import { useAuthStore } from '@/store/auth'
import { getStories } from '../bridge/bridge.js'

export default {
  name: 'ProfilePage',
  data() {
    return {
      name: '',
      user: null,
      auth: null,
      stories: []
    }
  },
  computed: {
    currentUserId() {
      return localStorage.getItem('userid')
    },
    isAuthenticated() {
      return this.auth.isAuthenticated
    }
  },
  async created() {
    console.log(localStorage.getItem('userid'))
    this.auth = useAuthStore()
    this.user = await getUser(this.currentUserId)
    this.stories = await getStories(`user_id=${this.user?.id}`)
  },
  methods: {
    async submitUserForm() {
      const userToUpdate = {
        id: this.user.id,
        name: this.name
      }
      try {
        const result = await updateUser(userToUpdate, this.auth.token)
        console.log('Update successful:', result)
      } catch (err) {
        console.error('Update failed:', err)
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
