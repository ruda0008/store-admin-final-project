<template>
  <div class="form-wrapper">
    <div class="form-header">
      <h2>{{ product.id ? 'Edit Product' : 'Add New Product' }}</h2>
      <div class="action-button">
        <button @click="saveProduct" class="button primary-btn">Save Product</button>
      </div>
    </div>
    
    <div v-if="showValidationErrors" class="error-banner">
      <strong>Please fix the following issues:</strong>
      <ul>
        <li v-for="error in validationErrors" :key="error">{{ error }}</li>
      </ul>
    </div>

    <div class="product-form-card">
      <div class="form-group">
        <label for="product-name">Product Name</label>
        <input id="product-name" placeholder="e.g. Sony 65' 4K TV" v-model="product.name" />
      </div>

      <div class="form-group half">
        <label for="product-price">Price ($)</label>
        <input id="product-price" placeholder="0.00" v-model="product.price" type="number" step="0.01" />
      </div>

      <div class="form-group">
        <label for="product-tags">Keywords (comma separated)</label>
        <input id="product-tags" placeholder="electronics, tv, sony" v-model="product.tags" />
      </div>

      <div class="form-group">
        <label for="product-description">Description</label>
        <div class="input-with-action">
          <textarea rows="6" id="product-description" placeholder="Enter detailed product description..." v-model="product.description" />
          <button @click="generateDescription" class="ai-button small-btn" v-show="aiCapabilities.includes('description')">
            Ask AI
          </button>
        </div>
      </div>

      <div class="form-group">
        <label>Product Image</label>
        <div class="image-section">
          <div class="image-input-row">
            <input id="product-image-text" placeholder="Image URL" v-model="product.image" v-show="!aiCapabilities.includes('image')"/>
            <button id="product-image-btn" @click="generateImage" class="ai-button small-btn" v-show="aiCapabilities.includes('image')">
              Generate Image
            </button>
          </div>
          
          <div id="product-image-container" class="image-preview" :class="{ loading: isLoadingImage }" v-show="product.image || isLoadingImage">
            <img v-if="product.image" :src="product.image" alt="Product Image" />
            <div class="overlay" v-if="isLoadingImage">{{ overlayText }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  const aiServiceUrl = '/ai/';
  const productServiceUrl = '/product/';
  
  export default {
    name: 'ProductForm',
    props: ['products'],
    emits: ['addProductsToList','updateProductInList'],
    data() {
      return {
        product: {
          id: 0,
          name: '',
          image: '/placeholder.png',
          description: '',
          price: 0.00,
          tags: []
        },
        aiCapabilities: [],
        showValidationErrors: false,
        isLoadingImage: false,
        overlayText: ''
      }
    },
    mounted() {
      if (this.$route.params.id) {
        const product = this.products.find(product => product.id == this.$route.params.id)
        if(product) {
            this.product = Object.assign({}, product);
            if (!this.product.tags) {
            this.product.tags = [];
            }
        }
      }

      fetch(`${aiServiceUrl}health`)
        .then(response => response.json())
        .then(data => {
          if (data.status === 'ok') {
            this.aiCapabilities = data.capabilities;
          }
        })
        .catch(error => {
          console.log(error)
        })
    },
    computed: {
      validationErrors() {
        let errors = [];
        if (this.product.name.length === 0) errors.push('Name is required');
        if (this.product.description.length === 0) errors.push('Description is required');
        if (this.product.price <= 0) errors.push('Price must be greater than 0');
        return errors;
      }
    },
    methods: {
      generateDescription() {
        if (this.product.tags.length === 0) {
          alert('Please enter keywords first')
          return;
        }
        const intervalId = this.waitForAI();
        let requestBody = {
          name: this.product.name,
          tags: typeof this.product.tags === 'string' ? this.product.tags.split(',') : this.product.tags
        }
        this.product.description = "";

        fetch(`${aiServiceUrl}generate/description`, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(requestBody)
        })
          .then(response => response.json())
          .then(product => { this.product.description = product.description })
          .catch(error => { console.log(error); alert('Error generating description') })
          .finally(() => { clearInterval(intervalId); })
      },
      generateImage() {
        if (this.product.description === "") {
          alert('Please enter a description first')
          return;
        }
        this.isLoadingImage = true;
        this.overlayText = 'Generating...';
        let requestBody = {
          name: this.product.name,
          description: this.product.description
        }

        fetch(`${aiServiceUrl}generate/image`, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(requestBody)
        })
          .then(response => response.json())
          .then(product => {
            this.overlayText = 'Downloading...';
            this.product.image = product.image
          })
          .catch(error => { console.log(error); alert('Error generating image') })
          .finally(() => { this.isLoadingImage = false; })
      },
      waitForAI() {
        let dots = '';
        const intervalId = setInterval(() => {
          dots += '.';
          this.product.description = `Thinking${dots}`;
        }, 500);
        return intervalId;
      },
      saveProduct() {
        if (this.validationErrors.length > 0) {
          this.showValidationErrors = true;
          return;
        }
        let method = 'PUT';
        let path = this.$route.path;
        if (path.includes('add')) {
          method = 'POST';
        }
        this.product.price = parseFloat(this.product.price);

        fetch(`${productServiceUrl}`, {
          method: method,
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(this.product)
        })
          .then(response => response.json())
          .then(product => {
            alert('Product saved successfully')            
            if (method === 'PUT') {
              this.$emit('updateProductInList', this.product);
            } else {
              this.$emit('addProductsToList', product);
            }
            this.$router.push(`/product/${product.id}`);
          })
          .catch(error => { console.log(error); alert('Error saving product') })
      }
    }
  }
</script>

<style scoped>
.form-wrapper {
  max-width: 800px;
  margin: 0 auto;
  text-align: left;
}

.form-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  border-bottom: 1px solid #c5cbd5;
  padding-bottom: 10px;
}

.product-form-card {
  background: white;
  padding: 30px;
  border-radius: 4px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
}

.form-group {
  margin-bottom: 20px;
}

.form-group.half {
  width: 50%;
}

label {
  display: block;
  font-weight: 700;
  margin-bottom: 8px;
  color: #1d252c;
  font-size: 14px;
}

input, textarea {
  width: 100%;
  padding: 10px;
  border: 1px solid #c5cbd5;
  border-radius: 4px;
  font-size: 16px;
  box-sizing: border-box;
}

input:focus, textarea:focus {
  outline: none;
  border-color: #0046be;
  box-shadow: 0 0 0 3px rgba(0, 70, 190, 0.2);
}

.input-with-action {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.error-banner {
  background-color: #fff0f0;
  border: 1px solid #d0021b;
  color: #d0021b;
  padding: 15px;
  border-radius: 4px;
  margin-bottom: 20px;
}

.image-preview {
  margin-top: 15px;
  border: 1px dashed #c5cbd5;
  padding: 10px;
  text-align: center;
  position: relative;
  min-height: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.image-preview img {
  max-width: 100%;
  max-height: 300px;
}

.small-btn {
  height: auto;
  padding: 8px 16px;
  align-self: flex-start;
}

.overlay {
  position: absolute;
  top: 0; left: 0; width: 100%; height: 100%;
  background: rgba(255, 255, 255, 0.8);
  display: flex;
  align-items: center;
  justify-content: center;
  color: #0046be;
  font-weight: bold;
}
</style>