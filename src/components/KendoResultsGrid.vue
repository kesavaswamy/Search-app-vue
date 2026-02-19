<template>
  <div class="kendo-results-grid">
    <KendoGrid
      :data-items="pagedData"
      :columns="columns"
      :sortable="{ allowUnsort: true, mode: 'multiple' }"
      :sort="sort"
      @sortchange="onSortChange"
      :pageable="{ buttonCount: 5, pageSizes: true }"
      :skip="skip"
      :page-size="pageSize"
      @pagechange="onPageChange"
      style="min-width: 1100px; border-radius: 10px; overflow: hidden; box-shadow: 0 2px 12px #0001;"
    />
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import { orderBy } from '@progress/kendo-data-query';
import { Grid as KendoGrid } from '@progress/kendo-vue-grid';
const props = defineProps({
  results: {
    type: Array,
    default: () => []
  }
});

const sort = ref([]);
const onSortChange = (e) => {
  sort.value = e.sort;
};

const sortedData = computed(() => {
  return orderBy(props.results, sort.value);
});

// Custom cell renderer for address
function addressCell(props) {
  if (!props || !props.dataItem || !props.dataItem.address) return '';
  const addr = props.dataItem.address;
  return `${addr.street}, ${addr.suite}, ${addr.city}, ${addr.zipcode}`;
}

// Custom cell renderer for company
function companyCell(props) {
  if (!props || !props.dataItem || !props.dataItem.company) return '';
  return props.dataItem.company.name ? props.dataItem.company.name : '';
}

const columns = [
  { field: 'id', title: 'ID', width: '60px' },
  { field: 'name', title: 'Name', width: '180px' },
  { field: 'username', title: 'Username', width: '140px' },
  { field: 'email', title: 'Email', width: '220px' },
  { field: 'address', title: 'Address', width: '260px', cell: addressCell },
  { field: 'phone', title: 'Phone', width: '160px' },
  { field: 'website', title: 'Website', width: '160px' },
  { field: 'company', title: 'Company', width: '180px', cell: companyCell }
];

const pageSize = 5;
const skip = ref(0);
const pagedData = computed(() => sortedData.value.slice(skip.value, skip.value + pageSize));
function onPageChange(e) {
  skip.value = e.skip;
}
</script>

<style scoped>
 .kendo-results-grid {
  margin: 2rem auto 0 auto;
  max-width: 900px;
  background: #fff;
  border-radius: 10px;
  box-shadow: 0 2px 12px #0001;
  padding: 1rem 1.5rem 2rem 1.5rem;
}
.kendo-results-grid :deep(.k-grid-header) {
  background: #f5f7fa;
  font-weight: 600;
  font-size: 1.08rem;
}
.kendo-results-grid :deep(.k-grid-table tr) {
  transition: background 0.2s;
}
.kendo-results-grid :deep(.k-grid-table tr:hover) {
  background: #f0f7ff;
}
.kendo-results-grid :deep(.k-grid) {
  border-radius: 10px;
}
</style>
