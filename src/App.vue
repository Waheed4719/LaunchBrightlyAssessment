<template>
  <div class="max-w-full w-[1500px] mx-auto px-4 min-h-screen">
    <div
      v-if="featuresAreLoading"
      class="flex items-center justify-center flex-col h-screen w-full">
      <Loader />
    </div>
    <div v-else>
      <div class="relative">
        <img
          class="w-[250px] md:w-[450px] mx-auto"
          src="./assets/images/launchbrightly-logo.png"
          alt="Launch Brightly Logo" />
        <h3
          class="text-lg md:text-2xl absolute top-[60%] left-[50%] translate-y-[50%] translate-x-[-50%]">
          Assessment
        </h3>
      </div>

      <Header
        @search="debouncedSearch"
        :sortKey="sortKey"
        :sortOrder="sortOrder"
        :sortName="sortName"
        :filters="filters"
        :editions="editions"
        @filter="filterFeatures" />
      <Table
        :data="features"
        @sort="sortFeatures"
        @filter="filterFeatures"
        :filters="filters" />
      <Pagination
        class="pagination-component"
        :currentPage="currentPage"
        :numberOfPages="numberOfPages"
        @navigate="navigateToPage" />
    </div>
  </div>
</template>

<script setup lang="ts">
import { defineComponent, onMounted, ref } from 'vue';
import Table from '@/components/Table/Table.vue';
import Loader from '@/components/Loader.vue';
import { useFeaturesAPI } from '@/hooks/useFeaturesAPI';
import Pagination from '@/components/Pagination.vue';
import { debounce } from '@/utils';
import Header from './components/Header.vue';

defineComponent({
  name: 'App',
  components: {
    Loader,
    Table,
    Pagination,
    Header,
  },
});
const currentPage = ref(1);
const rowsPerPage = ref(10);
const {
  features,
  featuresAreLoading,
  editions,
  loadFeatures,
  numberOfPages,
  sortFeatures,
  sortKey,
  sortOrder,
  sortName,
  filters,
  filterText,
  filterFeatures,
} = useFeaturesAPI(currentPage, rowsPerPage);
onMounted(async () => {
  document.title = 'Launch Brightly Assessment';
  await loadFeatures();
});

const navigateToPage = (page: number) => {
  currentPage.value = page;
};

//debounced text search by "name" field
const debouncedSearch = debounce((payload) => {
  filterText.value = payload.searchText;
  currentPage.value = 1;
}, 300);
</script>

<style>
#app {
  font-family: Quicksand, Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
