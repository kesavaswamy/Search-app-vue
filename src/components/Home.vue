<template>
  <header class="main-header">
    <img alt="Vue logo" class="logo" src="/src/assets/logo.svg" width="48" height="48" />
    <h1>User Search</h1>
  </header>
  <div class="home-container">
    <div class="search-bar">
      <input v-model="searchText" @keyup.enter="onSearch" type="text" placeholder="Enter user name or username..." />
      <button @click="onSearch" :disabled="loading">{{ loading ? 'Searching...' : 'Search' }}</button>
      <button @click="onClear" :disabled="loading || !searchText" class="clear-btn">Clear</button>
    </div>
    <div v-if="error" class="error">{{ error }}</div>
    <!-- <KendoResultsGrid :results="results" v-if="results.length" /> -->
    <div v-if="results.length">
      <table class="data-table">
        <thead>
          <tr>
            <th @click="sortBy('id')" class="sortable">
              ID <span class="sort-icon">{{ getSortIcon('id') }}</span>
            </th>
            <th @click="sortBy('name')" class="sortable">
              Name <span class="sort-icon">{{ getSortIcon('name') }}</span>
            </th>
            <th @click="sortBy('username')" class="sortable">
              Username <span class="sort-icon">{{ getSortIcon('username') }}</span>
            </th>
            <th @click="sortBy('email')" class="sortable">
              Email <span class="sort-icon">{{ getSortIcon('email') }}</span>
            </th>
            <th>Address</th>
            <th @click="sortBy('phone')" class="sortable">
              Phone <span class="sort-icon">{{ getSortIcon('phone') }}</span>
            </th>
            <th @click="sortBy('website')" class="sortable">
              Website <span class="sort-icon">{{ getSortIcon('website') }}</span>
            </th>
            <th @click="sortBy('company')" class="sortable">
              Company <span class="sort-icon">{{ getSortIcon('company') }}</span>
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="user in paginatedResults" :key="user.id">
            <td>{{ user.id }}</td>
            <td>{{ user.name }}</td>
            <td>{{ user.username }}</td>
            <td>{{ user.email }}</td>
            <td>
              {{ user.address ? `${user.address.street}, ${user.address.suite}, ${user.address.city}, ${user.address.zipcode}` : '' }}
            </td>
            <td>{{ user.phone }}</td>
            <td>{{ user.website }}</td>
            <td>{{ user.company && user.company.name ? user.company.name : '' }}</td>
          </tr>
        </tbody>
      </table>
      <div class="pagination">
        <button @click="prevPage" :disabled="currentPage === 1" class="page-btn">Previous</button>
        <span class="page-info">Page {{ currentPage }} of {{ totalPages }} ({{ results.length }} total results)</span>
        <button @click="nextPage" :disabled="currentPage === totalPages" class="page-btn">Next</button>
      </div>
    </div>
    <div v-else-if="!loading && !error">
      <p>No users to display.</p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const searchText = ref('');
const results = ref([]);
const loading = ref(false);
const error = ref('');

// Pagination
const currentPage = ref(1);
const itemsPerPage = ref(5);

// Sorting
const sortColumn = ref('');
const sortDirection = ref('asc');

// Computed: sorted results
const sortedResults = computed(() => {
  if (!sortColumn.value) return results.value;
  
  return [...results.value].sort((a, b) => {
    let aVal = a[sortColumn.value];
    let bVal = b[sortColumn.value];
    
    // Handle nested company name
    if (sortColumn.value === 'company') {
      aVal = a.company?.name || '';
      bVal = b.company?.name || '';
    }
    
    // Convert to strings for comparison
    aVal = String(aVal).toLowerCase();
    bVal = String(bVal).toLowerCase();
    
    if (sortDirection.value === 'asc') {
      return aVal > bVal ? 1 : aVal < bVal ? -1 : 0;
    } else {
      return aVal < bVal ? 1 : aVal > bVal ? -1 : 0;
    }
  });
});

