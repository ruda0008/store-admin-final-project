<template>
  <TopNav />
  <div class="main-container">
    <router-view
      :orders="orders"
      :products="products"
      @fetchOrders="fetchOrders"
      @completeOrder="completeOrder"
      @addProductsToList="addProductsToList"
      @updateProductInList="updateProductInList"
      @getProduct="getProduct"
      @getProducts="getProducts"
    ></router-view>
  </div>
</template>

<script>
import TopNav from './components/TopNav.vue';

const productServiceUrl = "/products/";
const singleProductServiceUrl = "/product/";
const makelineServiceUrl = "/makeline/";

export default {
  name: 'App',
  components: {
    TopNav
  },
  data() {
    return {
      orders: [],
      products: [],
      product: {}
    }
  },
  mounted() {
    this.getProducts();
  },
  methods: {
    async addProductsToList(newProduct) {
      this.products.push(newProduct);
    },
    async updateProductInList(updatedProduct) {
      const index = this.products.findIndex(product => product.id === updatedProduct.id);
      this.products[index] = updatedProduct;
    },
    async getProduct(id) {
      fetch(`${singleProductServiceUrl}${id}`)
        .then(response => response.json())
        .then(product => {
          this.product.id = product.id
          this.product.name = product.name
          this.product.image = product.image
          this.product.description = product.description
          this.product.price = product.price
        })
        .catch(error => {
          console.log(error)
          alert('Error occurred while fetching product')
        })
    },
    async getProducts() {
      fetch(`${productServiceUrl}`)
        .then(response => response.json())
        .then(products => {
          this.products = products
        })
        .catch(error => {
          console.log(error)
          alert('Error occurred while fetching products')
        })
    },
    async fetchOrders() {
      await fetch(`${makelineServiceUrl}order/fetch`)
        .then(response => response.json())
        .then(data => {
          console.log(data)
          if (data) {
            this.orders = data;
          } else {
            console.log('No orders from server');
          }
        })
        .catch(error => console.error(error));
    },
    async completeOrder(orderId) {      
      let order = this.orders.find(order => order.orderId === orderId);
      order.status = 1;

      let orderObject = JSON.stringify(order)
      console.log(orderObject);

      await fetch(`${makelineServiceUrl}order`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json'
        },
        body: orderObject
      })
        .then(response => {
          if (!response.ok) {
            alert('Error occurred while processing order')
          } else {
            alert('Order successfully processed')
            this.orders = this.orders.filter(order => order.orderId !== orderId);
            this.$router.go(-1);
          }
        })
        .catch(error => {
          console.log(error)
          alert('Error occurred while processing order')
        })
    }
  },
}
</script>

<style>
/* Global Best Buy Theme */
:root {
  --bb-blue: #0046be;
  --bb-yellow: #ffce00;
  --bb-dark: #1d252c;
  --bb-gray-bg: #f4f6f8;
  --bb-white: #ffffff;
  --bb-border: #c5cbd5;
}

body {
  background-color: var(--bb-gray-bg);
  margin: 0;
  padding: 0;
}

#app {
  font-family: 'Human BBY', Arial, Helvetica, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: var(--bb-dark);
}

.main-container {
  max-width: 1200px;
  margin: 80px auto 2rem auto; /* Top margin for fixed header */
  padding: 0 1rem;
}

/* Global Button Styles to match Best Buy */
button, .button {
  background-color: var(--bb-yellow);
  color: #000;
  font-weight: 700;
  border: none;
  border-radius: 4px;
  padding: 10px 20px;
  cursor: pointer;
  transition: background-color 0.2s;
  font-size: 14px;
}

button:hover, .button:hover {
  background-color: #e0b500;
}

/* Secondary Action Buttons (Blue) */
.ai-button, .secondary-btn {
  background-color: var(--bb-blue);
  color: white;
}

.ai-button:hover, .secondary-btn:hover {
  background-color: #003da6;
}

/* Global Table Styles */
table {
  width: 100%;
  border-collapse: collapse;
  background: var(--bb-white);
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
  border-radius: 4px;
  overflow: hidden;
}

th {
  background-color: #f0f2f4;
  color: var(--bb-dark);
  font-weight: 700;
  text-transform: uppercase;
  font-size: 12px;
  padding: 12px;
  border-bottom: 2px solid var(--bb-border);
}

td {
  padding: 12px;
  border-bottom: 1px solid #e0e6ef;
  font-size: 14px;
}

a {
  color: var(--bb-blue);
  text-decoration: none;
  font-weight: bold;
}

a:hover {
  text-decoration: underline;
}

h2, h3 {
  color: var(--bb-dark);
}

</style>