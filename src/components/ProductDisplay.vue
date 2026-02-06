<template>
  <div v-if="product" :class="['card', categoryClass]">
    <div class="card-image">
      <img :src="product.image" :alt="product.title">
    </div>
    <div class="card-details">
      <h1>{{ product.title }}</h1>
      <div class="meta-info">
        <span class="category">{{ product.category }}</span>
        <div class="rating-container">
          <span class="rating-score">{{ product.rating.rate }}/5</span>
          <span 
            v-for="n in 5" 
            :key="n"
            :class="['circle', n <= Math.round(product.rating.rate) ? 'filled' : 'empty']"
          ></span>
        </div>
      </div>

      <p class="description">
        {{ product.description }}
      </p>

      <div class="price-section">
        <div class="price">${{ product.price }}</div>
      </div>

      <div class="buttons">
        <button class="btn btn-buy">Buy now</button>
        <button class="btn btn-next" @click="nextProduct">Next product</button>
      </div>
    </div>
  </div>

  <div v-else-if="showUnavailable" class="card unavailable">
    
    <div class="eyebrow left"></div>
    <div class="eyebrow right"></div>
    <div class="eye left"></div>
    <div class="eye right"></div>
    <div class="mouth"></div>

    <div class="unavailable-content">
      <p class="unavailable-message">This product is unavailable to show</p>
      <button class="btn btn-next unavailable-btn" @click="nextProduct">Next product</button>
    </div>
  </div>

  <div v-else-if="loading" class="card">
    <div class="loading">Loading...</div>
  </div>
</template>

<script>
export default {
  name: 'ProductDisplay',
  data() {
    return {
      product: null,
      loading: false,
      currentId: 1,
      showUnavailable: false,
      categoryClass: ''
    }
  },
  methods: {
    async fetchProduct(id) {
      this.loading = true
      this.product = null
      this.showUnavailable = false
      
      try {
        const response = await fetch(`https://fakestoreapi.com/products/${id}`)
        
        if (!response.ok) {
          throw new Error('Failed to fetch product')
        }
        
        const data = await response.json()
        
        if (data.category === "men's clothing" || data.category === "women's clothing") {
          this.product = data
          this.updateCategoryClass(data.category)
        } else {
          this.showUnavailable = true
          this.updateCategoryClass('unavailable')
        }
        
      } catch (err) {
        console.error('Error fetching product:', err)
        this.showUnavailable = true
        this.updateCategoryClass('unavailable')
      } finally {
        this.loading = false
      }
    },
    
    updateCategoryClass(category) {
      if (category === "men's clothing") {
        this.categoryClass = 'men'
        document.body.className = 'men'
      } else if (category === "women's clothing") {
        this.categoryClass = 'women'
        document.body.className = 'women'
      } else {
        this.categoryClass = 'unavailable'
        document.body.className = 'unavailable'
      }
    },
    
    nextProduct() {
      this.currentId = this.currentId >= 20 ? 1 : this.currentId + 1
      this.fetchProduct(this.currentId)
    }
  },
  mounted() {
    this.fetchProduct(this.currentId)
  }
}
</script>