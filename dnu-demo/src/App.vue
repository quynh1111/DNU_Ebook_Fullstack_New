<script setup>
import { ref } from 'vue'
import Product from './components/Product.vue'

const gpa = 4.0;

</script>

<template>
  <div class="app">
      <div v-if="gpa >= 9">
        <p>Xuất sắc</p>
      </div>
      <div v-else-if="gpa >= 8">
        <p>Giỏi</p>
      </div>
      <div v-else-if="gpa >= 7">
        <p>Khá</p>
      </div>
      <div v-else>
        <p>Còn lại</p>
      </div>
  </div>
</template>

<style scoped>
.app {
  padding: 20px;
}

.app h1 {
  font-size: 2em;
  color: #333;
  margin-bottom: 20px;
}

.products-list {
  display: flex;
  gap: 20px;
  flex-wrap: wrap;
}
</style>