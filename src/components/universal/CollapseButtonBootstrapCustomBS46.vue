<template>
  <button
    class="btn btn-primary"
    type="button"
    data-toggle="collapse"
    :data-target="'#' + targetId"
    aria-expanded="false"
    :aria-controls="targetId"
  >
    <slot></slot>
  </button>
</template>

<script>
export default {
  name: "CollapseButtonBootstrapCustomBS46",
  props: ["targetId"],
};
</script>
