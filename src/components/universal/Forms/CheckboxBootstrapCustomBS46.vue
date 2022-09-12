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
        v-model="inputValue"
        @change="$emit('change', inputValue)"
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
    id: String,
    label: String,
    value: Boolean,
    widthGroup: Number,
    required: Boolean,
    readonly: Boolean,
    disabled: Boolean,
  },
  data() {
    return {
      inputValue: false,
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
