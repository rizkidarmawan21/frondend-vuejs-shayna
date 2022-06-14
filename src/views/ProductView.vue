<template>
  <div class="product">
    <HeaderShop />
    <!-- karena tidak menggunakan ke halaman yang berbeda -->
    <!-- Breadcrumb Section Begin -->
    <div class="breacrumb-section text-left">
      <div class="container">
        <div class="row">
          <div class="col-lg-12">
            <div class="breadcrumb-text product-more">
              <router-link to="/">
                <i class="fa fa-home"></i>
                Home
              </router-link>
              <span>Detail</span>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- Breadcrumb Section Begin -->

    <!-- Product Shop Section Begin -->
    <section class="product-shop spad page-details text-left">
      <div class="container">
        <div class="row">
          <div class="col-lg-12">
            <div class="row">
              <div class="col-lg-6">
                <div class="product-pic-zoom">
                  <img class="product-big-img" :src="gambar_default" alt="" />
                </div>
                <div class="product-thumbs" v-if="productDetails.galleries.length > 0">
                  <carousel
                    :dots="false"
                    :nav="false"
                    class="product-thumbs-track ps-slider"
                  >
                    <div class="" v-for="ss in productDetails.galleries" :key="ss.id">
                      <div class="pt" @click="changeImage(ss.photo)" :class="ss.photo == gambar_default ? 'active' : '' ">
                        <img :src="ss.photo" alt="" />
                      </div>
                    </div>
                  </carousel>
                </div>
              </div>
              <div class="col-lg-6">
                <div class="product-details">
                  <div class="pd-title">
                    <span> {{ productDetails.type }} </span>
                    <h3>{{ productDetails.name }}</h3>
                  </div>
                  <div class="pd-desc" >
                    <p v-html="productDetails.description"></p>
                    <h4>${{ productDetails.price }}</h4>
                  </div>
                  <div class="quantity">
                    <router-link to="/cart">
                      <a href="#" @click="saveKeranjang(productDetails.id, productDetails.name,productDetails.price,productDetails.galleries[0].photo)" class="primary-btn pd-cart"
                        >Add To Cart</a>
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
    <RelatedShop />

    <FooterShop />
  </div>
</template>

<script>
// @ is an alias to /src
import HeaderShop from '@/components/HeaderShop.vue'
import RelatedShop from '@/components/RelatedShop.vue'
import FooterShop from '@/components/FooterShop.vue'
import carousel from "vue-owl-carousel"
import axios from 'axios'

export default {
  name: 'ProductView',
  components: {
    HeaderShop,
    RelatedShop,
    FooterShop,
    carousel
  },
  data(){
    return{
      gambar_default: '',
      productDetails:[],
      keranjangUser:[]
    }
  },
  methods : {
    changeImage(img){
      this.gambar_default = img;
    },
    setDataPicture(data){
        this.productDetails = data;
        this.gambar_default = data.galleries[0].photo;
    },
    saveKeranjang(idProduct,nameProduct,priceProduct,photoProduct){

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
  
  mounted() {
    if(localStorage.getItem('keranjangUser')) {
      try {
        this.keranjangUser = JSON.parse(localStorage.getItem('keranjangUser'));
      } catch(e){
        localStorage.removeItem('keranjangUser');
      }
    }
        axios
            .get("http://laravue-shayna.io:8080/api/products/", {
                params: {
                    id: this.$route.params.id
                }
            })
            .then(res => {
                // this.productDetails = res.data.data;
                this.setDataPicture(res.data.data);
            }).catch(error => {
                console.log(error);
            });
    }
};
</script>

<style scoped>
.product-thumbs .pt {
  margin-right: 10px;
}
</style>