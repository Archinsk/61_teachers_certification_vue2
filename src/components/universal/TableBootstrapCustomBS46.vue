<template>
  <div>
    <template v-if="filter">
      <CollapseButtonBootstrapCustomBS46
        :target-id="tableData.id + 'Collapse'"
        class="mb-3"
      >
        Фильтр
      </CollapseButtonBootstrapCustomBS46>
      <CollapseBootstrapCustomBS46 :id="tableData.id + 'Collapse'">
        <FilterBootstrapCustomBS46
          :filter-data="tableData.filters"
          @change-filter="$emit('change-filter', $event)"
          @apply-filter="$emit('apply-filter')"
          @clear-filter="$emit('clear-filter')"
        />
        <!--        @changeFilter="$emit('change-filter', $event)"-->
      </CollapseBootstrapCustomBS46>
    </template>
    <div class="table-responsive">
      <table :class="tableClass">
        <TableHeadBootstrapCustomBS46
          :columns-list="tableData.columnsList"
          :sort-column="tableData.sortColumn"
          :ascending-sort-order="tableData.ascendingSortOrder"
          @sort-table="$emit('sort-table', $event)"
        />
        <TableBodyBootstrapCustomBS46
          :rows-list="sortedRows"
          :primary-column-index="primaryColumnIndex"
          @row-click="$emit('row-click', $event)"
        />
      </table>
    </div>
    <template v-if="pagination">
      <PaginationBS46
        v-show="itemsTotal > pageSize"
        :items-total="itemsTotal"
        :page="page"
        :page-size="pageSize"
        :items-per-page="itemsPerPage"
        @change-page-size="$emit('change-page-size', $event)"
        @change-page="$emit('change-page', $event)"
      ></PaginationBS46>
    </template>
  </div>
</template>

<script>
import TableHeadBootstrapCustomBS46 from "./TableHeadBootstrapCustomBS46";
import TableBodyBootstrapCustomBS46 from "./TableBodyBootstrapCustomBS46";
import PaginationBS46 from "./PaginationBS46";
import CollapseBootstrapCustomBS46 from "./CollapseBootstrapCustomBS46";
import CollapseButtonBootstrapCustomBS46 from "./CollapseButtonBootstrapCustomBS46";
import FilterBootstrapCustomBS46 from "./FilterBootstrapCustomBS46";
export default {
  name: "TableBootstrapCustomBS46",
  components: {
    FilterBootstrapCustomBS46,
    CollapseBootstrapCustomBS46,
    CollapseButtonBootstrapCustomBS46,
    PaginationBS46,
    TableBodyBootstrapCustomBS46,
    TableHeadBootstrapCustomBS46,
  },
  props: {
    tableData: Object,
    pagination: Boolean,
    filter: Boolean,
    itemsTotal: Number,
    page: Number,
    pageSize: Number,
    itemsPerPage: Array,
    dictionaries: Object,
    striped: Boolean,
    bordered: Boolean,
    borderless: Boolean,
    hover: Boolean,
  },
  computed: {
    tableClass: function () {
      let tableStyleClass = "table mb-0";
      if (this.striped) {
        tableStyleClass += " table-striped";
      }
      if (this.bordered) {
        tableStyleClass += " table-bordered";
      }
      if (this.borderless) {
        tableStyleClass += " table-borderless";
      }
      if (this.hover) {
        tableStyleClass += " table-hover";
      }
      return tableStyleClass;
    },
    primaryColumnIndex: function () {
      for (let i = 0; i < this.tableData.columnsList.length; i++) {
        if (
          this.tableData.columnsList[i].title === this.tableData.primaryColumn
        ) {
          return i;
        }
      }
      return "";
    },
    sortedColumnIndex: function () {
      return this.tableData.columnsList.findIndex(
        (item) => item === this.tableData.sortColumn
      );
    },
    sortedRows: function () {
      let rowsListCopy = JSON.parse(JSON.stringify(this.tableData.rowsList));
      let columnIndex;
      function sortByColumn(a, b) {
        if (a[columnIndex] > b[columnIndex]) return 1;
        if (a[columnIndex] === b[columnIndex]) return 0;
        if (a[columnIndex] < b[columnIndex]) return -1;
      }
      if (this.sortedColumnIndex !== -1) {
        columnIndex = this.sortedColumnIndex;
        if (this.tableData.ascendingSortOrder) {
          return rowsListCopy.sort(sortByColumn);
        } else {
          return rowsListCopy.sort(sortByColumn).reverse();
        }
      } else {
        return rowsListCopy;
      }
    },
  },
  methods: {
    test(value) {
      console.log("В таблице событие срабатывает: " + value);
    },
  },
};
</script>
