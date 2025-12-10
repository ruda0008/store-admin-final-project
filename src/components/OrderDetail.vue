<template>
  <div class="order-detail-container">
    <div v-if="orderExists">
      <div class="detail-header">
        <h1>Order #{{ order.orderId }}</h1>
        <div class="status-indicator">
            Status: <strong>{{ order.status }}</strong>
        </div>
        <button @click="completeOrder" class="button">Complete Order</button>
      </div>

      <div class="customer-info-card">
        <p><strong>Customer ID:</strong> {{ order.customerId }}</p>
      </div>

      <div class="items-card">
        <h3>Items</h3>
        <table>
          <thead>
            <tr>
              <th>Product ID</th>
              <th>Product Name</th>
              <th>Quantity</th>
              <th>Price</th>
              <th>Total</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in order.items" :key="item.productId">
              <td><router-link :to="`/product/${item.productId}`">{{ item.productId }}</router-link></td>
              <td>{{ productLookup(item.productId) }}</td>
              <td>{{ item.quantity }}</td>
              <td>${{ item.price.toFixed(2) }}</td>
              <td>${{ (item.quantity * item.price).toFixed(2) }}</td>
            </tr>
          </tbody>
          <tfoot>
            <tr>
              <td colspan="4" style="text-align:right; font-weight:bold;">Order Total:</td>
              <td style="font-weight:bold; font-size: 1.2em;">${{ orderTotal() }}</td>
            </tr>
          </tfoot>
        </table>
      </div>
    </div>
    
    <div class="not-found" v-else>
      <h3>Oops! That order was not found...</h3>
      <router-link to="/orders">Back to Orders</router-link>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'OrderDetail',
    props: ['orders','products'],
    emits: ['completeOrder'],
    data() {
      return {
        order: null
      }
    },
    computed: {
      orderExists() {
        return !!this.order
      }
    },
    mounted() {
      this.getOrder()
    },
    methods: {
      getOrder() {
        this.order = this.orders.find(order => order.orderId === this.$route.params.id);  
        
        if (!this.order) {          
          fetch(`/makeline/order/${this.$route.params.id}`)
            .then(response => {
              if (!response.ok) throw new Error('Network response was not ok');
              if (response.status === 204) return null;
              return response.json();
            })
            .then(data => {
              if (data) {
                this.order = data;
              }
            })
            .catch(error => console.error(error));
        }
        return this.order;
      },
      completeOrder() {
        this.$emit('completeOrder', this.order.orderId)
      },
      productLookup(id) {
        let p = this.products.find(product => product.id === id);
        return p ? p.name : 'Unknown Product';
      },
      orderTotal() {
        let total = 0;
        this.order.items.forEach(item => {
          total += item.price * item.quantity;
        });
        return total.toFixed(2);
      }
    }
  }
</script>

<style scoped>
.order-detail-container {
  text-align: left;
}

.detail-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  border-bottom: 1px solid #c5cbd5;
  padding-bottom: 15px;
}

.customer-info-card {
  background: white;
  padding: 15px;
  border-radius: 4px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
  margin-bottom: 20px;
}

.items-card {
  background: white;
  border-radius: 4px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
  padding: 20px;
}

.items-card h3 {
  margin-top: 0;
}

tfoot td {
  background-color: #f9f9f9;
  border-top: 2px solid #c5cbd5;
}

.not-found {
  text-align: center;
  padding: 50px;
}
</style>