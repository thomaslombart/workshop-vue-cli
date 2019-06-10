<template>
  <ul class="list-reset flex mt-3">
    <li
      :key="filter"
      v-for="filter in filters"
      @click="$emit('changeCurrentFilter', filter)"
      class="rounded-full uppercase px-2 py-1 text-xs font-bold mr-2 cursor-pointer"
      :class="currentFilter === filter ? 'bg-blue-700 text-white' : 'bg-gray-100 text-gray-700'"
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
