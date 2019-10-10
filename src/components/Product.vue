<template>
    <div class="product">
        <div class="product-image">
            <img :src="image" alt="Product image"/>
        </div>

        <div class="product-info">
            <h1>{{ title }}</h1>
            <p v-if="inStock">In Stock</p>
            <p v-else>Out of Stock</p>
            <p>Shipping: {{ shipping }}</p>

            <ul>
                <li v-for="(detail, index) in details" :key="index">{{ detail }}</li>
            </ul>

            <div class="color-box"
                 v-for="(variant, index) in variants"
                 :key="variant.variantId"
                 :style="{ backgroundColor: variant.variantColor }"
                 @mouseover="updateProduct(index)"
            ></div>

            <button @click="addToCart"
                    :disabled="!inStock"
                    :class="{ disabledButton: !inStock }"
            >Add to Cart</button>
        </div>

        <ProductTabs :reviews="reviews"></ProductTabs>
    </div>
</template>

<script>
import ProductTabs from './ProductTabs.vue';
import EventBus from '../event-bus';

import imgSocksGreen from '../assets/vmSocks-green-onWhite.jpg';
import imgSocksBlue from '../assets/vmSocks-blue-onWhite.jpg';

export default {
  name: 'Product',
  components: {
    ProductTabs,
  },
  props: {
    premium: {
      type: Boolean,
      required: true,
    },
  },
  data() {
    return {
      product: 'Socks',
      brand: 'Vue Mastery',
      selectedVariant: 0,
      details: ['80% cotton', '20% polyester', 'Gender-neutral'],
      variants: [
        {
          variantId: 2234,
          variantColor: 'green',
          variantImage: imgSocksGreen,
          variantQuantity: 10,
        },
        {
          variantId: 2235,
          variantColor: 'blue',
          variantImage: imgSocksBlue,
          variantQuantity: 0,
        },
      ],
      reviews: [],
    };
  },
  methods: {
    addToCart() {
      this.$emit('add-to-cart', this.variants[this.selectedVariant].variantId);
    },
    updateProduct(index) {
      this.selectedVariant = index;
    },
  },
  computed: {
    title() {
      return `${this.brand} ${this.product}`;
    },
    image() {
      return this.variants[this.selectedVariant].variantImage;
    },
    inStock() {
      return this.variants[this.selectedVariant].variantQuantity;
    },
    shipping() {
      if (this.premium) {
        return 'Free';
      }
      return '2.99';
    },
  },
  mounted() {
    EventBus.$on('review-submitted', (productReview) => {
      this.reviews.push(productReview);
    });
  },
};
</script>
