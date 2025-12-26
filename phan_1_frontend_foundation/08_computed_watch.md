# 🟦 BÀI 8: COMPUTED VÀ WATCH

## 🎯 Mục tiêu
- Hiểu computed là gì và khi nào dùng
- Hiểu watch là gì và khi nào dùng
- So sánh computed vs watch vs methods
- Thực hành với các ví dụ thực tế

---

## 🧠 1. Computed là gì?

### 🎬 Ví dụ dẫn nhập: Giỏ hàng trên Shopee

Hãy tưởng tượng bạn đang xây dựng trang **giỏ hàng** cho website bán hàng:

**Tình huống thực tế:**
- User có 5 sản phẩm trong giỏ
- Mỗi sản phẩm có: giá, số lượng
- Cần tính: Tổng tiền, Giảm giá (10%), Phí ship (30.000đ), Thành tiền
- Khi user thay đổi số lượng → Tất cả phải tự động tính lại

**Vấn đề với Methods:**

**Ví dụ dùng methods:**
```vue
<template>
  <div class="cart">
    <h2>Giỏ hàng</h2>
    
    <div v-for="item in cart" :key="item.id">
      <p>{{ item.name }} - {{ item.price }} đ x {{ item.quantity }}</p>
    </div>
    
    <div class="summary">
      <p>Tổng tiền: {{ calculateTotal() }} đ</p>
      <p>Giảm giá (10%): {{ calculateDiscount() }} đ</p>
      <p>Phí ship: {{ shippingFee }} đ</p>
      <p>Thành tiền: {{ calculateFinalTotal() }} đ</p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const cart = ref([
  { id: 1, name: 'iPhone 15', price: 20000000, quantity: 1 },
  { id: 2, name: 'Samsung S24', price: 18000000, quantity: 2 }
])

const shippingFee = 30000

function calculateTotal() {
  console.log('calculateTotal chạy')  // ⚠️ Chạy mỗi lần render
  return cart.value.reduce((sum, item) => {
    return sum + (item.price * item.quantity)
  }, 0)
}

function calculateDiscount() {
  console.log('calculateDiscount chạy')  // ⚠️ Chạy mỗi lần render
  return calculateTotal() * 0.1  // ⚠️ Gọi calculateTotal() lại
}

function calculateFinalTotal() {
  console.log('calculateFinalTotal chạy')  // ⚠️ Chạy mỗi lần render
  return calculateTotal() - calculateDiscount() + shippingFee  // ⚠️ Gọi lại 2 lần
}
</script>
```

**Vấn đề:**
- Mỗi lần render → Tất cả methods chạy lại
- `calculateTotal()` chạy **3 lần** (từ 3 methods khác nhau)
- `calculateDiscount()` chạy **2 lần** (từ chính nó và calculateFinalTotal)
- Performance kém, đặc biệt khi có nhiều sản phẩm
- Console log sẽ thấy: "calculateTotal chạy" xuất hiện 3 lần mỗi lần render!

**Giải pháp: Computed**

**Với Computed:**
```vue
<template>
  <div class="cart">
    <h2>Giỏ hàng</h2>
    
    <div v-for="item in cart" :key="item.id">
      <p>{{ item.name }} - {{ item.price }} đ x {{ item.quantity }}</p>
    </div>
    
    <div class="summary">
      <p>Tổng tiền: {{ totalPrice.toLocaleString('vi-VN') }} đ</p>
      <p>Giảm giá (10%): {{ discount.toLocaleString('vi-VN') }} đ</p>
      <p>Phí ship: {{ shippingFee.toLocaleString('vi-VN') }} đ</p>
      <p>Thành tiền: {{ finalPrice.toLocaleString('vi-VN') }} đ</p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const cart = ref([
  { id: 1, name: 'iPhone 15', price: 20000000, quantity: 1 },
  { id: 2, name: 'Samsung S24', price: 18000000, quantity: 2 }
])

const shippingFee = 30000

const totalPrice = computed(() => {
  console.log('totalPrice tính toán')  // ✅ Chỉ chạy khi cart thay đổi
  return cart.value.reduce((sum, item) => {
    return sum + (item.price * item.quantity)
  }, 0)
})

const discount = computed(() => {
  console.log('discount tính toán')  // ✅ Chỉ chạy khi totalPrice thay đổi
  return totalPrice.value * 0.1
})

const finalPrice = computed(() => {
  console.log('finalPrice tính toán')  // ✅ Chỉ chạy khi dependencies thay đổi
  return totalPrice.value - discount.value + shippingFee
})
</script>
```

