<template>
  <q-layout view="hHh lpR fFf">
    <q-header elevated class="bg-red text-white" height-hint="98">
      <q-toolbar>
        <q-btn dense flat round icon="menu" @click="toggleLeftDrawer" />
        <q-toolbar-title>
          <q-avatar>
            <img src="https://banner2.cleanpng.com/20180520/vgk/kisspng-2016-toyota-4runner-car-toyota-c-hr-concept-logo-5b011df01ddaf3.8285008815267998561223.jpg" />
          </q-avatar>
          TOYOTA SHOWROOM
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-drawer show-if-above v-model="leftDrawerOpen" side="left" bordered>
      <q-list>
        <q-item-label class="text-h6">Keranjang Belanja</q-item-label>
        <q-separator />
        <q-item v-for="(item, index) in cart" :key="item.name">
          <q-item-section>
            <q-item-label>{{ item.name }}</q-item-label>
            <q-item-label caption>{{ item.price }}</q-item-label>
          </q-item-section>
          <q-item-section side>
            <q-btn flat dense icon="delete" color="red" @click="removeFromCart(index)" />
          </q-item-section>
        </q-item>
        <q-separator />
        <q-item>
          <q-item-section>
            <q-item-label>Total: {{ totalCartAmount }}</q-item-label>
          </q-item-section>
        </q-item>
      </q-list>
    </q-drawer>

    <q-page-container>
      <div class="q-pa-md q-gutter-sm">
        <q-carousel animated v-model="slide" infinite>
          <q-carousel-slide name="Toyota Supra MK4">
            <q-video class="absolute-full" src="https://www.youtube.com/embed/thgNDespy1Q" />
          </q-carousel-slide>
          <q-carousel-slide name="Toyota AE86">
            <q-video class="absolute-full" src="https://www.youtube.com/embed/qZTcUswMMqg" />
          </q-carousel-slide>
        </q-carousel>

        <div class="row justify-center">
          <q-btn-toggle
            glossy
            v-model="slide"
            :options="[
              { label: 'Toyota Supra MK4', value: 'Toyota Supra MK4' },
              { label: 'Toyota AE86', value: 'Toyota AE86' },
            ]"
          />
        </div>

        <!-- Card Content Moved Below Carousel -->
        <div class="q-pa-md row items-start q-gutter-md">
          <q-card v-for="car in cars" :key="car.name" class="my-card" flat bordered>
            <q-img :src="car.image" />
            <q-card-section>
              <div class="text-overline text-orange-9">Sport Car</div>
              <div class="text-h5 q-mt-sm q-mb-xs">{{ car.name }}</div>
              <div class="text-caption text-grey">{{ car.description }}</div>
              <div class="text-h6 q-mt-md">Harga: {{ car.price }}</div>
            </q-card-section>
            <q-card-actions>
              <q-btn flat color="primary" label="Masukkan Keranjang" @click="addToCart(car)" />
              <q-btn flat color="secondary" label="Beli" @click="confirmPurchase(car)" />
              <q-space />
              <q-btn
                color="grey"
                round
                flat
                dense
                :icon="expanded ? 'keyboard_arrow_up' : 'keyboard_arrow_down'"
                @click="expanded = !expanded"
              />
            </q-card-actions>
            <q-slide-transition>
              <div v-show="expanded">
                <q-separator />
                <q-card-section class="text-subtitle2">{{ car.details }}</q-card-section>
              </div>
            </q-slide-transition>
          </q-card>
        </div>
      </div>
      <router-view />
    </q-page-container>

    <q-footer elevated class="bg-grey-8 text-white">
      <q-toolbar class="footer" >
        <q-toolbar-title>
          <q-avatar>
            <img src="https://banner2.cleanpng.com/20180520/vgk/kisspng-2016-toyota-4runner-car-toyota-c-hr-concept-logo-5b011df01ddaf3.8285008815267998561223.jpg" />
          </q-avatar>
          <div>TOYOTA SHOWROOM</div>
        </q-toolbar-title>
      </q-toolbar>
    </q-footer>

    <q-dialog v-model="purchaseDialog">
      <q-card>
        <q-card-section class="row items-center">
          <q-avatar icon="info" color="primary" />
          <span class="q-ml-sm">Pembelian Berhasil</span>
        </q-card-section>
        <q-card-section>
          <p>Anda telah berhasil membeli {{ selectedCar.name }} seharga {{ selectedCar.price }}</p>
        </q-card-section>
        <q-card-actions align="right">
          <q-btn flat label="OK" color="primary" @click="purchaseDialog = false" />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-layout>
</template>

