<template>
  <div :class="groupClass"><slot></slot></div>
</template>

<script>
export default {
  name: "FormGroupBootstrapCustomBS46",
  props: {
    widthGroup: Number,
  },
  computed: {
    groupClass: function () {
      let groupClass = "form-group";
      if (this.widthGroup) {
        groupClass += " col-" + this.widthGroup;
      } else {
        groupClass += " col";
      }
      return groupClass;
    },
  },
};
</script>
