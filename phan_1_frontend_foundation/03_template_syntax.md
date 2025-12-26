# 🟦 BÀI 3: TEMPLATE SYNTAX CƠ BẢN

## 🎯 Mục tiêu
- Hiểu Template trong Vue là gì
- Sử dụng Interpolation ({{ }})
- Sử dụng v-bind để bind attributes
- Sử dụng v-on để xử lý events
- Hiểu sự khác biệt giữa JavaScript thuần và Vue

---

## 🧠 1. Template là gì?

### 🎬 Ví dụ dẫn nhập: Website bán hàng

Hãy tưởng tượng bạn đang xây dựng website bán hàng như **Shopee** hoặc **Tiki**:

**Vấn đề thực tế:**
- Giá sản phẩm thay đổi theo thời gian (sale, flash sale)
- Số lượng hàng trong kho thay đổi mỗi khi có người mua
- Tên người dùng hiển thị khác nhau tùy theo ai đang đăng nhập
- Trạng thái "Đã giao hàng" / "Đang vận chuyển" thay đổi theo thời gian thực

**Với HTML thuần:**
```html
<!-- ❌ Phải viết lại HTML mỗi lần data thay đổi -->
<div>
  <h1>iPhone 15</h1>
  <p>Giá: 20.000.000 đ</p>
  <p>Còn lại: 5 sản phẩm</p>
</div>

<!-- Khi giá giảm, phải sửa HTML thủ công -->
<div>
  <h1>iPhone 15</h1>
  <p>Giá: 18.000.000 đ</p>  <!-- Phải sửa lại -->
  <p>Còn lại: 3 sản phẩm</p>  <!-- Phải sửa lại -->
</div>
```

**Với Vue Template:**
```vue
<!-- ✅ Chỉ cần thay đổi data, HTML tự động update -->
<template>
  <div>
    <h1>{{ product.name }}</h1>
    <p>Giá: {{ product.price.toLocaleString('vi-VN') }} đ</p>
    <p>Còn lại: {{ product.stock }} sản phẩm</p>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const product = ref({
  name: 'iPhone 15',
  price: 20000000,
  stock: 5
})

// Khi giá giảm, chỉ cần thay đổi data
function applyDiscount() {
  product.value.price = 18000000  // ✅ HTML tự động update!
  product.value.stock = 3         // ✅ HTML tự động update!
}
</script>
```

### Giới thiệu Template

**Template** trong Vue là phần HTML có thể chứa các cú pháp đặc biệt của Vue để:
- Hiển thị dữ liệu động (như ví dụ trên)
- Bind attributes (ảnh sản phẩm, link)
- Xử lý events (click mua hàng, thêm vào giỏ)
- Render có điều kiện (hiển thị "Còn hàng" / "Hết hàng")

**Ví dụ so sánh:**

**HTML thuần:**
```html
<div>
  <h1>Hello World</h1>
  <p>Giá: 100000</p>
</div>
```

**Vue Template:**
```html
<template>
  <div>
    <h1>{{ message }}</h1>
    <p>Giá: {{ price.toLocaleString('vi-VN') }} đ</p>
  </div>
</template>
```

**Khác biệt:**
- HTML: Nội dung tĩnh, không thay đổi
- Vue: Nội dung động, tự động cập nhật khi data thay đổi

### 🌐 Liên hệ thực tế

**Các website bạn dùng hàng ngày đều dùng Template động:**
- **Facebook**: Tên người dùng, số like, comment tự động update
- **YouTube**: Số lượt xem, lượt like thay đổi real-time
- **Shopee**: Giá sản phẩm, số lượng còn lại update liên tục
- **Gmail**: Danh sách email mới tự động xuất hiện

---

## 📝 2. Interpolation ({{ }})

### Cú pháp cơ bản

**Interpolation** dùng để hiển thị giá trị của biến trong template.

```vue
<template>
  <div>
    <p>{{ message }}</p>
    <p>{{ count }}</p>
    <p>{{ user.name }}</p>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const message = ref('Xin chào Vue!')
const count = ref(10)
const user = ref({ name: 'Nguyễn Văn A', age: 20 })
</script>
```

**Kết quả:**
```
Xin chào Vue!
10
Nguyễn Văn A
```

### Biểu thức JavaScript

Bạn có thể dùng **bất kỳ biểu thức JavaScript** nào trong `{{ }}`:

```vue
<template>
  <div>
    <!-- Phép tính -->
    <p>{{ 5 + 3 }}</p>  <!-- 8 -->
    
    <!-- Gọi hàm -->
    <p>{{ message.toUpperCase() }}</p>
    
    <!-- Toán tử 3 ngôi -->
    <p>{{ count > 0 ? 'Có' : 'Không' }}</p>
    
    <!-- Format số -->
    <p>{{ price.toLocaleString('vi-VN') }} đ</p>
    
    <!-- Chuỗi template -->
    <p>{{ `Xin chào ${user.name}` }}</p>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const message = ref('hello')
const count = ref(5)
const price = ref(1000000)
const user = ref({ name: 'Nguyễn Văn A' })
</script>
```

### ⚠️ Lưu ý quan trọng

**KHÔNG được dùng:**
- ❌ Câu lệnh (statements): `{{ if (count > 0) { ... } }}`
- ❌ Khai báo biến: `{{ let x = 5 }}`
- ❌ Vòng lặp: `{{ for (let i = 0; i < 10; i++) { ... } }}`

**CHỈ được dùng:**
- ✅ Biểu thức (expressions): `{{ count + 1 }}`
- ✅ Gọi hàm: `{{ formatPrice(price) }}`
- ✅ Toán tử: `{{ count > 0 ? 'Yes' : 'No' }}`

---

## 🔗 3. v-bind (Binding Attributes)

### Tại sao cần v-bind?

Trong HTML thuần, attributes là tĩnh:
```html
<img src="logo.png">
<a href="/home">Trang chủ</a>
```

Nhưng trong Vue, ta muốn attributes **động** (thay đổi theo data):
```vue
<img :src="imageUrl">
<a :href="homeLink">Trang chủ</a>
```

### Cú pháp

**Cách 1: Shorthand (khuyến nghị)**
```vue
<img :src="imageUrl">
<a :href="url">Link</a>
<button :disabled="isLoading">Gửi</button>
```

**Cách 2: Full syntax**
```vue
<img v-bind:src="imageUrl">
<a v-bind:href="url">Link</a>
```

### Ví dụ thực tế

**Ví dụ 1: Dynamic image**
```vue
<template>
  <div>
    <img :src="productImage" :alt="productName">
  </div>
</template>

<script setup>
import { ref } from 'vue'

const productImage = ref('https://example.com/iphone.jpg')
const productName = ref('iPhone 15')
</script>
```

**Ví dụ 2: Dynamic class**
```vue
<template>
  <div>
    <button :class="{ active: isActive, disabled: isLoading }">
      Click me
    </button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const isActive = ref(true)
const isLoading = ref(false)
</script>
```

**Kết quả:** `<button class="active">Click me</button>`

**Ví dụ 3: Dynamic style**
```vue
<template>
  <div>
    <div :style="{ color: textColor, fontSize: fontSize + 'px' }">
      Text động
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const textColor = ref('red')
const fontSize = ref(20)
</script>
```

---

## 🖱️ 4. v-on (Event Handling)

### Tại sao cần v-on?

Trong JavaScript thuần:
```html
<button onclick="handleClick()">Click</button>
```

Trong Vue, ta dùng `v-on` (hoặc `@`) để xử lý events:

```vue
<button @click="handleClick">Click</button>
```

### Cú pháp

**Cách 1: Shorthand (khuyến nghị)**
```vue
<button @click="handleClick">Click</button>
<input @input="handleInput">
<form @submit="handleSubmit">
```

**Cách 2: Full syntax**
```vue
<button v-on:click="handleClick">Click</button>
```

### Các events phổ biến

```vue
<template>
  <div>
    <!-- Click -->
    <button @click="handleClick">Click me</button>
    
    <!-- Input -->
    <input @input="handleInput" placeholder="Nhập text">
    
    <!-- Change -->
    <select @change="handleChange">
      <option value="1">Option 1</option>
    </select>
    
    <!-- Submit -->
    <form @submit.prevent="handleSubmit">
      <button type="submit">Gửi</button>
    </form>
    
    <!-- Key events -->
    <input @keyup.enter="handleEnter">
    <input @keydown.esc="handleEsc">
    
    <!-- Mouse events -->
    <div @mouseenter="handleMouseEnter"
         @mouseleave="handleMouseLeave">
      Hover me
    </div>
  </div>
</template>

<script setup>
function handleClick() {
  console.log('Clicked!')
}

function handleInput(event) {
  console.log('Input:', event.target.value)
}

function handleChange(event) {
  console.log('Changed:', event.target.value)
}

function handleSubmit(event) {
  event.preventDefault()
  console.log('Submitted!')
}

function handleEnter() {
  console.log('Enter pressed')
}
</script>
```

