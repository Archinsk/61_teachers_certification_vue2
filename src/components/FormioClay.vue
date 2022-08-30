<template>
  <div>
    <div class="col-6 form-group">
      <label for="lastName222" class="form-label">Фамилия</label
      ><input type="text" id="lastName222" class="form-control" />
    </div>
    <Form :form="form" :submission="submission" :options="options" />
  </div>
</template>

<script>
import { Form } from "vue-formio";
export default {
  name: "FormioClay",
  props: ["form", "submission", "options"],
  components: { Form },
};
</script>

<style lang="scss" scoped>
.form-control {
  border-color: blue;
}
</style>
