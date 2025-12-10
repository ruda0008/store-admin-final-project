<template>
  <div class="order-list-container">
    <div class="header-row">
      <h1>Order Management</h1>
    </div>
    
    <div class="order-list" v-if="hasOrders">
      <table>
        <thead>
          <tr>
            <th>Order ID</th>
            <th>Customer ID</th>
            <th>Status</th>
            <th>Total</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="order in orders" :key="order.orderId">
            <td class="id-col"><router-link :to="`/order/${order.orderId}`">{{ order.orderId }}</router-link></td>
            <td>{{ order.customerId }}</td>
            <td>
              <span class="status-badge">{{ order.status }}</span>
            </td>
            <td class="price-col">${{ orderTotal(order) }}</td>
            <td><router-link :to="`/order/${order.orderId}`" class="view-link">View Details</router-link></td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="empty-state" v-else>
      <h3>No active orders</h3>
      <p>New orders will appear here automatically.</p>
    </div> 
  </div>
</template>

<script>
  export default {
    name: 'OrderList',
    props: ['orders'],
    emits: ['fetchOrders', 'completeOrder'],
    computed: {
      hasOrders() {
        return this.orders.length > 0
      }
    },
    methods: {
      fetchOrders() {
        this.$emit('fetchOrders')
      },
      orderTotal(order) {
        let total = 0;
        order.items.forEach(item => {
          total += item.price * item.quantity;
        });
        return total.toFixed(2);
      }
    },
    beforeMount() {
      this.fetchOrders()
    }
  }
</script>

<style scoped>
.order-list-container {
  text-align: left;
}

.header-row {
  margin-bottom: 20px;
  border-bottom: 1px solid #c5cbd5;
  padding-bottom: 10px;
}

h1 {
  font-size: 24px;
  font-weight: 700;
  color: #1d252c;
  margin: 0;
}

.id-col a {
  font-family: monospace;
  font-size: 1.1em;
}

.price-col {
  font-weight: bold;
}

.status-badge {
  background-color: #e0e6ef;
  padding: 4px 8px;
  border-radius: 12px;
  font-size: 12px;
  font-weight: bold;
}

.view-link {
  font-size: 12px;
}

.empty-state {
  background: white;
  padding: 40px;
  text-align: center;
  border-radius: 4px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
}
</style>