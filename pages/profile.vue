<template>
  <div>
    <div v-if="user" class="email">
      p
      <span>
        <i class="el-icon-user"></i>
        {{ user.email }}
      </span>
      <el-button @click="logOut">log out</el-button>
    </div>
    <div class="todo">
      <div class="create-todo">
        <el-form ref="form" :rules="rules" :model="form">
          <el-form-item prop="todo">
            <el-input v-model="form.todo" placeholder="new todo" />
            <el-button icon="el-icon-plus" circle @click="addTodo" />
          </el-form-item>
        </el-form>
      </div>
      <div class="todo-list">
        <div class="todo-item"></div>
      </div>
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
  data() {
    return {
      form: { todo: '' },
      rules: {},
    }
  },
  methods: {
    async logOut() {
      await this.$fireAuth
        .signOut()
        .then(() => this.$router.replace({ name: 'login' }))
    },
    async addTodo() {
      const todos = this.$fireDb.ref('todos')
      try {
        await todos.push({
          todo: this.form.todo,
          date: Date.now(),
          uid: this.user.user_id,
        })
        this.$message({ message: 'todo create', type: 'success' })
      } catch (e) {
        this.$message({
          message: `${e.message}`,
          type: 'warning',
        })
      }
    },
  },
}
</script>

<style lang="scss" scoped></style>
