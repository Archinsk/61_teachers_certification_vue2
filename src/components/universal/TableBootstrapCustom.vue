<template>
  <table class="table table-hover">
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
</template>

<script>
import TableHeadBootstrapCustom from "./TableHeadBootstrapCustom";
import TableBodyBootstrapCustom from "./TableBodyBootstrapCustom";
export default {
  name: "TableBootstrapCustom",
  components: { TableBodyBootstrapCustom, TableHeadBootstrapCustom },
  props: ["tableData"],
  computed: {
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