// Computed: paginated results
const paginatedResults = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage.value;
  const end = start + itemsPerPage.value;
  return sortedResults.value.slice(start, end);
});

// Computed: total pages
const totalPages = computed(() => {
  return Math.ceil(results.value.length / itemsPerPage.value);
});

async function onSearch() {
  loading.value = true;
  error.value = '';
  results.value = [];
  currentPage.value = 1;
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/users');
    if (!response.ok) throw new Error('API error');
    const data = await response.json();
    if (!searchText.value) {
      results.value = data;
    } else {
      // Filter users by name or username containing the search text
      const filtered = data.filter(user =>
        user.name.toLowerCase().includes(searchText.value.toLowerCase()) ||
        user.username.toLowerCase().includes(searchText.value.toLowerCase())
      );
      results.value = filtered;
    }
  } catch (e) {
    error.value = 'Failed to fetch users.';
  } finally {
    loading.value = false;
  }
}

function onClear() {
  searchText.value = '';
  results.value = [];
  error.value = '';
  currentPage.value = 1;
  sortColumn.value = '';
  sortDirection.value = 'asc';
}

function sortBy(column) {
  if (sortColumn.value === column) {
    sortDirection.value = sortDirection.value === 'asc' ? 'desc' : 'asc';
  } else {
    sortColumn.value = column;
    sortDirection.value = 'asc';
  }
  currentPage.value = 1;
}

function getSortIcon(column) {
  if (sortColumn.value !== column) return '⇅';
  return sortDirection.value === 'asc' ? '↑' : '↓';
}

function nextPage() {
  if (currentPage.value < totalPages.value) {
    currentPage.value++;
  }
}

function prevPage() {
  if (currentPage.value > 1) {
    currentPage.value--;
  }
}
</script>

<style scoped>
.main-header {
  display: flex;
  align-items: center;
  gap: 1rem;
  background: #f5f7fa;
  padding: 1rem 2rem;
  border-bottom: 1px solid #e0e0e0;
  margin-bottom: 2rem;
}
.main-header .logo {
  display: block;
  height: 48px;
  width: 48px;
}
.main-header h1 {
  font-size: 2rem;
  font-weight: 700;
  margin: 0;
  color: #42b983;
}
.data-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 1.5rem;
  background: #fff;
  box-shadow: 0 2px 12px #0001;
}
.data-table th, .data-table td {
  border: 1px solid #ddd;
  padding: 0.5rem 0.75rem;
  text-align: left;
}
.data-table th {
  background: #f5f7fa;
  font-weight: 600;
}
.data-table th.sortable {
  cursor: pointer;
  user-select: none;
}
.data-table th.sortable:hover {
  background: #e8eef5;
}
.sort-icon {
  margin-left: 0.3rem;
  font-size: 0.9rem;
  color: #42b983;
}
.data-table tr:hover {
  background: #f0f7ff;
}
.home-container {
  max-width: 100%;
  margin: 2rem auto;
  padding: 1rem;
}
.search-bar {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1.5rem;
  justify-content: center;
}
.search-bar input {
  flex: 1 1 250px;
  padding: 0.5rem;
  font-size: 1rem;
  min-width: 200px;
}
.search-bar button {
  padding: 0.5rem 1.2rem;
  font-size: 1rem;
  cursor: pointer;
}
.clear-btn {
  background: #eee;
  color: #333;
  border: 1px solid #ccc;
}
.error {
  color: #b00;
  margin-bottom: 1rem;
}
.pagination {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1rem;
  margin-top: 1.5rem;
  padding: 1rem;
}
.page-btn {
  padding: 0.5rem 1rem;
  font-size: 0.95rem;
  cursor: pointer;
  background: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  transition: background 0.2s;
}
.page-btn:hover:not(:disabled) {
  background: #359268;
}
.page-btn:disabled {
  background: #ccc;
  cursor: not-allowed;
}
.page-info {
  font-size: 0.95rem;
  color: #333;
}
</style>
