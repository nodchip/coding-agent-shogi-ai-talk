<script setup lang="ts">
import { computed } from 'vue'

const props = defineProps({
  image: {
    type: String,
    default: '',
  },
  class: {
    type: String,
  },
  backgroundSize: {
    type: String,
    default: 'contain',
  },
  columns: {
    type: String,
    default: '1.7fr 0.3fr',
  },
  frameMaxWidth: {
    type: String,
    default: '340px',
  },
  frameMaxHeight: {
    type: String,
    default: '320px',
  },
})

const imgStyle = computed(() => ({
  maxWidth: props.frameMaxWidth,
  maxHeight: props.frameMaxHeight,
  objectFit: props.backgroundSize === 'cover' ? 'cover' : 'contain',
}))
</script>

<template>
  <div
    class="image-right-framed grid w-full h-full auto-rows-fr"
    :style="{ gridTemplateColumns: props.columns }"
  >
    <div class="slidev-layout default" :class="props.class">
      <slot />
    </div>
    <div class="image-right-framed__col">
      <div class="image-right-framed__frame">
        <img
          v-if="props.image"
          class="image-right-framed__image"
          :src="props.image"
          alt=""
          :style="imgStyle"
        >
      </div>
      <div class="image-right-framed__below">
        <slot name="right" />
      </div>
    </div>
  </div>
</template>

<style scoped>
.image-right-framed__col {
  display: grid;
  place-items: center;
  padding: 28px;
  align-content: center;
  gap: 12px;
}

.image-right-framed__frame {
  display: inline-block;
  border-radius: 16px;
  border: 1px solid rgba(15, 23, 42, 0.18);
  background: rgba(255, 255, 255, 0.72);
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.18);
  padding: 14px;
}

.image-right-framed__image {
  display: block;
  border-radius: 12px;
  background-color: rgba(15, 23, 42, 0.03);
}

.image-right-framed__below {
  text-align: center;
}

.image-right-framed__below :deep(p) {
  margin: 0;
}

html.dark .image-right-framed__frame {
  border-color: rgba(255, 255, 255, 0.16);
  background: rgba(255, 255, 255, 0.06);
  box-shadow: 0 10px 34px rgba(0, 0, 0, 0.42);
}

html.dark .image-right-framed__image {
  background-color: rgba(0, 0, 0, 0.18);
}
</style>