<script>
import { ref, computed } from 'vue'

export default {
  setup () {
    const leftDrawerOpen = ref(false)
    const slide = ref('Toyota AE86')
    const expanded = ref(false)
    const purchaseDialog = ref(false)
    const selectedCar = ref(null)
    const lorem = 'Yang menarik dari mobil ini tidak lain karena bisa diajak ngebut dengan kecepatan tertinggi 233 km/jam. Hasil kecepatan tersebut merupakan berkat perpaduan mesin mumpuni dengan dimensi kompak yang ringan. Meski dibawa kebut-kebutan di lintas balapan, Sobat Setir tetap nyaman duduk di kabin mobil dengan kursinya yang bergaya bucket.'
    const cart = ref([])

    const cars = ref([
      {
        name: 'New Toyota 86',
        image: 'https://astrido.s3.amazonaws.com/wp-content/uploads/2019/09/Crystal-BlackSilica.png',
        description: 'Mobil ini lebih tepat untuk yang menginginkan mobil sport Toyota generasi terbaru namun dengan harga lebih murah. Hampir menyentuh angka 1 miliar, dengan sejumlah eksklusivitas yang tidak meragukan. Eksterior khas coupe dengan grille baru memberikan kesan garang, bahkan saat hanya terpanjang di garasi rumah.',
        price: 'Rp 950.000.000',
        details: lorem
      },
      {
        name: 'Toyota Supra MK4',
        image: 'https://lh3.googleusercontent.com/s5UhSUYqmKqDvhfSajpz7YrS9hNl5VohIyKGjzZRpY-ibjkqyC2cED0QhqT73l9Vuco1quDbBJ_oGRFpOgmPxOjnSqfbxAO01eU-VazcE8AXatVx2o11vdqCe08QUc_eF-bB9IqBXF_TR28c_oiqbaQ',
        description: 'Toyota Supra MK4 terkenal dengan performa tinggi dan desain yang ikonik. Ditenagai mesin 2JZ yang legendaris, mobil ini mampu menghasilkan tenaga besar dan torsi yang memadai untuk kecepatan tinggi.',
        price: 'Rp 1.200.000.000',
        details: 'Toyota Supra MK4 memiliki kecepatan tertinggi sekitar 250 km/jam dan dapat mencapai 0-100 km/jam dalam waktu kurang dari 5 detik. Mobil ini sangat populer di kalangan penggemar mobil sport dan balap.'
      },
      {
        name: 'Toyota AE86',
        image: 'https://upload.wikimedia.org/wikipedia/commons/thumb/d/d9/Sprinter_Trueno_1600_GT_%28AE86%29.jpg/1200px-Sprinter_Trueno_1600_GT_%28AE86%29.jpg',
        description: 'Toyota AE86 adalah mobil legendaris yang dikenal dengan keseimbangannya yang sempurna dan handling yang responsif. Sangat populer di dunia drifting dan motorsport.',
        price: 'Rp 700.000.000',
        details: 'Dengan mesin 4A-GE yang kuat, AE86 menawarkan pengalaman berkendara yang luar biasa. Mobil ini juga dikenal dengan desainnya yang timeless dan performa yang solid di lintasan.'
      },
      {
        name: 'Toyota GR Supra',
        image: 'https://jdtoyota.co.id/wp-content/uploads/2020/03/matte-mineral-water.png',
        description: 'Toyota GR Supra keluaran tahun 2019 ini merupakan salah satu tipe tersukses Toyota Supra. Ditawarkan dengan harga lebih dari 2 miliar, tentunya memiliki beragam keunggulan melebihi para pesaingnya di segmen sport sedan. Bahkan bisa dibilang tergolong sempurna.',
        price: 'Rp 2.190.000.000',
        details: 'Eksteriornya dirancang sangat sporty, ditambah interior yang elegan dan mewah. Kemampuan mesinnya sangat tinggi karena merupakan hasil rancangan bersama dengan BMW. Salah satu poin lebih lainnya adalah fitur-fiturnya yang canggih.'
      },
      {
        name: 'Toyota Celica',
        image: 'https://www.autocar.co.uk/sites/autocar.co.uk/files/images/car-reviews/first-drives/legacy/toyota_celica_render_2023_front_three_quarter.jpg',
        description: 'Banyak yang menyebut mobil ini mirip dengan Toyota Soarer yaitu Toyota Celica. Dari segi harga pun sama-sama murah, walau hanya bisa didapatkan dalam kondisi bekas.',
        price: 'Rp 470.000.000',
        details: 'Unit dari generasi ketujuh antara tahun 2000-2005 memiliki tampilan paling oke sehingga banyak diminati. Desain bodi stylish tanpa bingkai jendela membuatnya sangat bergaya. Menurut berbagai perkumpulan para pecinta mobil sport di Indonesia, mobil ini acap kali dibawa dalam ajang balap rally dan pernah beberapa kali menjadi juara karena soal performa, Toyota Celica tidak dapat diragukan.'
      },
      {
        name: 'Toyota GT 86',
        image: 'https://imgx.gridoto.com/crop/38x81:949x593/700x465/photo/gridoto/2017/12/22/3919211700.jpg',
        description: 'Dari sisi mesin, mobil toyota ini menggunakan tipe mesin Boxer 4 Cylinder DOHC, dengan silinder sebesar 1,998 cc. Daya yang dihasilkan dari mesin tersebut sebesar 200 / 7,000 ps/rpm, dan torsinya adalah 20.9 / 6,600 kgm/rpm.',
        price: 'Rp 1.037.000.000',
        details: 'Toyota 86 ini memiliki ukuran panjang 4.240, lebar 1.775, dan tinggi 1.320. Sedangkan ukuran wheelbase sebesar 2.570 mm, front tread 1.520 mm, dan rear tread 1.540 mm.'
      },
      {
        name: 'Toyota Starlet GT',
        image: 'https://images.autofun.co.id/file1/ed23ea18bf5a4e12899424854fea979d_800.jpg',
        description: 'Toyota Starlet adalah mobil yang diproduksi oleh Toyota Motor Corporation, Japan dari tahun 1973 sampai 1999. Pada awalnya Starlet merupakan versi lebih mahal dari Toyota Publica, dan disebut Publica Starlet.',
        price: 'Rp 150.000.000',
        details: 'Setelah Starlet dihentikan produksinya pada tahun 1999, posisinya digantikan oleh Toyota Echo (1999) dan Toyota Yaris (2006). Di Indonesia, Starlet secara langsung diteruskan dengan sedan Toyota Soluna, sebelum munculnya Yaris pada 2006.'
      },
      {
        name: 'Toyota Levin',
        image: 'https://www.motortrend.com/uploads/f/29589205.jpg?fit=around%7C875:492',
        description: 'AE85 berbagi sasis dan desain dasar dengan AE86 yang terkenal , namun AE85 dirancang untuk ekonomi dan perbedaan utamanya terletak pada mesinnya, sedangkan AE86 dirancang untuk performa.',
        price: 'Rp 200.000.000',
        details: 'Itu hanya dijual di Jepang dan tidak dijual di Amerika Utara atau wilayah lain. Karakter utama di VIN tidak selalu menggunakan karakter yang sama dengan kode sasis, sehingga beberapa varian AE86 yang kurang bertenaga (dengan mesin 1,6 L 4A ) dijual di sana dengan kode sasis AE86 pada pelat build di ruang mesin. tapi dengan AE85 di VIN. '
      },
      {
        name: 'Toyota Soarer',
        image: 'https://www.blackxperience.com/assets/content/blackauto/autonews/l11.JPG',
        description: 'Rekomendasi berikutnya merupakan kembarannya mobil sport Supra namun dengan harga jauh lebih murah, sehingga merupakan salah satu pilihan terfavorit untuk koleksi mobil legend pertama.',
        price: 'Rp 868.501.250',
        details: 'Jika Supra unggul dari segi kecepatan dan pengendalian, Toyota Soarer bermain di aspek penampilan. Desainnya ramping dan dikemas dengan fitur-fitur canggih yang menjadikan mobil ini mampu memberikan pengalaman berkendara super halus.'
      }
    ])

    const toggleLeftDrawer = () => {
      leftDrawerOpen.value = !leftDrawerOpen.value
    }

    const addToCart = (car) => {
      cart.value.push(car)
    }

    const removeFromCart = (index) => {
      cart.value.splice(index, 1)
    }

    const confirmPurchase = (car) => {
      selectedCar.value = car
      purchaseDialog.value = true
    }

    const totalCartAmount = computed(() => {
      return cart.value.reduce((total, item) => {
        return total + parseInt(item.price.replace(/\D/g, ''), 10)
      }, 0)
    })

    return {
      leftDrawerOpen,
      slide,
      expanded,
      cars,
      cart,
      toggleLeftDrawer,
      addToCart,
      removeFromCart,
      purchaseDialog,
      selectedCar,
      confirmPurchase,
      totalCartAmount
    }
  }
}
</script>

<style lang="sass" scoped>
.my-card
  width: 100%
  max-width: 350px
</style>