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
      :readonly="readonly"
      :disabled="disabled"
    >
      <option disabled>Выберите...</option>
      <template v-if="itemsList.length > 0">
        <option
          v-for="(option, index) of itemsList"
          :value="option.id + '-' + index"
          :key="option.id + '-' + index"
          :selected="option.selected"
        >
          {{ option.text }}
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
    id: Number,
    widthGroup: Number,
    responsive: String,
    itemsList: Array,
    required: Boolean,
    disabled: Boolean,
  },
};
</script>
