<template>
  <div class="product">
    <HeaderShayna />
    <!-- Breadcrumb Section Begin -->
    <div class="breacrumb-section">
        <div class="container">
            <div class="row">
                <div class="col-lg-12">
                    <div class="breadcrumb-text text-left">
                        <router-link to="/"><i class="fa fa-home"></i> Home</router-link>
                        <span>Detail</span>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <!-- Breadcrumb Section Begin -->

    <!-- Product Shop Section Begin -->
    <section class="product-shop spad page-details">
        <div class="container">
            <div class="row">
                <div class="col-lg-12">
                    <div class="row">
                        <div class="col-lg-6">
                            <div class="product-pic-zoom">
                                <img class="product-big-img" :src="gambar_default" alt="" />
                            </div>
                            <div class="product-thumbs" v-if="productGallery.length > 0">
                                <carousel class="product-thumbs-track ps-slider" :dots="false" :nav="false">
                                    <div 
                                    class="pt" 
                                    v-for="item in productGallery" 
                                    v-bind:key="item.id"
                                    @click="changeImage(item.photo)" 
                                    :class="item.photo == gambar_default ? 'active':''">
                                        <img v-bind:src="item.photo" alt="" />
                                    </div>
                                </carousel>
                            </div>
                        </div>
                        <div class="col-lg-6">
                            <div class="product-details text-left">
                                <div class="pd-title">
                                    <span>{{ productDetails.type }}</span>
                                    <h3>{{ productDetails.name }}</h3>
                                </div>
                                <div class="pd-desc">
                                    <p v-html="productDetails.description"></p>
                                    <h4>${{ productDetails.price }}</h4>
                                </div>
                                <div class="quantity">
                                    <router-link to="/cart">
                                        <a @click="saveKeranjang(productDetails.id, productDetails.name, productDetails.price, productGallery[0].photo)" 
                                            href="#" 
                                            class="primary-btn pd-cart">
                                            Add To Cart
                                        </a>
                                    </router-link>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>
    <!-- Product Shop Section End -->
    <RelatedShayna />
    <FooterShayna />
  </div>
</template>

<script>
// @ is an alias to /src
import HeaderShayna from '@/components/HeaderShayna.vue';
import RelatedShayna from '@/components/RelatedShayna.vue';
import FooterShayna from '@/components/FooterShayna.vue';

import carousel from 'vue-owl-carousel';
import axios from 'axios';

export default {
    name: 'Home',
    components: {
        HeaderShayna,
        RelatedShayna,
        FooterShayna,
        carousel
    },
    data(){
        return {
            gambar_default: null,
            productDetails: [],
            productGallery: [],
            keranjangUser: []
        }
    },
    mounted() {
        if(localStorage.getItem('keranjangUser')) {
            try {
                this.keranjangUser = JSON.parse(localStorage.getItem('keranjangUser'));
            } catch (e) {
                localStorage.removeItem('keranjangUser');
            }
        }
        axios.get('http://ihsanfirdaus.my.id/api/products',{
            params: {
                id: this.$route.params.id
            }
        })
        .then(res => (this.setData(res.data.data)))
        .catch(err => console.log(err));
    },
    methods: {
        changeImage(urlImage) {
            this.gambar_default = urlImage;
        },
        setData(data) {
            // replace object productDetails dengan data dari API
            this.productDetails = data;
            // replace object productGallery dengan data dari productDetails
            this.productGallery = this.productDetails.product_gallery;
            // replace value gambar_default dengan data dari API (product_gallery)
            this.gambar_default = data.product_gallery[0].photo
        },
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
        }
    },
}
</script>

<style scoped>
.product .pt {
    margin-right: 14px;
}
</style>
