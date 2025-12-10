<template>
  <nav>
    <div class="nav-container">
      <div class="logo">
        <a href="/">
          <span class="logo-tag">BEST</span>
          <span class="logo-text">BUY</span> 
          <span class="portal-text">ADMIN PORTAL</span>
        </a>
      </div>
      <ul class="nav-links" :class="{ 'nav-links--open': isNavOpen }">
        <li><router-link to="/orders" @click="closeNav">Orders</router-link></li>
        <li><router-link to="/products" @click="closeNav">Products</router-link></li>
      </ul>
      <button class="hamburger" @click="toggleNav">
        <span class="hamburger-icon"></span>
      </button>
    </div>
  </nav>
</template>

<script>
export default {
  name: 'TopNav',
  data() {
    return {
      isNavOpen: false
    }
  },
  methods: {
    toggleNav() {
      this.isNavOpen = !this.isNavOpen
    },
    closeNav() {
      this.isNavOpen = false
    }
  }
}
</script>

<style scoped>
nav {
  background-color: #0046be; /* Best Buy Blue */
  color: #fff;
  height: 65px;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1000;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.nav-container {
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 100%;
  padding: 0 1rem;
}

.logo a {
  display: flex;
  align-items: center;
  color: #fff;
  text-decoration: none;
  gap: 5px;
}

.logo-tag {
  background-color: #ffce00;
  color: #000;
  font-weight: 900;
  padding: 2px 5px;
  font-size: 1.2rem;
  font-family: Arial, sans-serif;
}

.logo-text {
  font-weight: 900;
  font-size: 1.2rem;
  font-family: Arial, sans-serif;
}

.portal-text {
  font-weight: 300;
  font-size: 1rem;
  margin-left: 10px;
  opacity: 0.8;
  border-left: 1px solid rgba(255,255,255,0.3);
  padding-left: 10px;
}

.nav-links {
  display: flex;
  list-style: none;
  margin: 0;
  padding: 0;
}

.nav-links li {
  margin: 0 1.5rem;
}

.nav-links a {
  color: #fff;
  font-weight: 600;
  font-size: 14px;
  text-decoration: none;
  transition: color 0.2s;
}

.nav-links a:hover {
  color: #ffce00; /* Yellow hover */
  text-decoration: none;
}

.hamburger {
  display: none;
}

@media (max-width: 768px) {
  .nav-links {
    display: none;
    position: absolute;
    top: 65px;
    left: 0;
    right: 0;
    background-color: #0046be;
    flex-direction: column;
    padding: 1rem;
    border-top: 1px solid rgba(255,255,255,0.1);
  }

  .nav-links--open {
    display: flex;
  }

  .nav-links--open li {
    padding: 1rem 0;
    border-bottom: 1px solid rgba(255,255,255,0.1);
  }

  .hamburger {
    display: block;
    background: none;
    margin-top: 0;
  }
}
</style>