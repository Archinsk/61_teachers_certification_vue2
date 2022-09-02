<template>
  <form class="row g-3">
    <template v-for="filterItem of filterData">
      <InputBootstrapCustomBS46
        v-if="filterItem.type === 'input'"
        :key="filterItem.id"
        :id="filterItem.id"
        :label="filterItem.label"
        :type="filterItem.subtype"
        :value="filterItem.value"
        :required="filterItem.required"
        :readonly="filterItem.readonly"
        :disabled="filterItem.disabled"
        :width-group="filterItem.width"
        :responsive="filterItem.responsive"
      />
      <SelectBootstrapCustomBS46
        v-if="filterItem.type === 'select'"
        :key="filterItem.id"
        :id="filterItem.id"
        :label="filterItem.label"
        :items-list="filterItem.itemsList"
        :required="filterItem.required"
        :disabled="filterItem.disabled"
        :width-group="filterItem.width"
        :responsive="filterItem.responsive"
      />
      <CheckboxBootstrapCustomBS46
        v-if="filterItem.type === 'checkbox'"
        :key="filterItem.id"
        :id="filterItem.id"
        :label="filterItem.label"
        :value="filterItem.value"
        :required="filterItem.required"
        :disabled="filterItem.disabled"
        :width-group="filterItem.width"
        :responsive="filterItem.responsive"
      />
      <FilterRangeBootstrapCustomBS46
        v-if="filterItem.type === 'range'"
        :key="filterItem.id"
        :filter-data="filterItem"
      />
    </template>
    <div class="col-12">
      <div class="row form-group">
        <div class="col">
          <div class="btn btn-primary mr-2">Применить</div>
          <div class="btn btn-outline-secondary">Очистить</div>
        </div>
      </div>
    </div>
  </form>
</template>

<script>
import InputBootstrapCustomBS46 from "./Forms/InputBootstrapCustomBS46";
import SelectBootstrapCustomBS46 from "./Forms/SelectBootstrapCustomBS46";
import CheckboxBootstrapCustomBS46 from "./Forms/CheckboxBootstrapCustomBS46";
import FilterRangeBootstrapCustomBS46 from "./FilterRangeBootstrapCustomBS46";
export default {
  name: "FilterBootstrapCustomBS46",
  components: {
    FilterRangeBootstrapCustomBS46,
    CheckboxBootstrapCustomBS46,
    SelectBootstrapCustomBS46,
    InputBootstrapCustomBS46,
  },
  props: ["filterData"],
};
</script>
