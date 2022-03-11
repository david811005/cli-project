<template>
  <section>
    <div class="container">
    <h1>購物車</h1>
            <table class="table align-middle">
              <thead>
                <tr>
                  <th>圖片</th>
                  <th>商品名稱</th>
                  <th>價格</th>
                  <th></th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="item in products" :key="item.id">
                  <td style="width: 200px">
                    <div
                     :style="{backgroundImage: `url(${item.imageUrl})`}"
                     style="height: 100px; background-size: cover; background-position: center"></div>
                  </td>
                  <td>
                    {{ item.title }}
                  </td>
                  <td>
                    <div class="h5" v-if="item.origin_price == item.price">{{ item.price }} 元</div>
                    <div v-else>
                        <del class="h6">原價 {{ item.origin_price }} 元</del>
                        <div class="h5">現在只要 {{ item.price }} 元</div>
                    </div>
                  </td>
                  <td>
                    <div class="btn-group btn-group-sm">
                      <button type="button" class="btn btn-outline-secondary"
                      @click="openProductModal(item.id)"
                      :disabled="isLoading==item.id">
                        <i v-show="isLoading==item.id" class="spinner-border spinner-border-sm"></i>
                        查看更多
                      </button>
                      <button type="button" class="btn btn-outline-danger"
                      @click="addToCart(item.id)"
                      :disabled="isLoading==item.id">
                        <i v-show="isLoading==item.id" class="spinner-border spinner-border-sm"></i>
                        加到購物車
                      </button>
                    </div>
                  </td>
                </tr>
              </tbody>
            </table>
            <!-- 購物車列表 -->
            <div class="text-end">
              <button class="btn btn-outline-danger" type="button"
              @click="delAll" :disabled="cartData.carts.length==0">清空購物車</button>
            </div>
            <table class="table align-middle">
              <thead>
                <tr>
                  <th></th>
                  <th>品名</th>
                  <th style="width: 150px">數量/單位</th>
                  <th>單價</th>
                </tr>
              </thead>
              <tbody>
                  <!-- cart.carts -->
                <template v-if="cartData.carts">
                  <tr v-for="item in cartData.carts" :key="item.id">
                    <td>
                      <button type="button" class="btn btn-outline-danger btn-sm" @click="delCart(item.id)"
                      :disabled="isLoading==item.id">
                        <i v-show="isLoading==item.id" class="fas fa-spinner fa-pulse"></i>
                        x
                      </button>
                    </td>
                    <td>
                      {{ item.product.title}}
                      <!-- <div class="text-success">
                        已套用優惠券
                      </div> -->
                    </td>
                    <td>
                      <div class="input-group input-group-sm">
                        <div class="input-group mb-3">
                          <!-- <input
                               min="1" type="number" class="form-control"
                               v-model="item.qty"> -->
                          <select id="" class="form-select"
                          v-model="item.qty"
                          @change="updateCart(item)">
                              <option :value="num" v-for="num in 20" :key="`${num}+${item.id}`">{{num}}</option>
                          </select>
                          <span class="input-group-text" id="basic-addon2">{{ item.product.unit }}</span>
                        </div>
                      </div>
                    </td>
                    <td class="text-end">
                      <small class="text-success">折扣價：</small>
                      {{ item.final_total }}
                    </td>
                  </tr>
                </template>
              </tbody>
              <tfoot>
                <tr>
                  <td colspan="3" class="text-end">總計</td>
                  <td class="text-end">{{ cartData.total }}</td>
                </tr>
                <!-- <tr>
                  <td colspan="3" class="text-end text-success">折扣價</td>
                  <td class="text-end text-success">{{  }}</td>
                </tr> -->
              </tfoot>
            </table>
          </div>
           <div class="my-5 row  justify-content-center">
          <Form ref="form" class="col-md-6" v-slot="{ errors }"
          @submit="postForm" >
            <div class="mb-3">
              <label for="email" class="form-label">Email</label>
              <Field id="email" name="email" type="email" class="form-control"
                       :class="{ 'is-invalid': errors['email'] }" placeholder="請輸入 Email" rules="required|email"
                       v-model="form.user.email"
                      ></Field>
              <ErrorMessage name="email" class="invalid-feedback"></ErrorMessage>
            </div>

            <div class="mb-3">
              <label for="name" class="form-label">收件人姓名</label>
              <Field id="name" name="姓名" type="text" class="form-control" :class="{ 'is-invalid': errors['姓名'] }"
                       placeholder="請輸入姓名" rules="required"
                       v-model="form.user.name"></Field>
              <ErrorMessage name="姓名" class="invalid-feedback"></ErrorMessage>
            </div>

            <div class="mb-3">
              <label for="tel" class="form-label">收件人電話</label>
              <Field id="tel" name="電話" type="tel" class="form-control" :class="{ 'is-invalid': errors['電話'] }"
                       placeholder="請輸入電話" rules="required|min:8"
                       v-model="form.user.tel"></Field>
              <ErrorMessage name="電話" class="invalid-feedback"></ErrorMessage>
            </div>

            <div class="mb-3">
              <label for="address" class="form-label">收件人地址</label>
              <Field id="address" name="地址" type="text" class="form-control" :class="{ 'is-invalid': errors['地址'] }"
                       placeholder="請輸入地址" rules="required"
                       v-model="form.user.address"></Field>
              <ErrorMessage name="地址" class="invalid-feedback"></ErrorMessage>
            </div>

            <div class="mb-3">
              <label for="message" class="form-label">留言</label>
              <textarea id="message" class="form-control" cols="30" rows="10" v-model="form.message"></textarea>
            </div>
            <div class="text-end">
              <button type="submit" class="btn btn-danger"
              :disabled="Object.keys(errors).length > 0 || cartData.carts.length===0"
                      >送出訂單</button>
            </div>
          </Form>
        </div>
  </section>
