<template>
  <div class="collapse" :id="id"><slot></slot></div>
</template>

<script>
export default {
  name: "CollapseBootstrapCustomBS46",
  props: ["id"],
};
</script>
