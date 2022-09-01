<template>
  <FormGroupBootstrapCustomBS46 :width-group="widthGroup">
    <label :for="id">{{ label }}</label>
    <select :id="id" class="form-control">
      <option selected disabled>Выберите...</option>
      <template v-if="itemsList.length > 0">
        <option
          v-for="(option, index) of itemsList"
          :value="option.id + '-' + index"
          :key="option.id + '-' + index"
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
    itemsList: Array,
    readonly: Boolean,
    disabled: Boolean,
  },
};
</script>