**Ưu điểm:**
- ✅ Chỉ tính lại khi `cart` thay đổi (không phải mỗi lần render)
- ✅ Tự động cache kết quả
- ✅ `totalPrice` chỉ tính 1 lần, `discount` và `finalPrice` dùng kết quả đã cache
- ✅ Performance tốt hơn nhiều

### 🌐 Liên hệ thực tế

**Computed được dùng ở mọi nơi:**
- **Shopee**: Tính tổng tiền giỏ hàng, tính phí ship, tính giảm giá
- **Banking App**: Tính lãi suất, tính số dư sau giao dịch
- **E-commerce**: Tính tổng đơn hàng, tính điểm tích lũy
- **Calculator App**: Tất cả các phép tính đều dùng computed

**Tất cả đều dùng Computed để tính toán hiệu quả!**

### Giải pháp: Computed

**Computed** = Giá trị tính toán được **cache**, chỉ tính lại khi dependencies thay đổi

```vue
<template>
  <div>
    <p>Giá: {{ price }}</p>
    <p>Số lượng: {{ quantity }}</p>
    <p>Tổng: {{ total }}</p>
    <p>Thuế (10%): {{ tax }}</p>
    <p>Tổng cộng: {{ totalWithTax }}</p>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const price = ref(100000)
const quantity = ref(2)

const total = computed(() => {
  return price.value * quantity.value
})

const tax = computed(() => {
  return total.value * 0.1
})

const totalWithTax = computed(() => {
  return total.value + tax.value
})
</script>
```

**Ưu điểm:**
- ✅ Chỉ tính lại khi `price` hoặc `quantity` thay đổi
- ✅ Tự động cache kết quả
- ✅ Performance tốt hơn

---

## 📊 2. Computed Properties

### Cú pháp cơ bản

```vue
<script setup>
import { ref, computed } from 'vue'

const count = ref(0)

const doubleCount = computed(() => {
  return count.value * 2
})
</script>

<template>
  <p>{{ count }} x 2 = {{ doubleCount }}</p>
</template>
```

### Computed với nhiều dependencies

```vue
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('Nguyễn')
const lastName = ref('Văn A')

const fullName = computed(() => {
  return `${firstName.value} ${lastName.value}`
})
</script>

<template>
  <p>Tên đầy đủ: {{ fullName }}</p>
</template>
```

### Computed với filter

```vue
<template>
  <div>
    <input v-model="search" placeholder="Tìm kiếm">
    <ul>
      <li v-for="item in filteredItems" :key="item.id">
        {{ item.name }}
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const items = ref([
  { id: 1, name: 'Apple' },
  { id: 2, name: 'Banana' },
  { id: 3, name: 'Orange' }
])

const search = ref('')

const filteredItems = computed(() => {
  if (!search.value) {
    return items.value
  }
  return items.value.filter(item =>
    item.name.toLowerCase().includes(search.value.toLowerCase())
  )
})
</script>
```

---

## 👀 3. Watch là gì?

### Khi nào dùng Watch?

**Watch** = Theo dõi thay đổi và thực hiện side effects (gọi API, log, v.v.)

**Dùng watch khi:**
- Cần thực hiện side effects (API call, logging)
- Cần xử lý async
- Cần watch nhiều giá trị cùng lúc

**Dùng computed khi:**
- Cần tính toán từ data khác
- Cần giá trị để hiển thị
- Cần cache kết quả

### Cú pháp cơ bản

```vue
<script setup>
import { ref, watch } from 'vue'

const count = ref(0)

watch(count, (newValue, oldValue) => {
  console.log(`Count thay đổi từ ${oldValue} sang ${newValue}`)
})
</script>
```

### Watch với immediate

```vue
<script setup>
import { ref, watch } from 'vue'

const search = ref('')

watch(search, (newValue) => {
  console.log('Search:', newValue)
  // Gọi API search...
}, { immediate: true })  // ✅ Chạy ngay khi mount
</script>
```

### Watch với deep

```vue
<script setup>
import { ref, watch } from 'vue'

const user = ref({
  name: 'Nguyễn Văn A',
  address: {
    city: 'Đà Nẵng'
  }
})

watch(user, (newValue) => {
  console.log('User thay đổi:', newValue)
}, { deep: true })  // ✅ Theo dõi nested properties
</script>
```

### Watch nhiều giá trị

```vue
<script setup>
import { ref, watch } from 'vue'

const firstName = ref('Nguyễn')
const lastName = ref('Văn A')

watch([firstName, lastName], ([newFirst, newLast], [oldFirst, oldLast]) => {
  console.log('Tên thay đổi:', newFirst, newLast)
})
</script>
```

