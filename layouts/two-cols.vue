<script setup lang="ts">
const props = defineProps({
  columns: {
    type: String,
    default: '1fr 1fr',
  },
})
</script>

<template>
  <div class="slidev-layout two-cols">
    <div class="top">
      <slot />
    </div>
    <div class="cols" :style="{ gridTemplateColumns: props.columns }">
      <div class="col">
        <slot name="left" />
      </div>
      <div class="col">
        <slot name="right" />
      </div>
    </div>
  </div>
</template>

<style scoped>
.top {
  padding: var(--two-cols-top-padding, 28px 28px 0);
}
.top :deep(h1),
.top :deep(h2),
.top :deep(h3) {
  margin: 0;
}
.cols {
  margin-top: var(--two-cols-card-gap, 16px);
  display: grid;
  gap: var(--two-cols-gap, 28px);
  padding: var(--two-cols-padding, 28px);
  border-radius: 16px;
  border: 1px solid rgba(255, 255, 255, 0.12);
  background: rgba(255, 255, 255, 0.04);
}
.col :deep(h2) {
  margin-top: 0;
}
</style>
