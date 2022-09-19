<template>
  <div class="paginator d-flex justify-content-between align-items-center mt-3">
    <template v-if="itemsTotal > itemsPerPage[0]">
      <PaginationItemsPerPage
        :items-per-page="itemsPerPage"
        :page-size="pageSize"
        @change-page-size="changePageSize($event)"
      />
      <div class="paginator__items-of-total">
        {{ pageSize * (page - 1) + 1 }} -
        {{ page === pages ? itemsTotal : pageSize * page }} из
        {{ itemsTotal }}
      </div>
      <PaginationPageSelector
        v-if="itemsTotal > pageSize"
        :pages="pages"
        :active-page="page"
        @change-page="$emit('change-page', $event)"
      />
    </template>
    <div
      v-else-if="!itemsTotal && !firstLoad"
      class="alert alert-danger w-100"
      role="alert"
    >
      По вашему запросу ничего не найдено! Попробуйте поиск с другими
      значениями.
    </div>
  </div>
</template>

<script>
import PaginationItemsPerPage from "./PaginationItemsPerPageBS46";
import PaginationPageSelector from "./PaginationPageSelectorBS46";

export default {
  name: "PaginationBS46",

  components: {
    PaginationItemsPerPage,
    PaginationPageSelector,
  },

  props: ["itemsTotal", "page", "pageSize", "itemsPerPage"],

  data() {
    return {
      firstLoad: true,
    };
  },

  updated: function () {
    this.firstLoad = false;
  },

  methods: {
    changePageSize(newPageSize) {
      const currentItemOnTopOfPage = this.countFirstItemNumberOnPage(
        this.pageSize,
        this.page
      );
      const newPage = this.countNewPage(currentItemOnTopOfPage, newPageSize);
      let pageInfo = [];
      pageInfo.push(newPage);
      pageInfo.push(newPageSize);
      this.$emit("change-page-size", pageInfo);
    },

    countFirstItemNumberOnPage(pageSize, page) {
      return pageSize * (page - 1) + 1;
    },

    countNewPage(oldFirstItemNumberOnPage, newPageSize) {
      return Math.ceil(oldFirstItemNumberOnPage / newPageSize);
    },
  },

  computed: {
    pages: function () {
      return Math.ceil(this.itemsTotal / this.pageSize);
    },
  },
};
</script>
