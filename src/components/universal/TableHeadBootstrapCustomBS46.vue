<template>
  <thead>
    <tr>
      <th
        v-for="(column, indexColumn) of columnsList"
        :key="indexColumn"
        scope="col"
      >
        <div v-if="column.sorted" class="d-flex justify-content-between">
          <span
            class="pr-2 sort-link"
            @click="$emit('sort-table', indexColumn)"
          >
            {{ column.title }}
          </span>
          <span
            :class="[
              'material-icons align-self-end',
              column.name === sortColumn ? '' : 'invisible',
              ascendingSortOrder ? '' : 'rotate-180',
            ]"
            >arrow_circle_down</span
          >
        </div>
        <div v-else>{{ column.title }}</div>
      </th>
    </tr>
  </thead>
</template>

<script>
export default {
  name: "TableHeadBootstrapCustomBS46",
  props: {
    columnsList: Array,
    sortColumn: String,
    ascendingSortOrder: Boolean,
  },
};
</script>

<style lang="scss" scoped>
.sort-link {
  &:hover {
    cursor: pointer;
  }
}
.material-icons {
  transition: all 0.3s ease-in;

  &.invisible {
    transform: scale(0.1);
  }

  &.rotate-180 {
    transform: rotate(-180deg);
  }
}
</style>
