<template>
  <label :for="id" class="form-label">Фамилия</label>
  <input
    :type="type"
    class="form-control"
    :id="id"
    :readonly="readonly"
    :disabled="disabled"
  />
</template>

<script>
export default {
  name: "InputBootstrapCustom",
  props: {
    type: String,
    id: String,
    readonly: Boolean,
    disabled: Boolean,
  },
};
</script>
