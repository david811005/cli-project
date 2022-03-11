<template>
      <div class="container">
        <div class="row justify-content-center">
          <h1 class="h3 mb-3 font-weight-normal">
            請先登入
          </h1>
          <div class="col-8">
            <form id="form" class="form-signin">
              <div class="form-floating mb-3">
                <input type="email" class="form-control" id="username"
                  placeholder="name@example.com" required autofocus
                  v-model="user.username">
                <label for="username">Email address</label>
              </div>
              <div class="form-floating">
                <input type="password" class="form-control" id="password"
                  placeholder="Password" required
                  v-model="user.password">
                <label for="password">Password</label>
              </div>
              <button class="btn btn-lg btn-primary w-100 mt-3" type="button" @click="login">
                登入
              </button>
            </form>
          </div>
        </div>
      </div>
</template>

<script>
export default {
  data () {
    return {
      user: {
        username: '',
        password: ''
      }
    }
  },
  methods: {
    login () {
      this.$http.post(`${process.env.VUE_APP_API}/admin/signin`, this.user).then((res) => {
        // console.log(res.data)
        const { token, expired } = res.data
        document.cookie = `davidToken=${token};expires=${new Date(expired)}`
        this.$router.push('/admin/products')
      })
        .catch((err) => {
          alert(err.response.data.message.message)
        })
    }
  }
}
</script>
