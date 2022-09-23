<template>
  <FormGroupBootstrapCustomBS46
    :width-group="widthGroup"
    :responsive="responsive"
  >
    <label :for="id">{{ label }}</label>
    <select
      v-if="!badges"
      :id="id"
      class="form-control"
      :required="required"
      :disabled="disabled"
      :multiple="multiple"
      v-model="selectedValue"
      @change="$emit('change', selectedValue)"
    >
      <option disabled :selected="!selectedValue">Выберите...</option>
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
    <div
      v-else
      :class="['form-control', { 'disabled-multiple': disabled }]"
      :id="id"
      :required="required"
      disabled
    >
      <span
        v-if="this.itemsList.length > this.selectedValue.length && !disabled"
        class="dropdown"
      >
        <a
          class="btn btn-sm dropdown-toggle"
          href="#"
          role="button"
          data-toggle="dropdown"
          aria-expanded="false"
          :disabled="disabled"
        >
          Выберите...
        </a>
        <div class="dropdown-menu">
          <template v-for="option of itemsList">
            <a
              v-if="!selectedValue.includes(+option.value)"
              :key="option.id"
              class="dropdown-item"
              href="#"
              @click.prevent="addItemToMultipleSelect(+option.value)"
              >{{ option.label }}</a
            >
          </template>
        </div>
      </span>
      <template v-for="(option, index) of itemsList">
        <span
          v-if="selectedValue.includes(+option.value)"
          :key="index"
          class="badge badge-light mr-1"
          >{{ option.label
          }}<span
            v-if="!disabled"
            class="btn material-icons"
            @click="removeItemFromMultipleSelect(+option.value)"
            >close</span
          ></span
        >
      </template>
    </div>
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
    value: [String, Array, Number],
    widthGroup: Number,
    responsive: String,
    required: Boolean,
    disabled: Boolean,
    multiple: Boolean,
    badges: Boolean,
  },
  data() {
    return {
      selectedValue: "",
      itemsForMultiple: "",
    };
  },

  methods: {
    addItemToMultipleSelect(itemValue) {
      this.selectedValue.push(itemValue);
      this.$emit("change", this.selectedValue);
    },
    removeItemFromMultipleSelect(itemValue) {
      let indexOfRemovedItem = this.selectedValue.indexOf(itemValue);
      this.selectedValue.splice(indexOfRemovedItem, 1);
      this.$emit("change", this.selectedValue);
    },
  },

  created() {
    this.selectedValue = this.value;
    if (Number.isFinite(this.value)) {
      let arr = [];
      arr.push(this.value);
      this.selectedValue = arr;
    }
  },

  watch: {
    value: function () {
      this.selectedValue = this.value;
    },
  },
};
</script>

<style lang="scss" scoped>
.disabled-multiple {
  background-color: #e9ecef;
  opacity: 1;
  cursor: default;
  min-height: calc(1.5em + 0.75rem + 2px);
}

.form-control {
  height: unset;
}
.dropdown-toggle {
  margin-top: -0.375rem;
  padding-top: 0px;
  padding-bottom: 0px;
  border: none;
}
.badge {
  margin-top: -0.25rem;
  font-size: 87.5%;
  font-weight: 400;

  .material-icons {
    font-size: 1rem;
    line-height: 0.75;
    margin-left: 0.25rem;
    padding: 0;
    border: none;
  }
}
</style>
