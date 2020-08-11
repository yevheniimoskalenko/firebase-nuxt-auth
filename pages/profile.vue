<template>
  <div>
    <div v-if="user" class="email">
      {{ user.email }}
      <el-button @click="logOut">log out</el-button>
    </div>
  </div>
</template>

<script>
import { getUserFromCookie } from '@/helpers/index'
export default {
  async asyncData({ req, redirect, $fireAuth }) {
    if (process.server) {
      const user = await getUserFromCookie(req)
      if (!user) {
        redirect('/login')
      }
      return { user }
    } else {
      const user = await $fireAuth.currentUser
      if (!user) {
        redirect('/login')
      }
    }
  },
  methods: {
    async logOut() {
      await this.$fireAuth
        .signOut()
        .then(() => this.$router.replace({ name: 'login' }))
    },
  },
  // mounted() {
  //   this.$fireAuth.currentUser
  //     .getIdToken(true)
  //     .then((token) => console.log(token))

  //   // this.$fireAuth.onAuthStateChanged((user) => console.log(user))
  // },
}
</script>

<style lang="scss" scoped></style>
