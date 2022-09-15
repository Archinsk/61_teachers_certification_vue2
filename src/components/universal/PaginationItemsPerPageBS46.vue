<template>
  <div class="paginator__items-per-page d-none d-lg-block">
    <template v-if="separate">
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
    </template>
    <template v-else>
      <nav aria-label="...">
        <ul class="pagination mb-0">
          <li
            :class="['page-item', { active: valuePerPage === pageSize }]"
            v-for="valuePerPage of itemsPerPage"
            :key="valuePerPage"
          >
            <a
              href="#"
              class="page-link"
              @click.prevent="$emit('change-page-size', valuePerPage)"
              >{{ valuePerPage }}</a
            >
          </li>
        </ul>
      </nav>
    </template>
  </div>
</template>

<script>
export default {
  name: "PaginationItemsPerPageBS46",
  props: ["itemsPerPage", "pageSize", "separate", "text"],
};
</script>

<style lang="scss" scoped>
.paginator .paginator__items-per-page .page-size:not(:last-child) {
  margin-right: 0.5rem;
}
</style>
