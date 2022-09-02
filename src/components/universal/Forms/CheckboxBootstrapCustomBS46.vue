<template>
  <FormGroupBootstrapCustomBS46 :width-group="widthGroup">
    <div class="form-check">
      <input
        type="checkbox"
        class="form-check-input"
        :id="id"
        :checked="value"
        :required="required"
        :disabled="disabled"
      />
      <label class="form-check-label" :for="id">{{ label }}</label>
    </div>
  </FormGroupBootstrapCustomBS46>
</template>

<script>
import FormGroupBootstrapCustomBS46 from "./FormGroupBootstrapCustomBS46";
export default {
  name: "CheckboxBootstrapCustomBS46",
  components: { FormGroupBootstrapCustomBS46 },
  props: {
    id: Number,
    label: String,
    value: Boolean,
    widthGroup: Number,
    required: Boolean,
    readonly: Boolean,
    disabled: Boolean,
  },
};
</script>
