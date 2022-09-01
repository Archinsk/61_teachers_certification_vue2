<template>
  <div :class="filterClass">
    <div class="row">
      <InputBootstrapCustomBS46
        :id="filterData.id"
        id-postfix="start"
        :label="filterData.label + '. ' + filterData.itemsList[0].label"
        :type="filterData.subtype"
        :width-group="6"
      />
      <InputBootstrapCustomBS46
        :id="filterData.id"
        id-postfix="finish"
        :label="filterData.label + '. ' + filterData.itemsList[1].label"
        :type="filterData.subtype"
        :width-group="6"
      />
    </div>
  </div>
</template>

<script>
import InputBootstrapCustomBS46 from "./Forms/InputBootstrapCustomBS46";
export default {
  name: "FilterRangeBootstrapCustomBS46",
  components: { InputBootstrapCustomBS46 },
  props: {
    filterData: Object,
  },
  computed: {
    filterClass: function () {
      let filterClass = "form-group mb-0 col";
      if (this.filterData.width) {
        filterClass += "-" + this.filterData.width;
      }
      return filterClass;
    },
  },
};
</script>