### Event Modifiers

Vue cung cấp **modifiers** để xử lý events dễ dàng hơn:

```vue
<template>
  <!-- .prevent - Ngăn default behavior -->
  <form @submit.prevent="handleSubmit">
    <!-- Tự động gọi event.preventDefault() -->
  </form>
  
  <!-- .stop - Ngăn event bubbling -->
  <div @click="handleParentClick">
    <button @click.stop="handleButtonClick">
      Click (không trigger parent)
    </button>
  </div>
  
  <!-- .once - Chỉ chạy 1 lần -->
  <button @click.once="handleOnce">Click once</button>
  
  <!-- .self - Chỉ chạy khi click chính element -->
  <div @click.self="handleSelf">
    <p>Click vào p không trigger</p>
  </div>
</template>
```

### Truyền tham số

```vue
<template>
  <div>
    <!-- Không có tham số -->
    <button @click="handleClick">Click</button>
    
    <!-- Có tham số -->
    <button @click="handleClick('Hello')">Click với tham số</button>
    
    <!-- Truyền event object -->
    <button @click="handleClick($event)">Click với event</button>
    
    <!-- Truyền nhiều tham số -->
    <button @click="handleClick('Hello', 123, $event)">
      Click với nhiều tham số
    </button>
  </div>
</template>

<script setup>
function handleClick(message, number, event) {
  console.log(message, number, event)
}
</script>
```

---

## 🎯 5. So sánh JavaScript thuần vs Vue

### Ví dụ: Counter

**JavaScript thuần:**
```html
<!DOCTYPE html>
<html>
<body>
  <p id="count">0</p>
  <button onclick="increment()">Tăng</button>
  
  <script>
    let count = 0
    
    function increment() {
      count++
      document.getElementById('count').textContent = count
    }
  </script>
</body>
</html>
```

**Vue:**
```vue
<template>
  <div>
    <p>{{ count }}</p>
    <button @click="increment">Tăng</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}
</script>
```

**Khác biệt:**
- JavaScript: Phải tự select element và update DOM
- Vue: Tự động update DOM khi data thay đổi (reactive)

---

## 🧪 6. Thực hành

### Bài tập 1: Hiển thị thông tin
Tạo component hiển thị:
- Tên: "Nguyễn Văn A"
- Tuổi: 20
- Email: "a@example.com"
- Số điện thoại: "0123456789"

### Bài tập 2: Dynamic Image
- Tạo biến `imageUrl`
- Hiển thị ảnh với URL động
- Thêm alt text động

### Bài tập 3: Button Counter
- Tạo nút "Click me"
- Mỗi lần click tăng biến `count` lên 1
- Hiển thị số lần click

### Bài tập 4: Input Display
- Tạo input text
- Hiển thị giá trị input ngay bên dưới
- Format: "Bạn đã nhập: [giá trị]"

---

## 🎯 6. Case Study: Xây dựng Product Card Component

### Mô tả tình huống

Xây dựng component hiển thị thông tin sản phẩm cho website bán hàng, tương tự như **Shopee** hoặc **Tiki**.

### Yêu cầu

- Hiển thị ảnh sản phẩm (dynamic) - dùng `v-bind`
- Hiển thị tên sản phẩm - dùng `{{ }}`
- Hiển thị giá (format tiền Việt Nam) - dùng `{{ }}`
- Hiển thị giá cũ (nếu có)
- Nút "Mua ngay" có thể click - dùng `v-on`
- Disable nút khi hết hàng - dùng `v-bind`

**Lưu ý:** Trong bài này, chúng ta chỉ sử dụng các kiến thức đã học:
- `{{ }}` (Interpolation)
- `v-bind` hoặc `:`
- `v-on` hoặc `@`

Các tính năng như `v-if` (hiển thị có điều kiện) và `v-for` (render danh sách) sẽ được học ở các bài sau.

### Implementation

