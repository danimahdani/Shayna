<template>
  <div class="home">
      <HeaderShayna />
          <!-- Breadcrumb Section Begin -->
    <div class="breacrumb-section">
        <div class="container">
            <div class="row">
                <div class="col-lg-12">
                    <div class="breadcrumb-text product-more text-left">
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
                            <div class="product-thumbs" v-if="productDetails.galleries.length">
                                <carousel class="product-thumbs-track ps-slider" :autoplay="true" :nav="false" :dots="false" >
                                    <div 
                                    v-for="img in productDetails.galleries"
                                    :key="img.id"
                                    class="pt mr-2" 
                                    :class="{active: gambar_default == img.photo}"  
                                    @click="changeImage(img.photo)">
                                        <img :src="img.photo" alt="" />
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
                                    <p v-html="productDetails.description">
                                    </p>
                                    <h4>${{ productDetails.price }}</h4>
                                </div>
                                <div class="quantity">
                                    <a href="#"
                                       @click="saveKeranjang(productDetails.id)"
                                       class="primary-btn pd-cart">Add To Cart</a>
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
// import HelloWorld from '@/components/HelloWorld.vue'
import HeaderShayna  from '@/components/HeaderShayna.vue';
import carousel from 'vue-owl-carousel';
import FooterShayna from '@/components/FooterShayna.vue';
import RelatedShayna from '@/components/RelatedShayna.vue';
import axios from 'axios';

export default {
  name: 'Product',
  components: {
    // HelloWorld
    HeaderShayna,
    carousel,
    FooterShayna,
    RelatedShayna
  },
  data() {
    return {
      gambar_default: "",
      productDetails: [],
      keranjangaUser: [],
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
    saveKeranjang(idProduct) {
        this.keranjangaUser.push(idProduct);
        const parsed = JSON.stringify(this.keranjangaUser);
        localStorage.setItem('keranjangaUser', parsed);
    }
  },
    mounted() {
        if (localStorage.getItem('keranjangaUser')) {
            try {
                this.cats = JSON.parse(localStorage.getItem('keranjangaUser'));
            } catch(e) {
                localStorage.removeItem('keranjangaUser');
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
    }
};
</script>
