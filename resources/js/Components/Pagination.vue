<template>
    <div class="pagination-wrapper" style="width:50%;">
      <nav class="d-flex justify-items-center justify-content-between">
        <div class="d-none flex-sm-fill d-sm-flex align-items-sm-center justify-content-sm-between">
          <div>
            <p class="small text-muted">
              Showing
              <span class="fw-semibold">{{ currentPageStart }}</span>
              to
              <span class="fw-semibold">{{ currentPageEnd }}</span>
              of
              <span class="fw-semibold">{{ totalItems }}</span>
              results
            </p>
          </div>
  
          <div>
            <ul class="pagination">
              <li :class="['page-item', { 'disabled': !hasPrevious }]">
                <button class="page-link" @click="getData('previous')">‹</button>
              </li>
              <li v-for="page in pages" :key="page" :class="['page-item', { 'active': page === currentPage }]">
                <button v-if="page !== currentPage" class="page-link" @click="getData(page)">{{ page }}</button>
                <span v-else class="page-link">{{ page }}</span>
              </li>
              <li :class="['page-item', { 'disabled': !hasNext }]">
                <button class="page-link" @click="getData('next')">›</button>
              </li>
            </ul>
          </div>
        </div>
        <div class="d-flex justify-content-between flex-fill d-sm-none me-2">
          <ul class="pagination">
            <li :class="['page-item', { 'disabled': !hasPrevious }]">
              <button class="page-link" @click="getData('previous')">« Previous</button>
            </li>
            <li :class="['page-item', { 'disabled': !hasNext }]">
              <button class="page-link" @click="getData('next')">Next »</button>
            </li>
          </ul>
        </div>
      </nav>
    </div>
  </template>
  
  <script setup>
  import { computed, defineProps, defineEmits } from 'vue';
  
  const props = defineProps({
    currentPage: Number,
    totalPages: Number,
    totalItems: Number,
    perPage: Number,
    links: Array,
    currentPageStart: Number,
    currentPageEnd: Number,
  });

  const emit = defineEmits(['getPageData'])
  
  const hasPrevious = computed(() => props.currentPage > 1);
  const hasNext = computed(() => props.currentPage < props.totalPages);
  
  const pages = computed(() => {
    const pageList = [];
    for (let i = 1; i <= props.totalPages; i++) {
      pageList.push(i);
    }
    return pageList;
  });

  function getData(param) {
    if(param === 'previous') {
        emit('getPageData', props.currentPage - 1);
    } else if(param === 'next') {
        emit('getPageData', props.currentPage + 1)
    } else {
        emit('getPageData', param)
    }
  }
  </script>
  
  <style scoped>
  .pagination-wrapper {
    margin: 1rem 0;
  }
  </style>
  