```vue
<template>
  <div class="product-card">
    <!-- Ảnh sản phẩm - dùng v-bind -->
    <img 
      :src="productImage" 
      :alt="productName"
      class="product-image"
      @error="handleImageError"
    />
    
    <!-- Tên sản phẩm - dùng interpolation -->
    <h3 class="product-name">{{ productName }}</h3>
    
    <!-- Giá - dùng interpolation với format -->
    <div class="price-section">
      <p class="current-price">{{ formatPrice(currentPrice) }} đ</p>
      <p v-if="oldPrice" class="old-price">{{ formatPrice(oldPrice) }} đ</p>
    </div>
    
    <!-- Đánh giá - dùng interpolation -->
    <div class="rating">
      <span>⭐ {{ rating }}</span>
      <span>({{ reviewCount }} đánh giá)</span>
    </div>
    
    <!-- Nút Mua ngay - dùng v-on và v-bind -->
    <button 
      class="buy-button"
      :disabled="!inStock"
      @click="handleBuy"
    >
      {{ inStock ? 'Mua ngay' : 'Hết hàng' }}
    </button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

// Dữ liệu sản phẩm
const productName = ref('iPhone 15 Pro Max')
const currentPrice = ref(30000000)
const oldPrice = ref(35000000)
const productImage = ref('https://example.com/iphone15.jpg')
const rating = ref(4.8)
const reviewCount = ref(1250)
const inStock = ref(true)

// Hàm format giá
function formatPrice(price) {
  return price.toLocaleString('vi-VN')
}

// Xử lý khi click nút Mua ngay
function handleBuy() {
  if (inStock.value) {
    console.log('Mua sản phẩm:', productName.value)
    alert(`Đã thêm ${productName.value} vào giỏ hàng!`)
    // Logic thêm vào giỏ hàng sẽ học ở bài sau
  }
}

// Xử lý khi ảnh lỗi
function handleImageError(event) {
  // Nếu ảnh lỗi, dùng ảnh placeholder
  event.target.src = '/placeholder.jpg'
}

// Ví dụ: Cập nhật giá khi có sale
function applySale() {
  currentPrice.value = 25000000  // ✅ Vue tự động update UI!
}
</script>

<style scoped>
.product-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 16px;
  position: relative;
  transition: transform 0.2s;
  max-width: 300px;
}

.product-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 4px 12px rgba(0,0,0,0.1);
}

.product-image {
  width: 100%;
  height: 200px;
  object-fit: cover;
  border-radius: 4px;
}

.product-name {
  margin: 12px 0;
  font-size: 1.2em;
}

.current-price {
  font-size: 1.5em;
  color: #e74c3c;
  font-weight: bold;
  margin: 8px 0;
}

.old-price {
  text-decoration: line-through;
  color: #999;
  font-size: 0.9em;
  margin: 4px 0;
}

.rating {
  margin: 8px 0;
  color: #666;
}

.buy-button {
  width: 100%;
  padding: 12px;
  background: #3498db;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  margin-top: 12px;
}

.buy-button:hover {
  background: #2980b9;
}

.buy-button:disabled {
  background: #95a5a6;
  cursor: not-allowed;
}
</style>
```

**Giải thích:**
- **v-bind (:src, :alt)**: Bind ảnh động, tự động update khi `productImage` thay đổi
- **Interpolation ({{ }})**: Hiển thị tên, giá, đánh giá - tự động format
- **v-on (@click)**: Xử lý sự kiện click "Mua ngay"
- **v-bind (:disabled)**: Disable nút khi `inStock = false`
- **v-on (@error)**: Xử lý khi ảnh load lỗi

**Ví dụ sử dụng:**
```vue
<template>
  <div>
    <!-- Hiển thị 1 sản phẩm -->
    <ProductCard />
    
    <!-- Có thể thay đổi data và UI tự động update -->
    <button @click="changePrice">Giảm giá</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

// Ví dụ: Thay đổi giá
function changePrice() {
  // Logic này sẽ được học ở bài sau về reactivity
  // Hiện tại chỉ cần biết: khi data thay đổi, UI tự động update
}
</script>
```

**Lưu ý quan trọng:**
- Trong bài này, chúng ta chỉ hiển thị **1 sản phẩm**
- Để hiển thị **nhiều sản phẩm**, chúng ta sẽ học `v-for` ở **Bài 6**
- Để **hiển thị có điều kiện** (như badge "SALE"), chúng ta sẽ học `v-if` ở **Bài 5**

---

## ❌ 7. Các lỗi thường gặp

### Lỗi 1: Quên .value với ref()

