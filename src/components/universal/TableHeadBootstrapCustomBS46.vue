<template>
  <thead>
    <tr>
      <th
        v-for="(column, indexColumn) of columnsList"
        :key="indexColumn"
        scope="col"
      >
        <div class="d-flex justify-content-between">
          <span
            class="pr-2 sort-link"
            @click="$emit('sort-table', indexColumn)"
          >
            {{ column }}
          </span>
          <span
            :class="[
              'material-icons align-self-end',
              column === sortColumn ? '' : 'invisible',
              ascendingSortOrder ? '' : 'rotate-180',
            ]"
            >arrow_circle_down</span
          >
          <!--          <template v-if="column === sortColumn">-->
          <!--            <span v-if="ascendingSortOrder" class="material-icons"-->
          <!--              >arrow_circle_down</span-->
          <!--            >-->
          <!--            <span v-else class="material-icons">arrow_circle_up</span>-->
          <!--          </template>-->
        </div>
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
