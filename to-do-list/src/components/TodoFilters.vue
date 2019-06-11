<template>
  <ul class="list">
    <li
      :key="filter"
      v-for="filter in filters"
      @click="$emit('changeCurrentFilter', filter)"
      class="filter"
      :class="{'filter--active': currentFilter === filter}"
    >{{ filter }}</li>
  </ul>
</template>

<script>
export default {
  props: {
    currentFilter: String
  },
  data() {
    return {
      filters: ["all", "active", "done"]
    };
  }
};
</script>

<style scoped>
.list {
  display: flex;
  list-style: none;
}

.filter {
  border-radius: 9999px;
  text-transform: uppercase;
  padding: 0.25rem 0.5rem;
  font-size: 0.75rem;
  font-weight: bold;
  cursor: pointer;
  margin-right: 0.25rem;
  background-color: #f7fafc;
  color: #4a5568;
}

.filter--active {
  background-color: #2b6cb0;
  color: #fff;
}
</style>
