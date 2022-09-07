<template>
  <FormGroupBootstrapCustomBS46
    :width-group="widthGroup"
    :responsive="responsive"
  >
    <label :for="id">{{ label }}</label>
    <select
      :id="id"
      class="form-control"
      :required="required"
      :disabled="disabled"
      v-model="selectedValue"
      @change="$emit('change', selectedValue)"
    >
      <option disabled>Выберите...</option>
      <template v-if="itemsList.length > 0">
        <option
          v-for="option of itemsList"
          :value="option.value"
          :key="option.id"
          :selected="option.value === selectedValue"
        >
          {{ option.label }}
        </option>
      </template>
    </select>
  </FormGroupBootstrapCustomBS46>
</template>

<script>
import FormGroupBootstrapCustomBS46 from "./FormGroupBootstrapCustomBS46";
export default {
  name: "SelectBootstrapCustomBS46",
  components: { FormGroupBootstrapCustomBS46 },
  props: {
    label: String,
    id: String,
    itemsList: Array,
    value: String,
    widthGroup: Number,
    responsive: String,
    required: Boolean,
    disabled: Boolean,
  },
  data() {
    return {
      selectedValue: "",
    };
  },
  created() {
    this.selectedValue = this.value;
  },
  watch: {
    value: function () {
      this.selectedValue = this.value;
    },
  },
};
</script>
