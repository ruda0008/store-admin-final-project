<template>
  <div class="list-container">
    <div class="header-row">
      <h1>Product Inventory</h1>
      <router-link to="/product/add">
        <button class="button">Add Product</button>
      </router-link>
    </div>
    
    <div class="product-list">
      <table>
        <thead>
          <tr>
            <th>ID</th>
            <th>Product Name</th>
            <th style="width: 50%;">Description</th>
            <th>Price</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="product in products" :key="product.id">
            <td><router-link :to="`/product/${product.id}`"><strong>{{ product.id }}</strong></router-link></td>
            <td><router-link :to="`/product/${product.id}`">{{ product.name }}</router-link></td>
            <td class="desc-cell">{{ product.description }}</td>
            <td class="price-cell">${{ product.price }}</td>
            <td>
               <router-link :to="`/product/${product.id}/edit`" style="font-size:12px;">Edit</router-link>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'ProductList',
    props: ['products'],
    mounted() {
      this.$emit('getProducts')
    }
  }
</script>

<style scoped>
.list-container {
  text-align: left;
}

.header-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  border-bottom: 1px solid #c5cbd5;
  padding-bottom: 15px;
}

h1 {
  margin: 0;
}

.desc-cell {
  color: #555;
  font-size: 13px;
}

.price-cell {
  font-weight: bold;
}

.product-list {
  overflow-x: auto;
}
</style>