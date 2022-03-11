<template>
  <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
  <div class="container-fluid">
    <router-link class="navbar-brand" to="/admin">後台</router-link>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarNav">
      <ul class="navbar-nav">
        <li class="nav-item">
          <router-link class="nav-link" to="/">回到前台</router-link>
        </li>
        <li class="nav-item">
          <router-link class="nav-link" to="/admin/products">產品列表</router-link>
        </li>
        <li class="nav-item">
          <router-link class="nav-link" to="/admin/coupon">優惠券</router-link>
        </li>
        <li class="nav-item">
          <a href="#" class="nav-link" @click.prevent="signout">登出</a>
        </li>
      </ul>
    </div>
  </div>
</nav>
<router-view v-if="checkSuccess"></router-view>
</template>

<script>
export default {
  data () {
    return {
      checkSuccess: false
    }
  },
  methods: {
    checkLogin () {
      // eslint-disable-next-line
      const mytoken = document.cookie.replace(/(?:(?:^|.*;\s*)davidToken\s*\=\s*([^;]*).*$)|^.*$/, '$1')
      if (mytoken) {
        this.$http.defaults.headers.common.Authorization = `${mytoken}`
        this.$http.post(`${process.env.VUE_APP_API}/api/user/check`).then((res) => {
          this.checkSuccess = true
        })
          .catch(() => {
            alert('尚未登入')
            this.$router.push('/login')
          })
      } else {
        alert('尚未登入')
        this.$router.push('/login')
      }
    },
    signout () {
      document.cookie = 'davidToken=;expires=;'
      this.$router.push('/login')
    }
  },
  mounted () {
    this.checkLogin()
  }
}
</script>
