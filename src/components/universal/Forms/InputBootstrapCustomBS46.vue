<template>
  <FormGroupBootstrapCustomBS46 :width-group="widthGroup">
    <label :for="id" class="form-label">{{ label }}</label>
    <input
      :type="type"
      class="form-control"
      :id="idFull"
      :value="value"
      :required="required"
      :readonly="readonly"
      :disabled="disabled"
    />
  </FormGroupBootstrapCustomBS46>
</template>

<script>
import FormGroupBootstrapCustomBS46 from "./FormGroupBootstrapCustomBS46";
export default {
  name: "InputBootstrapCustomBS46",
  components: { FormGroupBootstrapCustomBS46 },
  props: {
    label: String,
    type: String,
    id: Number,
    idPostfix: String,
    value: String,
    widthGroup: Number,
    required: Boolean,
    readonly: Boolean,
    disabled: Boolean,
  },
  computed: {
    idFull: function () {
      let idFull = this.id;
      if (this.idPostfix) {
        idFull += "-" + this.idPostfix;
      }
      return idFull;
    },
  },
};
</script>
