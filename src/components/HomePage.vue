<template>
  <h1>每小時只能取得50張圖片...手下留情！</h1>
  <div class="wrapper" v-if="!loadingNow">
    <div>
      <img :src="photoUrl" alt="photo" />
      <div class="btnWrapper">
        <button type="button" class="btn" @click="changePhoto">換一張</button>
        <button type="button" class="btn" @click="copyPhotoUrl">複製圖片位址</button>
      </div>
    </div>
  </div>

  <LoadingSpinner :loadingNow="loadingNow" />
</template>

<script>
import LoadingSpinner from './LoadingSpinner.vue'
const { VITE_ACCESS_KEY } = import.meta.env

export default {
  components: { LoadingSpinner },
  data() {
    return {
      url: 'https://api.unsplash.com/photos/random',
      key: VITE_ACCESS_KEY,
      photoUrl: '',
      loadingNow: true
    }
  },
  methods: {
    async getRandomPhoto() {
      this.loadingNow = true
      try {
        const res = await fetch(this.url, {
          method: 'GET',
          headers: {
            Authorization: `Client-ID ${this.key}`,
            'Content-Type': 'application/json'
          }
        })
        if (!res.ok) {
          throw new Error('似乎有些不對勁！')
        }
        const data = await res.json()
        this.photoUrl = data.urls.regular
        this.loadingNow = false
      } catch (err) {
        this.loadingNow = false
        this.errorAlert(String(err))
      }
    },
    changePhoto() {
      this.changeAlert()
      this.getRandomPhoto()
    },
    copyPhotoUrl() {
      if (!this.photoUrl) {
        this.errorAlert('目前沒有圖片')
      } else {
        navigator.clipboard.writeText(this.photoUrl)
        this.copyAlert()
      }
    },
    toast() {
      return this.$swal.mixin({
        toast: true,
        position: 'top-end',
        showConfirmButton: false,
        timer: 1200,
        timerProgressBar: true,
        didOpen: (toast) => {
          toast.addEventListener('mouseenter', this.$swal.stopTimer)
          toast.addEventListener('mouseleave', this.$swal.resumeTimer)
        }
      })
    },
    copyAlert() {
      this.toast().fire({
        icon: 'success',
        title: '複製成功！'
      })
    },
    changeAlert() {
      this.toast().fire({
        icon: 'success',
        title: '馬上來...'
      })
    },
    errorAlert(msg = '有地方出錯了！？') {
      this.$swal.fire('錯誤', msg, 'error')
    }
  },
  mounted() {
    setTimeout(() => {
      this.getRandomPhoto()
    }, 500)
  }
}
</script>

<style scoped>
h1 {
  text-align: center;
  font-size: 1.5rem;
  font-weight: bold;
  margin-bottom: 2rem;
  color: #1b3a68;
}

.wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.wrapper img {
  height: 60vh;
  max-width: 100%;
}

.btnWrapper {
  display: flex;
  justify-content: space-around;
  margin: 2rem auto 0 auto;
}

.btn {
  display: inline-block;
  padding: 0.5rem 1rem;
  font-size: 1rem;
  font-weight: bold;
  text-align: center;
  text-decoration: none;
  border: 2px solid #1b3a68;
  color: #1b3a68;
  background-color: transparent;
  border-radius: 0.5rem;
  cursor: pointer;
  transition: all 0.3s ease-in-out;
}

.btn:hover {
  background-color: #1b3a68;
  color: #fff;
}
</style>
