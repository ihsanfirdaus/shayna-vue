<template>
    <!-- Women Banner Section Begin -->
    <section class="women-banner spad">
        <div class="container-fluid">
            <div class="row">
                <div class="col-lg-12 mt-5" v-if="products.length > 0">
                    <carousel class="product-slider" :dots="false" :items="3" :autoplay="true" :nav="false">
                        <div class="product-item" v-for="item in products" v-bind:key="item.id">
                            <div class="pi-pic">
                                <img v-bind:src="item.product_gallery[0].photo" alt="" />
                                <ul>
                                    <li class="w-icon active" @click="saveKeranjang(item.id, item.name, item.price, item.product_gallery[0].photo)">
                                        <a href="#">
                                            <i class="icon_bag_alt"></i>
                                        </a>
                                    </li>
                                    <li class="quick-view">
                                        <router-link v-bind:to="'/product/'+item.id">+ Quick View</router-link>
                                    </li>
                                </ul>
                            </div>
                            <div class="pi-text">
                                <div class="catagory-name">{{ item.type }}</div>
                                <router-link to="/product">
                                    <h5>{{ item.name }}</h5>
                                </router-link>
                                <div class="product-price">
                                    ${{ item.price }}
                                    <span>$35.00</span>
                                </div>
                            </div>
                        </div>
                    </carousel>
                </div>
                <div class="col-lg-12" v-else>
                    Produk terbaru belum tersedia untuk saat ini.
                </div>
            </div>
        </div>
    </section>
    <!-- Women Banner Section End -->
</template>

<script>

import carousel from 'vue-owl-carousel';
import axios from 'axios';

export default {
    name: 'WomenShayna',
    components: {
        carousel
    },
    data() {
        return {
            products: [],
            keranjangUser: []
        }
    },
    mounted() {
        axios.get('http://ihsanfirdaus.my.id/api/products')
        .then(res => (this.products = res.data.data.data))
        .catch(err => console.log(err))
    },
    methods: {
        saveKeranjang(idProduct, nameProduct, priceProduct, photoProduct) 
        {
            var productStored = {
                "id": idProduct,
                "name": nameProduct,
                "price": priceProduct,
                "photo": photoProduct
            }
            this.keranjangUser.push(productStored);
            const parsed = JSON.stringify(this.keranjangUser);
            localStorage.setItem('keranjangUser', parsed);
            
            window.location.reload();
        }
    },
}
</script>

<style scoped>
.product-item {
    margin-right: 25px;
}
.pi-pic img {
    height: 28em;
}
</style>