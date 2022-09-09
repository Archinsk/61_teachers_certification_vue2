<template>
  <div :class="filterClass + ' ' + filterData.responsive">
    <div class="row">
      <InputBootstrapCustomBS46
        :id="filterData.id"
        id-postfix="start"
        :label="filterData.label + filterData.itemsList[0].label"
        :type="filterData.subtype"
        :value="filterData.itemsList[0].value"
        :required="filterData.required || filterData.itemsList[0].required"
        :readonly="filterData.readonly || filterData.itemsList[0].readonly"
        :disabled="filterData.disabled || filterData.itemsList[0].disabled"
        :width-group="6"
      />
      <InputBootstrapCustomBS46
        :id="filterData.id"
        id-postfix="finish"
        :label="filterData.label + filterData.itemsList[1].label"
        :type="filterData.subtype"
        :value="filterData.itemsList[1].value"
        :required="filterData.required || filterData.itemsList[1].required"
        :readonly="filterData.readonly || filterData.itemsList[1].readonly"
        :disabled="filterData.disabled || filterData.itemsList[1].disabled"
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
