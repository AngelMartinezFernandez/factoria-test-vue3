<template>
  <div id="home">
    <div class="content" :class="loadingClass">
      <div v-if="loading" class="lds-dual-ring"></div>
      <!--      LOADING SACADO DE https://loading.io/css/-->
      <div class="content__products" v-else v-for="product in productsStore.products" :key="product.id">
        <Product
            :id="product.id"
            :image="product.image"
            :noImage="productsStore.noImageSrc"
            :title="product.title"
            :description="product.description"
            :price="product.price"
            @addProduct="addProduct"
        />
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, onBeforeMount, ref } from 'vue'
import { useProductsStore } from '@/stores/products.js' // CASI LLORO PORQUE PUSE STORE EN VEZ DE STORES Y EL ERROR NO ERA DESCRIPTIVO
import { useCartStore } from '@/stores/cart'
import Product from '@/components/products/Product.vue'

const productsStore = useProductsStore()
const cartStore = useCartStore()
// CICLO DE VIDA DE VUE, TAMBIÉN SE CONOCEN COMO HOOKS, BUSCAR EN LA DOCUMENTACIÓN OFICIAL:
// onBeforeMount: AQUI SUELO TRATAR DATOS ANTES DE QUE SE RENDERICE EL TEMPLATE (HTML)
// onMounted: ES LA PARTE "FINAL" EN LA QUE SE RENDERIZA EL TEMPLATE (HTML)
// onBeforeUnmount: AQUI SUELO TRATAR ACCIONES QUE SE REALIZAN AL DESTRUIR EL COMPONENTE
// SI TENÉIS DUDAS PONERLOS TODOS CON UN CONSOLE.LOG DIFERENTE EN CADA UNO, ASI VERÉIS EL ORDEN.
onBeforeMount( () => {
  getProducts()
})

// DATA
const loading = ref(true)

// COMPUTED
const loadingClass = computed(() => {
  return loading.value ? 'loading' : ''
})

// METHODS
const getProducts = async () => {
  await productsStore.fetchProducts()
  loading.value = false
}

const addProduct = (productId) => {
  const product = productsStore.getProductById(productId)
  cartStore.addItemToShoppingCart(product)
}
</script>

<style scoped>
#home {
  height: 100vh;
  overflow-y: auto;
}

.content {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.content__products {
  width: 90%;
}

.loading {
  display: flex;
  justify-content: center;
  align-content: center;
  width: 100%;
  height: 100%;
}
</style>
