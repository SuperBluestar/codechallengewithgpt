<template>
  <div class="container">
    <h1>Product List</h1>
    <input type="text" v-model="searchTerm" @keyup="delayedSearch">
    <table class="table">
      <thead>
        <tr>
          <th>Name</th>
          <th>Price</th>
          <th>Description</th>
        </tr>
      </thead>
      <tbody>
        <ProductRow v-for="product in products" :key="product.id" :product="product" />
      </tbody>
    </table>
    <Pagination :totalItems="maxCnt" :itemsPerPage="perPage" :currentPage="curPage" @page_changed="pageChanged"/>
  </div>
</template>

<script>
import ProductRow from './ProductRow.vue'
import Pagination from '../utils/Pagination.vue'

export default {
  name: 'App',
  components: {
    ProductRow,
    Pagination
  },
  data() {
    return {
      products: [],
      curPage: 1,
      maxCnt: 1,
      perPage: 5,
      searchTerm: ''
    }
  },
  methods: {
    fetchData() {
      console.log('sssss')
      if(this.searchTerm === '') {
        fetch(`https://dummyjson.com/products?limit=${this.perPage}&skip=${(this.curPage - 1) * this.perPage}&select=title,price,description`)
        .then(response => response.json())
        .then(data => {
          this.products = data.products
          console.log(this.products)
          this.maxCnt = data.total
        })
        .catch(error => console.error(error))
      } else {
        fetch(`https://dummyjson.com/products/search?q=${this.searchTerm}&select=title,price,description`)
        .then(response => response.json())
        .then(data => {
          this.products = data.products
          console.log(this.products)
          this.maxCnt = data.total
        })
        .catch(error => console.error(error))
      }
    },
    delayedSearch() {
      clearTimeout(this.searchTimeout);
      this.searchTimeout = setTimeout(() => {
        this.fetchData();
      }, 500);
    },
    resetSearch() {
      this.searchQuery = '';
      this.filteredProducts = this.products;
    },
    pageChanged(curPage) {
      this.curPage = curPage
      this.fetchData()
    }
  },
  mounted() {
    this.fetchData()
  }
}
</script>
