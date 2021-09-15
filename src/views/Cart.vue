<template>
  <div class="cart">
    <HeaderShayna />
    <!-- Breadcrumb Section Begin -->
    <div class="breacrumb-section">
      <div class="container">
        <div class="row">
          <div class="col-lg-12">
            <div class="breadcrumb-text text-left">
              <a href="./home.html"><i class="fa fa-home"></i> Home</a>
              <span>Shopping Cart</span>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- Breadcrumb Section Begin -->

    <!-- Shopping Cart Section Begin -->
    <section class="shopping-cart spad">
      <div class="container">
        <div class="row">
          <div class="col-lg-8">
            <div class="row">
              <div class="col-lg-12">
                <div class="cart-table">
                  <table>
                    <thead>
                      <tr>
                        <th>Image</th>
                        <th class="p-name text-center">Product Name</th>
                        <th>Price</th>
                        <th>Action</th>
                      </tr>
                    </thead>
                    <tbody v-if="keranjangUser.length > 0">
                      <tr v-for="item in keranjangUser" v-bind:key="item.id">
                        <td class="cart-pic first-row">
                          <img class="img-cart" v-bind:src="item.photo" />
                        </td>
                        <td class="cart-title first-row text-center">
                          <h5>{{ item.name }}</h5>
                        </td>
                        <td class="p-price first-row">${{ item.price }}</td>
                        <td
                          class="delete-item"
                          @click="removeItem(keranjangUser.index)"
                        >
                          <a href="#"><i class="material-icons">close</i></a>
                        </td>
                      </tr>
                    </tbody>
                    <tbody v-else>
                      <tr class="text-center">
                        <td colspan="4">Keranjang Kosong</td>
                      </tr>
                    </tbody>
                  </table>
                </div>
              </div>
              <div class="col-lg-8 text-left">
                <h4 class="mb-4">Informasi Pembeli:</h4>
                <div class="user-checkout">
                  <form>
                    <div class="form-group">
                      <label for="name">Nama lengkap</label>
                      <input
                        v-model="customerInfo.name"
                        type="text"
                        class="form-control"
                        id="name"
                        aria-describedby="namaHelp"
                        placeholder="Masukan Nama"
                      />
                    </div>
                    <div class="form-group">
                      <label for="email">Email Address</label>
                      <input
                        v-model="customerInfo.email"
                        type="email"
                        class="form-control"
                        id="email"
                        aria-describedby="emailHelp"
                        placeholder="Masukan Email"
                      />
                    </div>
                    <div class="form-group">
                      <label for="number">No. HP</label>
                      <input
                        v-model="customerInfo.number"
                        type="text"
                        class="form-control"
                        id="number"
                        aria-describedby="noHPHelp"
                        placeholder="Masukan No. HP"
                      />
                    </div>
                    <div class="form-group">
                      <label for="address">Alamat Lengkap</label>
                      <textarea
                        v-model="customerInfo.address"
                        class="form-control"
                        id="address"
                        rows="3"
                      ></textarea>
                    </div>
                  </form>
                </div>
              </div>
            </div>
          </div>
          <div class="col-lg-4">
            <div class="row">
              <div class="col-lg-12">
                <div class="proceed-checkout">
                  <ul class="text-left">
                    <li class="subtotal">
                      ID Transaction <span>#SH12000</span>
                    </li>
                    <li class="subtotal mt-3">
                      Subtotal <span>${{ subTotal }}.00</span>
                    </li>
                    <li class="subtotal mt-3">
                      Pajak <span>10% = ${{ totalPajak }}.00</span>
                    </li>
                    <li class="subtotal mt-3">
                      Total Biaya <span>${{ totalBiaya }}.00</span>
                    </li>
                    <li class="subtotal mt-3">
                      Bank Transfer <span>Mandiri</span>
                    </li>
                    <li class="subtotal mt-3">
                      No. Rekening <span>2208 1996 1403</span>
                    </li>
                    <li class="subtotal mt-3">
                      Nama Penerima <span>Shayna</span>
                    </li>
                  </ul>
                  <a @click="checkout()" href="#" class="proceed-btn"
                    >I ALREADY PAID</a
                  >
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
    <!-- Shopping Cart Section End -->
    <FooterShayna />
  </div>
</template>

<script>
import HeaderShayna from "@/components/HeaderShayna.vue";
import FooterShayna from "@/components/FooterShayna.vue";

import axios from "axios";

export default {
  name: "Cart",
  components: {
    HeaderShayna,
    FooterShayna,
  },
  data() {
    return {
      keranjangUser: [],
      customerInfo: {
        name: "",
        email: "",
        number: "",
        address: "",
      },
    };
  },
  mounted() {
    if (localStorage.getItem("keranjangUser")) {
      try {
        this.keranjangUser = JSON.parse(localStorage.getItem("keranjangUser"));
      } catch (e) {
        localStorage.removeItem("keranjangUser");
      }
    }
  },
  methods: {
    removeItem(index) {
      this.keranjangUser.splice(index, 1);
      const parsed = JSON.stringify(this.keranjangUser);
      localStorage.setItem("keranjangUser", parsed);
    },
    checkout() {
      var productIds = this.keranjangUser.map(function (product) {
        return product.id;
      });

      console.log(productIds);

      var checkoutData = {
        name: this.customerInfo.name,
        email: this.customerInfo.email,
        number: this.customerInfo.number,
        address: this.customerInfo.address,
        transaction_total: parseInt(this.totalBiaya),
        transaction_status: "PENDING",
        transaction_details: productIds,
      };

      axios
        .post("http://ihsanfirdaus.my.id/api/checkout", checkoutData)
        .then(() => this.$router.push("success"))
        .catch((err) => console.log(err));
    },
  },
  computed: {
    subTotal() {
      return this.keranjangUser.reduce(function (items, data) {
        return items + data.price;
      }, 0);
    },
    totalPajak() {
      return (this.subTotal * 10) / 100;
    },
    totalBiaya() {
      return this.subTotal + this.totalPajak;
    },
  },
};
</script>

<style scoped>
.img-cart {
  width: 100px;
  height: 100px;
}
</style>