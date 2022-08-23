<template>
  <thead>
    <tr>
      <th
        v-for="(column, indexColumn) of columnsList"
        :key="indexColumn"
        scope="col"
      >
        <a href="" @click.prevent="$emit('sort-table', indexColumn)">
          {{ column }}
          <template v-if="column === sortColumn">
            <span v-if="ascendingSortOrder" class="material-icons"
              >arrow_circle_down</span
            >
            <span v-else class="material-icons">arrow_circle_up</span>
          </template>
        </a>
      </th>
    </tr>
  </thead>
</template>

<script>
export default {
  name: "TableHeadBootstrapCustom",
  props: {
    columnsList: Array,
    sortColumn: String,
    ascendingSortOrder: Boolean,
  },
};
</script>
