<template>
  <div class="container">
    <h2 class="title">Auth in user</h2>
    <el-form ref="form" :model="form" :rules="rules">
      <el-form-item prop="email" label="email">
        <el-input v-model="form.email" placeholder="email" />
      </el-form-item>
      <el-form-item prop="password" label="password">
        <el-input
          v-model="form.password"
          placeholder="password"
          type="password"
          show-password
        />
      </el-form-item>
      <el-button @click="auth">Auth</el-button>
      <nuxt-link to="/">back</nuxt-link>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      form: {
        email: '',
        password: '',
      },
      rules: {
        email: [{ required: true }],
        password: [{ required: true }],
      },
    }
  },
  methods: {
    auth() {
      this.$refs.form.validate((valid) => {
        if (valid) {
          // eslint-disable-next-line
          const form = {
            email: this.form.email,
            password: this.form.password,
          }
        } else {
          // eslint-disable-next-line
          console.log('error submit!!')
          return false
        }
      })
    },
  },
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
  flex-direction: column;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
