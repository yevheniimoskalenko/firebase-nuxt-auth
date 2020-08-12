<template>
  <div>
    <div v-if="user" class="email">
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
      <div v-loading="loading" class="todo-list">
        <div v-for="(t, index) in todo" :key="index" class="todo-item">
          <span>id: {{ t.uid }}</span>
          <p>{{ t.todo }}</p>
          <time>{{ t.date }}</time>
          <el-button
            icon="el-icon-delete"
            circle
            @click="todoDelete(index)"
          ></el-button>
          <el-button
            icon="el-icon-edit"
            circle
            @click="edit(index)"
          ></el-button>
        </div>
      </div>
      <el-dialog
        title="Tips"
        :visible.sync="dialogVisible"
        width="30%"
        :before-close="handleClose"
      >
        <el-form ref="form" :rules="rules" :model="form">
          <el-form-item prop="todo">
            <el-input v-model="form.edit" placeholder="new todo" />
          </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
          <el-button @click="dialogVisible = false">Cancel</el-button>
          <el-button type="primary" @click="editTodo">Confirm</el-button>
        </span>
      </el-dialog>
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
      form: { todo: '', edit: '' },
      loading: false,
      dialogVisible: false,
      rules: {},
      todo: {},
      item: null,
    }
  },
  mounted() {
    this.load()
  },
  methods: {
    edit(item) {
      this.item = item
      this.dialogVisible = true
    },
    async load() {
      const todos = this.$fireDb.ref('todos').orderByChild('date')
      try {
        this.loading = true
        const snapshot = (await todos.once('value')).val()
        this.todo = snapshot
      } catch (e) {
        this.$message({
          message: `${e.message}`,
          type: 'warning',
        })
      } finally {
        this.loading = false
      }
    },
    async editTodo() {
      const todo = this.$fireDb.ref(`todos/${this.item}`)
      try {
        await todo.update({ todo: this.form.edit })
        this.dialogVisible = false
      } catch (e) {
        this.$message({
          message: `${e.message}`,
          type: 'warning',
        })
      }
    },
    async logOut() {
      await this.$fireAuth
        .signOut()
        .then(() => this.$router.replace({ name: 'login' }))
    },
    async todoDelete(id) {
      const todoDelete = this.$fireDb.ref(`todos/${id}`)
      try {
        await todoDelete.remove()
        this.$message({
          showClose: true,
          message: 'this todo is deleted',
        })
      } catch (e) {
        this.$message({
          message: `${e.message}`,
          type: 'warning',
        })
      }
    },
    handleClose(done) {
      this.$confirm('Are you sure to close this dialog?')
        .then((_) => {
          done()
        })
        .catch((_) => {})
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