---

## ⚖️ 4. So sánh: Computed vs Watch vs Methods

| Đặc điểm | Computed | Watch | Methods |
|----------|---------|-------|---------|
| **Mục đích** | Tính toán giá trị | Side effects | Thực hiện hành động |
| **Cache** | ✅ Có | ❌ Không | ❌ Không |
| **Return value** | ✅ Có | ❌ Không | ✅ Có (tùy) |
| **Khi nào chạy** | Khi dependency thay đổi | Khi giá trị thay đổi | Mỗi lần gọi |
| **Dùng trong template** | ✅ Như property | ❌ Không | ✅ Như function |

### Ví dụ so sánh

**Computed:**
```vue
<template>
  <p>{{ fullName }}</p>  <!-- Không cần () -->
</template>

<script setup>
const fullName = computed(() => firstName.value + ' ' + lastName.value)
</script>
```

**Methods:**
```vue
<template>
  <p>{{ getFullName() }}</p>  <!-- Phải có () -->
</template>

<script setup>
function getFullName() {
  return firstName.value + ' ' + lastName.value
}
</script>
```

**Watch:**
```vue
<script setup>
watch(fullName, (newValue) => {
  console.log('Full name thay đổi:', newValue)
})
</script>
```

---

## 💻 5. Ví dụ thực tế

### Ví dụ 1: Shopping Cart với Computed

```vue
<template>
  <div>
    <h2>Giỏ hàng</h2>
    <div v-for="item in cart" :key="item.id">
      <p>{{ item.name }} - {{ item.price }} đ x {{ item.quantity }}</p>
    </div>
    
    <div class="summary">
      <p>Tổng tiền: {{ totalPrice.toLocaleString('vi-VN') }} đ</p>
      <p>Giảm giá (10%): {{ discount.toLocaleString('vi-VN') }} đ</p>
      <p>Thành tiền: {{ finalPrice.toLocaleString('vi-VN') }} đ</p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const cart = ref([
  { id: 1, name: 'iPhone', price: 20000000, quantity: 1 },
  { id: 2, name: 'Samsung', price: 18000000, quantity: 2 }
])

const totalPrice = computed(() => {
  return cart.value.reduce((sum, item) => {
    return sum + (item.price * item.quantity)
  }, 0)
})

const discount = computed(() => {
  return totalPrice.value * 0.1
})

const finalPrice = computed(() => {
  return totalPrice.value - discount.value
})
</script>
```

### Ví dụ 2: Search với Watch

```vue
<template>
  <div>
    <input v-model="searchQuery" placeholder="Tìm kiếm sản phẩm">
    <div v-if="loading">Đang tải...</div>
    <div v-else>
      <div v-for="product in results" :key="product.id">
        {{ product.name }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'

const searchQuery = ref('')
const results = ref([])
const loading = ref(false)

watch(searchQuery, async (newQuery) => {
  if (!newQuery) {
    results.value = []
    return
  }
  
  loading.value = true
  try {
    // Giả lập API call
    await new Promise(resolve => setTimeout(resolve, 500))
    results.value = [
      { id: 1, name: `${newQuery} - Sản phẩm 1` },
      { id: 2, name: `${newQuery} - Sản phẩm 2` }
    ]
  } finally {
    loading.value = false
  }
})
</script>
```

### Ví dụ 3: Form Validation với Computed

```vue
<template>
  <form @submit.prevent="handleSubmit">
    <input v-model="form.email" type="email" placeholder="Email">
    <span v-if="!isEmailValid" class="error">Email không hợp lệ</span>
    
    <input v-model="form.password" type="password" placeholder="Mật khẩu">
    <span v-if="!isPasswordValid" class="error">
      Mật khẩu phải có ít nhất 6 ký tự
    </span>
    
    <input v-model="form.confirmPassword" type="password" placeholder="Xác nhận">
    <span v-if="!isPasswordMatch" class="error">Mật khẩu không khớp</span>
    
    <button type="submit" :disabled="!isFormValid">Đăng ký</button>
  </form>
</template>

<script setup>
import { ref, computed } from 'vue'

const form = ref({
  email: '',
  password: '',
  confirmPassword: ''
})

const isEmailValid = computed(() => {
  return form.value.email.includes('@') && form.value.email.includes('.')
})

const isPasswordValid = computed(() => {
  return form.value.password.length >= 6
})

const isPasswordMatch = computed(() => {
  return form.value.password === form.value.confirmPassword
})

const isFormValid = computed(() => {
  return isEmailValid.value && 
         isPasswordValid.value && 
         isPasswordMatch.value
})

function handleSubmit() {
  if (isFormValid.value) {
    console.log('Submit:', form.value)
  }
}
</script>
```

