<template>
  <form class="row g-3">
    <template v-for="filterItem of filterData" :key="filterItem.name">
      <div class="col-6">
        <InputBootstrapCustom
          v-if="filterItem.type === 'input'"
          :type="filterItem.subtype"
          :id="qwe"
        />
        <SelectBootstrapCustom
          v-if="filterItem.type === 'select'"
          :label="filterItem.name"
          :options-list="filterItem"
        />
      </div>
    </template>
  </form>
</template>

<script>
import InputBootstrapCustom from "./Forms/InputBootstrapCustom";
import SelectBootstrapCustom from "./Forms/SelectBootstrapCustom";
export default {
  name: "FilterBootstrapCustom",
  components: { SelectBootstrapCustom, InputBootstrapCustom },
  props: ["filterData"],
};
</script>
