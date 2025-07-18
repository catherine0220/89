<template>
  <div>
    <!-- Header -->
    <DynamicHeader />

    <!-- Banner -->
    <div class="flex justify-center">
      <img src="@/assets/images/cockfightingbanner.jpg" alt="banner" class="banner" />
    </div>

    <!-- Announcement -->
    <ABar />
    <div class="cockfighting-container">
      <div class="cockfighting-wrapper">
        <el-row :gutter="20">
          <el-col :span="8" v-for="(game, index) in category2Games" :key="index" class="image-box">
            <div class="image-wrapper" @click="navigateToGame(game.url)">
              <p class="intro">{{ game.name }}</p>
              <img
                :src="game.image_url"
                :alt="game.name"
                class="cockfighting-img"
                @error="(e) => (e.target.src = placeholderImage)"
              />
              <el-button
                class="img-button"
                type="primary"
                size="small"
                @click.stop="navigateToGame(game.url)"
              >
                进入游戏
              </el-button>
            </div>
          </el-col>
        </el-row>
      </div>
    </div>

    <!-- Footer -->
    <FooterMain />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import DynamicHeader from '@/views/DynamicHeader.vue'
import ABar from '@/views/ABar.vue'
import FooterMain from '../FooterMain.vue'
import axios from 'axios'
import placeholderImage from '@/assets/images/placeholder.png'

const category2Games = ref([])

// Fetch category 2 games (斗鸡游戏)
const fetchCategory2Games = async () => {
  try {
    const response = await axios.get('http://192.168.0.122/silver/user/game_list.php', {
      params: {
        category: 2,
        status: 1,
      },
    })

    if (response.data.success) {
      category2Games.value = response.data.data
        .map((game) => ({
          name: game.game_name || game.name,
          image_url: game.image_url
            ? `http://192.168.0.122${game.image_url.startsWith('/') ? '' : '/'}${game.image_url}`
            : placeholderImage,
          url: game.url || '#',
        }))
        .reverse()
    }
  } catch (error) {
    console.error('Error fetching category 2 games:', error)
    category2Games.value = []
  }
}

const navigateToGame = (url) => {
  if (url && url !== '#') {
    window.location.href = url
  }
}

onMounted(() => {
  fetchCategory2Games()
})
</script>

<style scoped>
.cockfighting-container {
  position: relative;
  padding: 30px 0;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.cockfighting-wrapper {
  position: relative;
  width: 1200px;
}

.cockfighting.img {
  position: relative;
  width: 390px;
  height: 250px;
  margin: 10px 0;
  overflow: hidden;
  cursor: pointer;
}

.image-wrapper {
  position: relative;
  width: 100%;
  height: auto;
  overflow: hidden;
  cursor: pointer;
}

.image-box {
  margin-bottom: 25px;
}

.cockfighting-img {
  width: 100%;
  border-radius: 10px;
  display: block;
}

.img-button {
  position: absolute;
  left: 23px;
  bottom: 38px;
  width: 104px;
  height: 32px;
  color: #ffd630;
  border-radius: 16px;
  border: solid 1px #ffd630;
  background-color: transparent;
  font-size: 14px;
}

.image-wrapper:hover .img-button {
  background-color: #ffd630;
  color: black;
  border: solid 1px #ffd630;
}

.image-wrapper:hover .img-button:hover {
  background-color: #ffd630;
  color: black;
  border: solid 1px #ffd630;
}

.intro {
  position: absolute;
  left: 27px;
  bottom: 88px;
  margin: 0;
  color: #fff;
  font-size: 18px;
  z-index: 1;
}
</style>