---

## ⚠️ 6. Các lỗi thường gặp

### Lỗi 1: Dùng methods thay computed

**❌ Vấn đề:**
```vue
<template>
  <p>{{ calculateTotal() }}</p>  <!-- Chạy mỗi lần render -->
</template>
```

**✅ Giải pháp:**
```vue
<template>
  <p>{{ total }}</p>  <!-- Chỉ tính khi cần -->
</template>

<script setup>
const total = computed(() => price.value * quantity.value)
</script>
```

---

### Lỗi 2: Mutate trong computed

**❌ Vấn đề:**
```vue
<script setup>
const items = computed(() => {
  items.value.push('new')  // ❌ Không được mutate
  return items.value
})
</script>
```

**✅ Giải pháp:**
```vue
<script setup>
const items = computed(() => {
  return [...originalItems.value, 'new']  // ✅ Tạo array mới
})
</script>
```

---

### Lỗi 3: Watch không deep cho object

**❌ Vấn đề:**
```vue
<script setup>
const user = ref({ name: 'A', age: 20 })

watch(user, () => {
  // ❌ Không trigger khi user.name thay đổi
})
</script>
```

**✅ Giải pháp:**
```vue
<script setup>
watch(user, () => {
  // ...
}, { deep: true })  // ✅ Theo dõi nested
</script>
```

---

## 💡 7. Best Practices

### 1. Dùng computed cho tính toán

```vue
<!-- ✅ Tốt -->
<p>{{ totalPrice }}</p>

<script setup>
const totalPrice = computed(() => {
  return items.value.reduce((sum, item) => sum + item.price, 0)
})
</script>
```

### 2. Dùng watch cho side effects

```vue
<script setup>
watch(searchQuery, async (newQuery) => {
  // ✅ Side effect: Gọi API
  const results = await searchAPI(newQuery)
  searchResults.value = results
})
</script>
```

### 3. Tránh computed phức tạp

```vue
<!-- ❌ Tránh -->
<p>{{ items.filter(i => i.active).map(i => i.price).reduce((a, b) => a + b, 0) }}</p>

<!-- ✅ Tốt -->
<p>{{ totalActivePrice }}</p>

<script setup>
const totalActivePrice = computed(() => {
  return items.value
    .filter(i => i.active)
    .map(i => i.price)
    .reduce((a, b) => a + b, 0)
})
</script>
```

---

## 🧪 8. Thực hành

### Bài tập 1: Calculator
Tạo calculator:
- Input số 1, số 2
- Computed: Tổng, Hiệu, Tích, Thương
- Hiển thị kết quả tự động

### Bài tập 2: Search với debounce
Tạo search:
- Input search
- Watch với debounce (chờ 500ms)
- Gọi API search (giả lập)

### Bài tập 3: Form validation
Tạo form:
- Email, Password, Confirm Password
- Computed validation cho từng field
- Computed isFormValid

---

## 🧪 9. Mini Test

### Câu 1: Computed khác Methods như thế nào?
<details>
<summary>Xem đáp án</summary>
Computed có cache, chỉ tính lại khi dependency thay đổi. Methods chạy mỗi lần gọi.
</details>

### Câu 2: Khi nào dùng Watch?
<details>
<summary>Xem đáp án</summary>
Khi cần thực hiện side effects (API call, logging) khi giá trị thay đổi.
</details>

### Câu 3: Computed có thể mutate data không?
<details>
<summary>Xem đáp án</summary>
Không, computed chỉ nên return giá trị tính toán, không nên có side effects.
</details>

### Câu 4: Watch deep dùng khi nào?
<details>
<summary>Xem đáp án</summary>
Khi cần theo dõi thay đổi của nested properties trong object.
</details>

---

## 📌 10. Quick Notes

### Computed
```javascript
const total = computed(() => {
  return price.value * quantity.value
})
```

### Watch
```javascript
watch(variable, (newVal, oldVal) => {
  // Side effects
}, { immediate: true, deep: true })
```

### So sánh
- **Computed**: Tính toán, có cache
- **Watch**: Side effects, không cache
- **Methods**: Hành động, không cache

---

**👉 Bài tiếp theo: [09_components.md](./09_components.md) - Components cơ bản**

