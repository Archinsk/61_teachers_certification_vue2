<template>
  <FormGroupBootstrapCustomBS46
    :width-group="widthGroup"
    :responsive="responsive"
  >
    <label :for="id" class="form-label">{{ label }}</label>
    <input
      :type="type"
      class="form-control"
      :id="idFull"
      :value="value"
      :required="required"
      :readonly="readonly"
      :disabled="disabled"
      v-model="inputValue"
      @input="$emit('input', inputValue)"
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
    id: String,
    idPostfix: String,
    value: String,
    widthGroup: Number,
    responsive: String,
    required: Boolean,
    readonly: Boolean,
    disabled: Boolean,
  },
  data() {
    return {
      inputValue: "",
    };
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
  created() {
    this.inputValue = this.value;
  },
  watch: {
    value: function () {
      this.inputValue = this.value;
    },
  },
};
</script>
