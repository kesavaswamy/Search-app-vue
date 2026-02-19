<template>
  <div class="results-grid">
    <table v-if="results.length">
      <thead>
        <tr>
          <th>Name</th>
          <th>Username</th>
          <th>Email</th>
          <th>City</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in results" :key="user.id">
          <td>{{ user.name }}</td>
          <td>{{ user.username }}</td>
          <td>{{ user.email }}</td>
          <td>{{ user.address?.city || 'N/A' }}</td>
        </tr>
      </tbody>
    </table>
    <div v-else>
      <p>No users to display.</p>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  results: {
    type: Array,
    default: () => []
  }
});
</script>
