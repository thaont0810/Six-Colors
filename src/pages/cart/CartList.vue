<template>
  <div>
    <div v-if="addedProducts.length">
      <b-list-group class="c-list">
        <div class="cart-header">
          <div class="cart-header__checkbox w-checkbox"></div>
          <div class="cart-header__product w-product">Product</div>
          <div class="cart-header__unit-price w-unit-price">Price</div>
          <div class="cart-header__quantity w-quantity">Quantity</div>
          <div class="cart-header__price w-price">Total</div>
          <div class="cart-header__action w-action">Action</div>
        </div>
        <div v-for="addedProduct in addedProducts" :key="addedProduct.id">
          <cart-item
            :addedProduct="addedProduct"
            @toggleDeleteModal="toggleDeleteModal"
            @onDelete="onDeleteProduct"
            @handleSelectProduct="selectProduct"
            @handleDecreaseQuantity="decreaseQuantity"
            @handleIncreaseQuantity="increaseQuantity"
          />
        </div>
      </b-list-group>
      <confirmation-modal
        ref="confirmModal"
        @confirmDeleteProduct="confirmDelete"
        @cancelDeleteProduct="cancelDelete"
      />
    </div>
    <div v-else>No products in cart</div>
  </div>
</template>

<script>
import ConfirmationModal from '../../components/thaont/ConfirmationModal.vue'
import CartItem from './CartItem.vue'

export default {
  components: { CartItem, ConfirmationModal },

  name: 'CartList',

  props: {
    addedProducts: {
      type: Array
    }
  },

  data() {
    return {
      deletingProduct: {},
      selectedProducts: []
    }
  },

  methods: {
    toggleDeleteModal() {
      this.$refs.confirmModal.isShow = !this.$refs.confirmModal.isShow
    },

    onDeleteProduct(item) {
      this.deletingProduct = item
    },

    confirmDelete() {
      this.$emit('handleDeleteInCart', this.deletingProduct)
      this.$refs.confirmModal.isShow = false
    },

    cancelDelete() {
      this.$refs.confirmModal.isShow = false
    },

    selectProduct(item) {
      const hasId = this.selectedProducts.find((p) => p.id === item.id)

      if (hasId) {
        const newSelectedProducts = this.selectedProducts.filter((product) => {
          return product.id !== item.id
        })
        this.selectedProducts = newSelectedProducts
        this.$emit('handleSelectMultiple', newSelectedProducts)
      } else {
        this.selectedProducts.push(item)
        this.$emit('handleSelectMultiple', this.selectedProducts)
      }
    },

    decreaseQuantity(item) {
      const newProducts = this.addedProducts.map((product) => {
        if (product.id === item.id) {
          const updatedTask = { ...item }
          return updatedTask
        }
        return product
      })
      localStorage.setItem('productsInCart', JSON.stringify(newProducts))
    },

    increaseQuantity(item) {
      const newProducts = this.addedProducts.map((product) => {
        if (product.id === item.id) {
          const updatedTask = { ...item }
          return updatedTask
        }
        return product
      })
      localStorage.setItem('productsInCart', JSON.stringify(newProducts))
    }
  }
}
</script>
