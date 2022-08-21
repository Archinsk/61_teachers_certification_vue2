<template>
  <tbody>
    <tr
      v-for="(row, indexRow) of rowsList"
      :key="primaryColumnIndex ? primaryColumnIndex : indexRow"
      @click="$emit('row-click', row[primaryColumnIndex])"
    >
      <td v-for="(cell, indexCell) of row" :key="indexCell">{{ cell }}</td>
    </tr>
  </tbody>
</template>

<script>
export default {
  name: "TableBodyBootstrapCustom",
  props: ["rowsList", "primaryColumnIndex"],
};
</script>

<style scoped></style>
