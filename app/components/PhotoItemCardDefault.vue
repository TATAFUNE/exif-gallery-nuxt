<script setup lang="ts">
import type { HTMLAttributes } from 'vue'
import { cn } from '~/lib/utils'

defineProps<{
  photo?: IPhoto
  imageClass?: HTMLAttributes['class']
  mini?: boolean
  fullscreen?: boolean
  priority?: boolean
}>()
</script>

<template>
  <div
    :class="[
      fullscreen ? 'h-dvh w-dvw' : 'w-full h-auto',
      mini ? 'h-full' : 'w-full',
    ]"
  >
    <picture v-if="photo">
      <source v-if="photo.avif" :srcset="`/photos/${photo.avif}`" type="image/avif">
      <source v-if="photo.webp" :srcset="`/photos/${photo.webp}`" type="image/webp">
      <img
        :src="`/photos/${photo.jpeg || photo.webp || photo.avif}`"
        :class="cn('w-full h-auto block object-contain', fullscreen ? 'rounded-none' : 'rounded-lg', imageClass)"
        :fetchpriority="priority ? 'high' : undefined"
        :alt="photo.semanticDescription || photo.caption || photo.title"
      >
    </picture>
  </div>
</template>

<style scoped>

</style>
