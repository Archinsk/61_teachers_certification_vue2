<template>
  <div :class="groupClass + ' ' + responsive"><slot></slot></div>
</template>

<script>
export default {
  name: "FormGroupBootstrapCustomBS46",
  props: {
    widthGroup: Number,
    responsive: String,
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
