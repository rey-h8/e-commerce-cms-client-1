<template>
  <div id="auth-content">
    <div id="auth-title">Login</div>
    <div
      v-if="errorBanner"
      class="notification notification-banner notification-error"
    >
      {{ errorBanner }}
    </div>
    <div id="auth-oauth">
      <div id="gSignIn" class="g-signin2" data-onsuccess="onSignIn"></div>
    </div>
    <div id="auth-form">
      <form id="loginForm" @submit.prevent="submitLoginForm">
        <label for="user">Username/Email</label>
        <input
          type="text"
          name="user"
          id="user"
          v-model="user"
          placeholder="Insert your username or email"
          autofocus
        />
        <div
          v-if="errors.user.length > 0"
          class="notification notification-error"
        >
          <ul>
            <li v-for="(error, i) in errors.user" :key="i">
              {{ error }}
            </li>
          </ul>
        </div>

        <label for="password">Password</label>
        <input
          type="password"
          name="password"
          id="password"
          v-model="password"
          placeholder="Insert your password"
        />
        <div
          v-if="errors.password.length > 0"
          class="notification notification-error"
        >
          <ul>
            <li v-for="(error, i) in errors.password" :key="i">
              {{ error }}
            </li>
          </ul>
        </div>

        <div class="actions">
          <button class="button button-secondary">
            Log In
          </button>
        </div>
        <p>
          I want to
          <a
            id="link-register"
            @click="toggleLoginForm"
            href="javascript:void(0)"
          >
            create an account
          </a>
        </p>
      </form>
    </div>
  </div>
</template>

<script>
import axios from '../../config/axios'

export default {
  data () {
    return {
      name: 'FormLogin',
      user: '',
      password: '',
      errors: {
        user: []
      },
      errorBanner: null
    }
  },
  mounted () {
    this.renderGoogleButton()
  },
  methods: {
    submitLoginForm () {
      const { user, password, errors } = this
      console.log({ user, password, errors })

      if (this.validateLogin()) {
        for (const key in this.errors) {
          this.errors[key] = []
        }
        this.errorBanner = null

        console.log('submit login form')

        axios({
          method: 'POST',
          url: '/login',
          data: {
            user,
            password
          }
        })
          .then(({ data }) => {
            console.log('berhasil login', data)
            if (data.access_token) {
              localStorage.setItem('access_token', data.access_token)
            }
            this.user = ''
            this.password = ''
            this.$emit('showMessage', {
              type: 'success',
              msg: 'Login successful'
            })
            console.log('from login page -> home-page')
            this.$emit('changePage', 'home-page')
          })
          .catch((err) => {
            console.log(err.response)
            this.$toasted.global.errorMessage(err.response.data.msg)

            if (err.response?.status === 401) {
              this.errorBanner = err.response.data.msg
            }

            this.$emit('showMessage', {
              msg: err,
              type: 'error'
            })
          })
      }
    },
    validateLogi () {
      for (const key in this.errors) {
        this.errors[key] = []
      }
      console.log('validating login form')
      if (!this.user) {
        this.errors.user.push('Username or email address is required')
      }
      if (!this.password) {
        this.errors.password.push('Password is required')
      }
      console.log('errors:', JSON.stringify(this.errors, null, 2))

      for (const key in this.errors) {
        if (this.errors[key].length > 0) {
          console.log('validation pass fail')
          return false
        }
      }

      console.log('validation pass success')

      return true
    },
    toggleLoginForm () {
      this.$emit('toggleLoginForm')
    }
  }
}
</script>

<style></style>
