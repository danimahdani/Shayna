<template>
  <div class="shopping-card">
    <HeaderShayna />
    <!-- Breadcrumb Section Begin -->
    <div class="breacrumb-section">
        <div class="container">
            <div class="row">
                <div class="col-lg-12 text-left">
                    <div class="breadcrumb-text product-more">
                        <router-link to="/">
                            <i class="fa fa-home"></i> Home
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
                                    <tbody v-if="keranjangUser.length">
                                        <tr v-for="keranjang in keranjangUser" :key="keranjang.id">
                                            <td class="cart-pic first-row">
                                                <img width="100px" height="100px" :src="keranjang.photo" />
                                            </td>
                                            <td class="cart-title first-row text-center">
                                                <h5>{{ keranjang.name }}</h5>
                                            </td>
                                            <td class="p-price first-row">${{ keranjang.price }}</td>
                                            <td @click="removeItem(keranjangUser.index)" class="delete-item"><a href="#"><i class="material-icons">
                                              close
                                              </i></a></td>
                                        </tr>
                                    </tbody>
                                </table>
                            </div>
                        </div>
                        <div class="col-lg-8 ">
                            <h4 class="mb-4 text-left">
                                Informasi Pembeli:
                            </h4>
                            <div class="user-checkout text-left">
                                <form>
                                    <div class="form-group">
                                        <label for="namaLengkap">Nama lengkap</label>
                                        <input type="text" class="form-control" id="namaLengkap" aria-describedby="namaHelp" placeholder="Masukan Nama" v-model="costumerInfo.name">
                                    </div>
                                    <div class="form-group">
                                        <label for="namaLengkap">Email Address</label>
                                        <input type="email" class="form-control" id="emailAddress" aria-describedby="emailHelp" placeholder="Masukan Email" v-model="costumerInfo.email">
                                    </div>
                                    <div class="form-group">
                                        <label for="namaLengkap">No. HP</label>
                                        <input type="text" class="form-control" id="noHP" aria-describedby="noHPHelp" placeholder="Masukan No. HP" v-model="costumerInfo.number">
                                    </div>
                                    <div class="form-group">
                                        <label for="alamatLengkap">Alamat Lengkap</label>
                                        <textarea class="form-control" id="alamatLengkap" rows="3" v-model="costumerInfo.address"></textarea>
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
                                    <li class="subtotal">ID Transaction <span>#SH12000</span></li>
                                    <li class="subtotal mt-3">Subtotal <span>${{ totalHarga }}.00</span></li>
                                    <li class="subtotal mt-3">Pajak <span>10% ${{ ditambahPajak }}.00</span></li>
                                    <li class="subtotal mt-3">Total Biaya <span>${{ totalBiaya }}.00</span></li>
                                    <li class="subtotal mt-3">Bank Transfer <span>Mandiri</span></li>
                                    <li class="subtotal mt-3">No. Rekening <span>2208 1996 1403</span></li>
                                    <li class="subtotal mt-3">Nama Penerima <span>Shayna</span></li>
                                </ul>
                                <!-- <router-link to="/success" class="proceed-btn"> -->
                                   <a @click="checkout()" href="#" class="proceed-btn"> I ALREADY PAID </a>
                                <!-- </router-link> -->
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>
    <!-- Shopping Cart Section End -->
  </div>
</template>

<script>
import HeaderShayna from '@/components/HeaderShayna.vue';
import axios from 'axios';


export default {
  name: 'Cart',
  components: {
      HeaderShayna
  },
    data() {
    return {
      keranjangUser: [],
      costumerInfo: {
          name: '',
          email: '',
          number: '',
          address: ''
      }
    }
  },
  methods: {
    changeImage(urlImage) {
      this.gambar_default = urlImage;
    },
    setDataPicture(data) {
        this.productDetails = data;
        // console.log(this.productDetails);
        this.gambar_default = data.galleries[0].photo;
    },
    saveKeranjang(idProduct,productName,productPrice,productPhoto) {

        let ProductStore = {
            'id': idProduct,
            'name': productName,
            'price': productPrice,
            'photo': productPhoto,
        }

        this.keranjangUser.push(ProductStore);
        const parsed = JSON.stringify(this.keranjangUser);
        localStorage.setItem('keranjangUser', parsed);
    },
     removeItem(index) {
          this.keranjangUser.splice(index, 1);
          const parsed = JSON.stringify(this.keranjangUser);
          localStorage.setItem('keranjangUser', parsed);
    },
    //mengirim data ke API
    checkout(){
        let productIds = this.keranjangUser.map(function (product) {
            return product.id;
        });

        let checkoutData = {
            'name': this.costumerInfo.name,
            'email': this.costumerInfo.email,
            'number': this.costumerInfo.number,
            'address': this.costumerInfo.address,
            'transaction_total': this.totalBiaya,
            'transaction_status': "PENDING",
            'transaction_details': productIds
        }

        axios.post("http://shayna-backend.belajarkoding.com/api/checkout", checkoutData)
        .then(() => this.$router.push("success"))
        .catch(err => console.log(err));
    }
  },
    mounted() {
        if (localStorage.getItem('keranjangUser')) {
            try {
                this.keranjangUser = JSON.parse(localStorage.getItem('keranjangUser'));
            } catch(e) {
                localStorage.removeItem('keranjangUser');
            }
        }
        axios
        .get("http://shayna-backend.belajarkoding.com/api/products", {
            params: {
                id: this.$route.params.id
            }
        })
        .then(res => (this.setDataPicture(res.data.data)))
        .catch(err => console.log(err));
    },
     computed: {
      totalHarga() {
          return this.keranjangUser.reduce(function(items, data){
              return items + data.price;
          }, 0);
      },
      ditambahPajak()  {
          return (this.totalHarga * 10) / 100
      },
      totalBiaya() {
          return this.totalHarga + this.ditambahPajak;
      }
  }
}
</script>