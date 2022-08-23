<template>
  <div>
    <table :class="tableClass">
      <TableHeadBootstrapCustom
        :columns-list="tableData.columnsList"
        :sort-column="tableData.sortColumn"
        :ascending-sort-order="tableData.ascendingSortOrder"
        @sort-table="$emit('sort-table', $event)"
      />
      <TableBodyBootstrapCustom
        :rows-list="sortedRows"
        :primary-column-index="primaryColumnIndex"
        @row-click="$emit('row-click', $event)"
      />
    </table>
    <PaginationBS46
      v-show="itemsTotal > pageSize"
      :items-total="itemsTotal"
      :page="page"
      :page-size="pageSize"
      :items-per-page="itemsPerPage"
      @change-page-size="$emit('change-page-size', $event)"
      @change-page="$emit('change-page', $event)"
    ></PaginationBS46>
  </div>
</template>

<script>
import TableHeadBootstrapCustom from "./TableHeadBootstrapCustom";
import TableBodyBootstrapCustom from "./TableBodyBootstrapCustom";
import PaginationBS46 from "./PaginationBS46";
export default {
  name: "TableBootstrapCustom",
  components: {
    PaginationBS46,
    TableBodyBootstrapCustom,
    TableHeadBootstrapCustom,
  },
  props: {
    tableData: Object,
    itemsTotal: Number,
    page: Number,
    pageSize: Number,
    itemsPerPage: Array,
    striped: Boolean,
    bordered: Boolean,
    borderless: Boolean,
    hover: Boolean,
  },
  computed: {
    tableClass: function () {
      let tableStyleClass = "table";
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
        if (this.tableData.columnsList[i] === this.tableData.primaryColumn) {
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
      const columnIndex = this.sortedColumnIndex;
      function sortByColumn(a, b) {
        if (a[columnIndex] > b[columnIndex]) return 1;
        if (a[columnIndex] === b[columnIndex]) return 0;
        if (a[columnIndex] < b[columnIndex]) return -1;
      }
      if (this.tableData.ascendingSortOrder) {
        return rowsListCopy.sort(sortByColumn);
      } else {
        return rowsListCopy.sort(sortByColumn).reverse();
      }
    },
  },
};
</script>
