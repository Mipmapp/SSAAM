<template>
  <div class="loading-screen">
    <div class="flex flex-col items-center justify-center gap-6 text-center">
      <div class="w-32 h-32 flex items-center justify-center animate-bounce">
        <img :src="jrmsuLogo" alt="JRMSU CCS Logo" class="w-full h-full object-contain drop-shadow-2xl" />
      </div>
      <div>
        <h1 class="text-4xl font-bold text-white mb-2">SSAAM</h1>
        <p class="text-lg text-white opacity-90">Student School Activities Attendance Monitoring</p>
      </div>
      <div class="flex gap-2 mt-8">
        <div class="w-3 h-3 rounded-full bg-white animate-pulse"></div>
        <div class="w-3 h-3 rounded-full bg-white animate-pulse" style="animation-delay: 0.2s;"></div>
        <div class="w-3 h-3 rounded-full bg-white animate-pulse" style="animation-delay: 0.4s;"></div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted } from 'vue'
import { useRouter } from 'vue-router'
import jrmsuLogo from '../assets/jrmsu-logo.webp'

const router = useRouter()

// List of all images to preload
const imagesToPreload = [
  '/user.svg',
  '/key.svg',
  '/mail.svg',
  '/register_user.svg',
  '/arrow_down.svg',
  '/course.svg',
  '/book.svg',
  '/detector.svg',
  '/calendar.svg',
  '/event_note.svg',
  '/home.svg',
  '/logout.svg',
  '/help.svg',
  '/visibility_on.svg',
  '/visibility_off.svg',
  '/classroom-bg.jpg',
  jrmsuLogo
]

const preloadImages = () => {
  return Promise.all(
    imagesToPreload.map(
      (imageSrc) =>
        new Promise((resolve) => {
          const img = new Image()
          img.onload = resolve
          img.onerror = resolve // Resolve on error too, so we don't wait indefinitely
          img.src = imageSrc
        })
    )
  )
}

onMounted(async () => {
  // Preload all images before proceeding
  await preloadImages()
  
  // Give a small buffer to ensure rendering is complete
  setTimeout(() => {
    router.push('/')
  }, 500)
})
</script>

<style>
.loading-screen {
  width: 100%;
  height: 100vh;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
