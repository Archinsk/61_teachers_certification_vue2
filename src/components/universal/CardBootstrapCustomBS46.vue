<template>
  <div class="card">
    <div v-if="header" class="card-header">
      <slot name="card-header"></slot>
    </div>
    <div v-if="!noBody" class="card-body">
      <slot name="card-body"></slot>
    </div>
    <div v-if="footer" class="card-footer text-muted">
      <slot name="card-footer"></slot>
    </div>
  </div>
</template>

<script>
export default {
  name: "CardBootstrapCustomBS46",
  props: {
    cardData: Object,
    header: Boolean,
    noBody: Boolean,
    footer: Boolean,
  },
};
</script>

<style scoped></style>