**❌ Vấn đề:**
```vue
<script setup>
const count = ref(0)
function increment() {
  count++ // ❌ SAI
}
</script>
```

**✅ Giải pháp:**
```vue
<script setup>
const count = ref(0)
function increment() {
  count.value++ // ✅ ĐÚNG
}
</script>
```

**🔍 Giải thích:** `ref()` trả về object wrapper, phải dùng `.value` trong script.

---

### Lỗi 2: Dùng .value trong template

**❌ Vấn đề:**
```vue
<template>
  <p>{{ count.value }}</p> <!-- ❌ SAI -->
</template>
```

**✅ Giải pháp:**
```vue
<template>
  <p>{{ count }}</p> <!-- ✅ ĐÚNG - Vue tự động unwrap -->
</template>
```

---

### Lỗi 3: Dùng câu lệnh trong {{ }}

**❌ Vấn đề:**
```vue
<template>
  <p>{{ if (count > 0) { return 'Có' } }}</p> <!-- ❌ SAI -->
</template>
```

**✅ Giải pháp:**
```vue
<template>
  <p>{{ count > 0 ? 'Có' : 'Không' }}</p> <!-- ✅ ĐÚNG -->
</template>
```

---

### Lỗi 4: Quên import ref

**❌ Vấn đề:**
```vue
<script setup>
const count = ref(0) // ❌ Lỗi: ref is not defined
</script>
```

**✅ Giải pháp:**
```vue
<script setup>
import { ref } from 'vue' // ✅ Phải import

const count = ref(0)
</script>
```

---

## 💡 8. Best Practices

### 1. Dùng shorthand syntax
```vue
<!-- ✅ Tốt -->
<img :src="url" @click="handleClick">

<!-- ❌ Tránh -->
<img v-bind:src="url" v-on:click="handleClick">
```

### 2. Tránh logic phức tạp trong template
```vue
<!-- ❌ Tránh -->
<p>{{ users.filter(u => u.age > 18).map(u => u.name).join(', ') }}</p>

<!-- ✅ Tốt -->
<p>{{ adultUserNames }}</p>

<script setup>
const adultUserNames = computed(() => {
  return users.value
    .filter(u => u.age > 18)
    .map(u => u.name)
    .join(', ')
})
</script>
```

### 3. Dùng computed cho tính toán
```vue
<!-- ❌ Tránh -->
<p>{{ price * quantity * (1 - discount) }}</p>

<!-- ✅ Tốt -->
<p>{{ totalPrice }}</p>

<script setup>
const totalPrice = computed(() => {
  return price.value * quantity.value * (1 - discount.value)
})
</script>
```

---

## 🧪 9. Mini Test

### Câu 1: Interpolation dùng để làm gì?
<details>
<summary>Xem đáp án</summary>
Hiển thị giá trị của biến hoặc biểu thức JavaScript trong template.
</details>

### Câu 2: v-bind dùng khi nào?
<details>
<summary>Xem đáp án</summary>
Khi muốn attributes của HTML element thay đổi theo data (dynamic attributes).
</details>

### Câu 3: v-on dùng khi nào?
<details>
<summary>Xem đáp án</summary>
Khi muốn xử lý events (click, input, submit, etc).
</details>

### Câu 4: Có thể dùng câu lệnh trong {{ }} không?
<details>
<summary>Xem đáp án</summary>
Không, chỉ được dùng biểu thức (expressions), không được dùng statements.
</details>

### Câu 5: .prevent modifier làm gì?
<details>
<summary>Xem đáp án</summary>
Tự động gọi event.preventDefault() để ngăn default behavior.
</details>

---

## 📌 10. Quick Notes

### Interpolation
```vue
{{ variable }}
{{ expression }}
{{ functionCall() }}
```

### v-bind (Shorthand: :)
```vue
<img :src="url">
<button :disabled="isLoading">
<div :class="{ active: isActive }">
```

### v-on (Shorthand: @)
```vue
<button @click="handleClick">
<input @input="handleInput">
<form @submit.prevent="handleSubmit">
```

### Event Modifiers
- `.prevent` - preventDefault()
- `.stop` - stopPropagation()
- `.once` - Chỉ chạy 1 lần
- `.self` - Chỉ khi click chính element

---

**👉 Bài tiếp theo: [04_reactivity_ref.md](./04_reactivity_ref.md) - Reactivity với ref()**

