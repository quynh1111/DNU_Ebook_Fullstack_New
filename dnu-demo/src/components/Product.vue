<script setup>
const props = defineProps({
  name: String,
  price: Number
})
</script>

<template>
  <div class="product">
    <h3>{{ name }}</h3>
    <p>Giá: {{ price }} đ</p>
  </div>
</template>

<style scoped>
.product {
  border: 1px solid #ccc;
  padding: 15px;
  margin: 10px;
}

.product h3 {
  margin: 0 0 10px 0;
}

.product p {
  margin: 0;
  color: #e74c3c;
  font-weight: bold;
}
</style>
