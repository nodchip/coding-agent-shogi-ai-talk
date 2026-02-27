<template>
  <div class="slidev-layout two-cols">
    <div class="cols">
      <div class="col">
        <slot name="left" />
      </div>
      <div class="col">
        <slot name="right" />
      </div>
    </div>
    <slot />
  </div>
</template>

<style scoped>
.cols {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 28px;
  padding: 28px;
  border-radius: 16px;
  border: 1px solid rgba(255, 255, 255, 0.12);
  background: rgba(255, 255, 255, 0.04);
}
.col :deep(h2) {
  margin-top: 0;
}
</style>

