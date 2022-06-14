<template>
    <!-- Women Banner Section Begin -->
    <section class="women-banner spad">
        <div class="container-fluid">
            <div class="row">
                <div class="col-lg-12 mt-5" v-if="products.length > 0">
                    <carousel class="product-slider" :items="3" :dots="false" :autoplay="true" :nav="false">

                        <div class="product-item" v-for="itemProduct in products" v-bind:key="itemProduct.id">
                            <div class="pi-pic">
                                <img v-bind:src="itemProduct.galleries[0].photo" alt="" />
                                <ul>
                                    <li class="w-icon active">
                                        <a href="#"
                                            @click="saveKeranjang(itemProduct.id, itemProduct.name, itemProduct.price, itemProduct.galleries[0].photo)"><i
                                                class="icon_bag_alt"></i></a>
                                    </li>
                                    <li class="quick-view">
                                        <router-link v-bind:to="'/product/' + itemProduct.id">
                                            + Quick View
                                        </router-link>
                                    </li>
                                </ul>
                            </div>
                            <div class="pi-text">
                                <div class="catagory-name">
                                    {{ itemProduct.type }}
                                </div>
                                <router-link v-bind:to="'/product/' + itemProduct.id">
                                    <a href="#">
                                        <h5>{{ itemProduct.name }}</h5>
                                    </a>
                                </router-link>
                                <div class="product-price">
                                    ${{ itemProduct.price }}
                                    <span>${{ itemProduct.price + itemProduct.price }}</span>
                                </div>
                            </div>
                        </div>

                    </carousel>
                </div>

                <div class="col-lg-12" v-else>
                    <p>Produk belum tersedia untuk saat ini</p>
                </div>
            </div>
        </div>
    </section>
    <!-- Women Banner Section End -->
</template>

<script>
import carousel from "vue-owl-carousel";
import axios from "axios";

export default {
    name: "WomenShop",
    components: {
        carousel
    },
    data() {
        return {
            products: [],
            keranjangUser: []
        }
    },
    methods: {
        saveKeranjang(idProduct, nameProduct, priceProduct, photoProduct) {

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
    mounted() {
        if (localStorage.getItem('keranjangUser')) {
            try {
                this.keranjangUser = JSON.parse(localStorage.getItem('keranjangUser'));
            } catch (e) {
                localStorage.removeItem('keranjangUser');
            }
        }

        axios
            .get("http://laravue-shayna.io:8080/api/products")
            .then(res => {
                this.products = res.data.data.data;
            }).catch(error => {
                console.log(error);
            });
    }
}
</script>

<style scoped>
.product-item {
    margin-right: 25px;
}

.pi-pic img {
    height: 450px;
}
</style>