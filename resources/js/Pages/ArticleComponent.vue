<template>
  <div class="col-lg-6 mx-auto p-4 py-md-5">
    <header class="d-flex align-items-center pb-3 mb-3">
      <a href="/" class="d-flex align-items-center text-dark text-decoration-none">
        <img src="https://omgiva-prod.nyc3.cdn.digitaloceanspaces.com/websites/app/omgiva_logo_with_text_v4_600x144.png"
          style="max-width: 200px;">
      </a>
    </header>
    <main>
      <div class="row g-5">
        <div class="col-md-12">
          <div v-if="loading" class="text-center">
            <p>Loading articles...</p>
          </div>

          <article v-for="article in articles" :key="article.id" class="">
            <small class="text-success text-opacity-50 fw-bold">{{ article.category }}</small>
            <h2 class="mb-2 fw-bold">{{ article.title }}</h2>
            <p>
              <i class="small bi bi-clock text-info text-opacity-70"></i> <span class="small text-muted">{{
                formatDate(article.created_at) }} </span>
              <i class="small bi bi-person text-info text-opacity-70 ms-2"></i> <a href="#"
                class="small text-muted text-body text-decoration-none me-2">{{ article.author }} </a>
              <template v-if="article.tags.length > 0">
                <span v-for="tag in article.tags" :key="tag">
                  <i class="small bi bi-tag text-info text-opacity-70 ms-1"></i> <span class="small text-muted">{{ tag
                    }} </span>
                </span>
              </template>
            </p>
            <hr>
            <p>This is some additional paragraph placeholder content. It has been written to fill the available space
              and show how a
              longer snippet of text affects the surrounding content. We'll repeat it often to keep the demonstration
              flowing, so be
              on the lookout for this exact same string of text.</p>
          </article>

          <div v-if="error" class="text-center text-danger">
            <p>Error loading articles: {{ error.message }}</p>
          </div>

          <div v-if="!loading && articles.length === 0" class="text-center">
            <p>No articles available.</p>
          </div>

          <Pagination :currentPage="currentPage" :totalPages="totalPages" :totalItems="totalItems" :perPage="perPage"
            :links="links" :currentPageStart="currentPageStart" :currentPageEnd="currentPageEnd"
            @getPageData="getPageData" />
        </div>
      </div>
    </main>
    <footer class="pt-5 my-5 text-muted border-top">
      <!-- Created by the Bootstrap team &middot; &copy; 2022 -->
    </footer>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import Pagination from './../Components/Pagination.vue';
import moment from 'moment';

const articles = ref([]);
const loading = ref(true);
const error = ref(null);
const currentPage = ref(1);
const totalPages = ref(1);
const totalItems = ref(0);
const links = ref([]);
const perPage = ref(0);
const currentPageStart = ref(1);
const currentPageEnd = ref(1);
const isPage = ref(false);

function getPageData(data) {
  isPage.value = true;
  fetchArticles(`/api/articles?page=${data}`);
}

const fetchArticles = async (url = '/api/articles') => {
  loading.value = true;
  error.value = null;
  try {
    const response = await fetch(url);
    const data = await response.json();

    if (data && data.data) {
      articles.value = data.data;
      currentPage.value = data.meta.current_page;
      perPage.value = data.meta.per_page;
      totalItems.value = data.meta.total;
      links.value = data.meta.links;
      totalPages.value = data.meta.last_page;
      currentPageStart.value = data.meta.from;
      currentPageEnd.value = data.meta.to;
    } else {
      console.error('No articles found in response data');
    }
  } catch (err) {
    error.value = err;
    console.error('Error fetching articles:', err);
  } finally {
    loading.value = false;
  }
};

const formatDate = (dateString) => {
  return moment(dateString).fromNow();
};

onMounted(() => {
  fetchArticles();
});
</script>

<style scoped>
.text-muted {
  color: #6c757d !important
}
</style>