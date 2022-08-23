<template>
  <div class="paginator__items-per-page d-none d-lg-block">
    <button
      v-for="valuePerPage of itemsPerPage"
      :key="valuePerPage"
      type="button"
      :class="[
        'page-size btn',
        valuePerPage === pageSize ? 'btn-primary' : 'btn-outline-secondary',
      ]"
      @click="$emit('change-page-size', valuePerPage)"
    >
      {{ valuePerPage }}
    </button>
  </div>
</template>

<script>
export default {
  name: "PaginationItemsPerPageBS46",
  props: ["itemsPerPage", "pageSize"],
};
</script>

<style lang="scss" scoped>
.paginator .paginator__items-per-page .page-size:not(:last-child) {
  margin-right: 0.5rem;
}
</style>
