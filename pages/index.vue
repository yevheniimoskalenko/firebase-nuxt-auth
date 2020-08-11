<template>
  <div class="container">
    <h2 class="title">Create new User</h2>
    <el-form ref="form" :model="form" :rules="rules">
      <el-form-item prop="email">
        <el-input v-model="form.email" placeholder="email" />
      </el-form-item>
      <el-form-item prop="password">
        <el-input
          v-model="form.password"
          placeholder="password"
          type="password"
          show-password
        />
      </el-form-item>
      <el-form-item prop="confirm">
        <el-input
          v-model="form.confirm"
          placeholder="confirm"
          type="password"
          show-password
        />
      </el-form-item>
      <el-button @click="create">Create</el-button>
      <nuxt-link to="login">login</nuxt-link>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    const validatePass = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('Please input the password'))
      } else {
        if (this.form.confirm !== '') {
          this.$refs.form.validateField('checkPass')
        }
        callback()
      }
    }
    const validatePass2 = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('Please input the password again'))
      } else if (value !== this.form.password) {
        callback(new Error("Two inputs don't match!"))
      } else {
        callback()
      }
    }
    return {
      form: {
        email: '',
        password: '',
        confirm: '',
      },
      rules: {
        email: [{ required: true }],
        password: [{ validator: validatePass, trigger: 'blur' }],
        confirm: [{ validator: validatePass2, trigger: 'blur' }],
      },
    }
  },
  methods: {
    create() {
      this.$refs.form.validate(async (valid) => {
        if (valid) {
          const form = {
            email: this.form.email,
            password: this.form.password,
            confirm: this.form.confirm,
          }
          try {
            await this.$fireAuth
              .createUserWithEmailAndPassword(form.email, form.password)
              .then((result) => {
                this.$message({
                  message: `User was created`,
                  type: 'success',
                })
              })
              .catch((e) => {
                this.$message({
                  message: `${e.message}`,
                  type: 'warning',
                })
              })
          } catch (e) {
            // eslint-disable-next-line
            console.log(e)
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
