<template>
  <nav>
    <ul class="pagination">
      <li class="page-item" :class="{ disabled: currentPage === 1 }">
        <button class="page-link" @click="prevPage">Previous</button>
      </li>
      <li v-for="page in pages" :key="page" class="page-item" :class="{ active: currentPage === page }">
        <button class="page-link" @click="changePage(page)">{{ page }}</button>
      </li>
      <li class="page-item" :class="{ disabled: currentPage === totalPages }">
        <button class="page-link" @click="nextPage">Next</button>
      </li>
    </ul>
  </nav>
</template>

<script>
import { computed, reactive, defineEmits } from 'vue';

export default {
  name: 'Pagination',
  props: {
    totalItems: {
      type: Number,
      required: true
    },
    itemsPerPage: {
      type: Number,
      default: 10
    },
    maxPages: {
      type: Number,
      default: 5
    }
  },
  setup(props, {emit}) {

    const state = reactive({
      currentPage: 1
    });

    const totalPages = computed(() => Math.ceil(props.totalItems / props.itemsPerPage));

    const pages = computed(() => {
      const pageNumbers = [];
      let startPage = 1;
      let endPage = totalPages.value;

      if (totalPages.value > props.maxPages) {
        const maxPagesBeforeCurrentPage = Math.floor(props.maxPages / 2);
        const maxPagesAfterCurrentPage = Math.ceil(props.maxPages / 2) - 1;

        if (state.currentPage <= maxPagesBeforeCurrentPage) {
          endPage = props.maxPages;
        } else if (state.currentPage + maxPagesAfterCurrentPage >= totalPages.value) {
          startPage = totalPages.value - props.maxPages + 1;
        } else {
          startPage = state.currentPage - maxPagesBeforeCurrentPage;
          endPage = state.currentPage + maxPagesAfterCurrentPage;
        }
      }

      for (let i = startPage; i <= endPage; i++) {
        pageNumbers.push(i);
      }

      return pageNumbers;
    });
    defineEmits()
    function changePage(page) {
      state.currentPage = page;
        emit('page_changed', page)
    }

    function prevPage() {
      if (state.currentPage > 1) {
        state.currentPage--;
        emit('page_changed', state.currentPage--)
      }
    }

    function nextPage() {
      if (state.currentPage < totalPages.value) {
        state.currentPage++;
        emit('page_changed', state.currentPage--)
      }
    }

    return { state, totalPages, pages, changePage, prevPage, nextPage };
  }
}
</script>

<style>
.pagination li {
  display: inline-block;
}

.pagination .page-item.disabled button {
  pointer-events: none;
  opacity: 0.5;
}

.pagination .page-item.active button {
  background-color: #007bff;
  border-color: #007bff;
}
</style>