</template>

<script>
import emitter from '@/libs/emitter'
export default {
  data () {
    return {
      cartData: {
        carts: []
      },
      products: [],
      productId: '',
      isLoading: '',
      form: {
        user: {
          name: '',
          email: '',
          tel: '',
          address: ''
        },
        message: ''
      }
    }
  },
  methods: {
    getPoducts () {
      this.$http.get(`${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/products/all`)
        .then((res) => {
          this.products = res.data.products
        })
    },
    addToCart (id, qty = 1) {
      const data = {
        product_id: id,
        qty: qty
      }
      this.isLoading = id
      this.$http.post(`${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/cart`, { data })
        .then((res) => {
          this.getCart()
          console.log(res.data)
          emitter.emit('get-cart')
          this.isLoading = ''
        })
    },
    getCart () {
      this.$http.get(`${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/cart`)
        .then((res) => {
          this.cartData = res.data.data
        })
    },
    delCart (id) {
      this.isLoading = id
      this.$http.delete(`${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/cart/${id}`)
        .then(() => {
          this.getCart()
          emitter.emit('get-cart')
          this.isLoading = ''
        })
    },
    updateCart (item) {
      const data = {
        product_id: item.product_id,
        qty: item.qty
      }
      this.isLoading = item.id
      this.$http.put(`${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/cart/${item.id}`, { data })
        .then(() => {
          this.getCart()
          emitter.emit('get-cart')
          this.isLoading = ''
        })
    },
    delAll () {
      this.$http.delete(`${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/carts`)
        .then(() => {
          emitter.emit('get-cart')
          this.getCart()
        })
    },
    postForm () {
      const cartlist = this.form
      console.log(cartlist)
      const url = `${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/order`
      this.$http.post(url, { data: cartlist })
        .then(() => {
          this.$refs.form.resetForm()
          this.getCart()
        }).catch((err) => {
          alert(err.data.message)
        })
    }
  },
  mounted () {
    this.getPoducts()
    this.getCart()
  }
}
</script>
