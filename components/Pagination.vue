<template>
  <div class="pagination">
    <button
      v-if="hasPrev"
      @click="$emit('prev')">
        &larr; Prev
    </button>
    <button
      v-if="hasNext"
      @click="$emit('next')">
        Next &rarr;
    </button>
  </div>  
</template>

<script>
export default {
  name: 'Pagination',
  props: {
    hasPrev: {
      type: Boolean,
      default: false
    },
    hasNext: {
      type: Boolean,
      default: false
    }
  }
}
</script>

<style scoped>
  .pagination {

  }
</style>
