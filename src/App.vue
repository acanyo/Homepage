<template>
  <div class="wallpaper-background" v-if="wallpaperConfig.enabled">
    <!-- 图片背景 -->
    <img 
      v-if="wallpaperConfig.type === 'image'" 
      :src="wallpaperConfig.url" 
      alt="Background" 
      class="wallpaper-image" 
    />
    <!-- 视频背景 -->
    <video 
      v-if="wallpaperConfig.type === 'video'"
      :src="wallpaperConfig.url"
      class="wallpaper-video"
      autoplay
      muted
      loop
      playsinline
    />
    <div class="wallpaper-overlay" :style="{ opacity: wallpaperConfig.opacity }"></div>
  </div>
  <Transition name="fade" appear>
    <Loading v-if="showLoading" />
  </Transition>
  <main>
    <main-card></main-card>
  </main>
  <div class="reThemeBtn" :data-theme="theme" @click="changeTheme" :title="theme === 'light' ? '切换到暗色模式' : '切换到亮色模式'">
  </div>
</template>

<script setup>
import MainCard from "./views/MainCard.vue";
import Loading from "./components/Loading.vue";
import wallpaperConfig from "./config/wallpaper.json";
import { onMounted, ref } from "vue";

let theme = ref(localStorage.getItem("theme") || "light");
let showLoading = ref(true);
document.body.style.overflow = "hidden";

onMounted(() => {
  document.body.setAttribute("theme", theme.value);
  showLoading.value = false;
  setTimeout(() => {
    document.body.style.overflow = "";
  }, 300);
});

const changeTheme = () => {
  if (theme.value == "light") {
    theme.value = "dark";
    onTheme("dark");
  } else {
    theme.value = "light";
    onTheme("light");
  }

};

const onTheme = (theme) => {
  document.body.setAttribute("theme", theme);
  localStorage.setItem("theme", theme);
};
</script>

<style>
@import url(assets/css/App.css);

/* 添加渐隐效果的CSS */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
