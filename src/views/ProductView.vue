<template>
  <section>
    <h1>單一產品</h1>
    <div class="container">
      <div class="row">
        <div class="card mb-3">
          <img :src="product.imageUrl" class="card-img-top" alt="...">
          <div class="card-body">
            <h5 class="card-title">{{product.title}}</h5>
            <p class="card-text">{{product.description}}</p>
            <p class="card-text"><small class="text-muted">Last updated 3 mins ago</small></p>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  data () {
    return {
      product: []
    }
  },
  methods: {
    getProduct () {
      const { id } = this.$route.params
      this.$http(`${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/product/${id}`).then((res) => {
        this.product = res.data.product
      })
    }
  },
  mounted () {
    this.getProduct()
  }
}
</script>
