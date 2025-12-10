<template>
  <div class="product-page-container">
    <div class="action-bar">
      <router-link to="/products" class="back-link">&larr; Back to Products</router-link>
      <router-link :to="`/product/${this.$route.params.id}/edit`">
        <button class="button">Edit Product</button>
      </router-link>
    </div>

    <div class="product-detail-card" v-if="productExists">
      <div class="product-image-col">
        <div class="image-wrapper">
          <img :src="product.image" alt="Product Image" onerror="this.src='/placeholder.png'">
        </div>
      </div>
      
      <div class="product-info-col">
        <h1 class="product-title">{{ product.name }}</h1>
        <div class="product-id">Model: {{ product.id }}</div>
        
        <div class="price-block">
          ${{ product.price }}
        </div>
        
        <div class="description-block">
          <h3>Overview</h3>
          <p>{{ product.description }}</p>
        </div>
      </div>
    </div>
    
    <div class="not-found" v-else>
      <h3>Product not found</h3>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'ProductDetail',
    props: ['products'],
    computed: {
      product() {
        return this.products.find(product => product.id == this.$route.params.id)
      },
      productExists() {
        return !!this.product
      }
    },
  }
</script>

<style scoped>
.product-page-container {
  text-align: left;
}

.action-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.back-link {
  color: #0046be;
  font-size: 14px;
}

.product-detail-card {
  display: flex;
  background: white;
  padding: 30px;
  border-radius: 4px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
  gap: 40px;
}

.product-image-col {
  flex: 1;
  max-width: 400px;
}

.image-wrapper {
  border: 1px solid #e0e6ef;
  padding: 20px;
  border-radius: 4px;
  text-align: center;
}

.image-wrapper img {
  max-width: 100%;
  height: auto;
}

.product-info-col {
  flex: 2;
}

.product-title {
  font-size: 24px;
  font-weight: 700;
  margin: 0 0 5px 0;
}

.product-id {
  color: #555;
  font-size: 12px;
  margin-bottom: 20px;
}

.price-block {
  font-size: 28px;
  font-weight: 800;
  color: #1d252c;
  margin-bottom: 30px;
}

.description-block h3 {
  font-size: 16px;
  border-bottom: 1px solid #c5cbd5;
  padding-bottom: 10px;
  margin-bottom: 15px;
}

.description-block p {
  line-height: 1.6;
  color: #333;
}

@media (max-width: 768px) {
  .product-detail-card {
    flex-direction: column;
  }
}
</style>