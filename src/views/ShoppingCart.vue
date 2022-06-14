<template>
    <div class="shopping">
        <HeaderShop />
        <!-- Breadcrumb Section Begin -->
        <div class="breacrumb-section">
            <div class="container">
                <div class="row">
                    <div class="col-lg-12 text-left">
                        <div class="breadcrumb-text product-more">
                            <router-link to="/">
                                <i class="fa fa-home"></i>
                                Home
                            </router-link>
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
                                        <tbody>
                                            <tr v-for="(keranjang, index) in keranjangUser" :key="keranjang.id">
                                                <td class="cart-pic first-row">
                                                    <img class="photo-item" :src="keranjang.photo" />
                                                </td>
                                                <td class="cart-title first-row text-center">
                                                    <h5>{{ keranjang.name }}</h5>
                                                </td>
                                                <td class="p-price first-row">${{ keranjang.price }}.00</td>
                                                <td class="delete-item"><a href="#" @click="removeItem(index)"><i
                                                            class="material-icons">
                                                            close
                                                        </i></a></td>
                                            </tr>
                                        </tbody>
                                    </table>
                                </div>
                            </div>
                            <div class="col-lg-8">
                                <h4 class="mb-4 text-left">
                                    Informasi Pembeli:
                                </h4>
                                <div class="user-checkout text-left">
                                    <form>
                                        <div class="form-group">
                                            <label for="namaLengkap">Nama lengkap</label>
                                            <input type="text" class="form-control" id="namaLengkap"
                                                aria-describedby="namaHelp" placeholder="Masukan Nama"
                                                v-model="customerInfo.name">
                                        </div>
                                        <div class="form-group">
                                            <label for="namaLengkap">Email Address</label>
                                            <input type="email" class="form-control" id="emailAddress"
                                                aria-describedby="emailHelp" placeholder="Masukan Email"
                                                v-model="customerInfo.email">
                                        </div>
                                        <div class="form-group">
                                            <label for="namaLengkap">No. HP</label>
                                            <input type="number" class="form-control" id="noHP"
                                                aria-describedby="noHPHelp" placeholder="Masukan No. HP"
                                                v-model="customerInfo.number">
                                        </div>
                                        <div class="form-group">
                                            <label for="alamatLengkap">Alamat Lengkap</label>
                                            <textarea class="form-control" id="alamatLengkap" rows="3"
                                                v-model="customerInfo.address"></textarea>
                                        </div>
                                    </form>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="col-lg-4">
                        <div class="row">
                            <div class="col-lg-12">
                                <div class="proceed-checkout text-left">
                                    <ul>
                                        <!-- <li class="subtotal">ID Transaction <span>#SH12000</span></li> -->
                                        <li class="subtotal mt-3">Subtotal <span>${{ totalHarga }}.00</span></li>
                                        <li class="subtotal mt-3">Pajak <span>10% ${{ ditambahPajak }}.00</span></li>
                                        <li class="subtotal mt-3">Total Biaya <span>${{ totalBiaya }}.00</span></li>
                                        <li class="subtotal mt-3">Bank Transfer <span>Mandiri</span></li>
                                        <li class="subtotal mt-3">No. Rekening <span>2208 1996 1403</span></li>
                                        <li class="subtotal mt-3">Nama Penerima <span>Shayna</span></li>
                                    </ul>
                                    <!-- <router-link to="/success"> -->
                                    <a href="#" @click="checkout()" class="proceed-btn">I ALREADY PAID</a>
                                    <!-- </router-link> -->
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </section>
        <!-- Shopping Cart Section End -->
        <FooterShop />

    </div>

</template>

<script>
// import header
import HeaderShop from '@/components/HeaderShop.vue'
// import footer
import FooterShop from '@/components/FooterShop.vue'

import axios from 'axios';

export default ({
    name: 'ShoppingCart',
    components: {
        HeaderShop,
        FooterShop
    },
    data() {
        return {
            keranjangUser: [],
            customerInfo: {
                name: '',
                email: '',
                number: '',
                address: ''
            }
        };
    },
    methods: {
        removeItem(idx) {
            let keranjangUserStorage = JSON.parse(localStorage.getItem('keranjangUser'));
            let itemKeranjangUserStorage = keranjangUserStorage.map(itemKeranjangUserStorage => itemKeranjangUserStorage.id);
            let index = itemKeranjangUserStorage.findIndex(id => id == idx);
            this.keranjangUser.splice(index, 1);

            const parsed = JSON.stringify(this.keranjangUser);
            localStorage.setItem('keranjangUser', parsed);
            window.location.reload();
        },
        checkout() {
            let productIds = this.keranjangUser.map(function (product) {
                return product.id;
            });

            let checkoutData = {
                name: this.customerInfo.name,
                email: this.customerInfo.email,
                number: this.customerInfo.number,
                address: this.customerInfo.address,
                transaction_total: this.totalBiaya,
                transaction_status: "PENDING",
                transaction_details: productIds
            };

            axios
                .post(
                    "http://laravue-shayna.io:8080/api/checkout",
                    checkoutData
                )
                .then(() => {
                    // delete all data in keranjangUser

                    localStorage.removeItem('keranjangUser');
                    this.$router.push("success")
                })
                // eslint-disable-next-line no-console
                .catch(err => console.log(err));
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
    },
    computed: {
        totalHarga() {
            return this.keranjangUser.reduce((total, item) => {
                return total + item.price;
            }, 0);
        },
        ditambahPajak() {
            return (this.totalHarga * 10) / 100;

        },
        totalBiaya() {
            return (this.totalHarga + this.ditambahPajak);
        }
    }
})
</script>
<style scoped>
.photo-item {
    width: 80px;
    height: 80px;
}
</style>