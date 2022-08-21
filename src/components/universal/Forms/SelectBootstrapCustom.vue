<template>
  <label for="inputState" class="form-label">Область</label>
  <select
    id="inputState"
    class="form-select"
    aria-label="Пример выбора по умолчанию"
  >
    <option selected>Откройте это меню выбора</option>
    <option value="1">Один</option>
    <option value="2">Два</option>
    <option value="3">Три</option>
  </select>
</template>

<script>
export default {
  name: "SelectBootstrapCustom",
};
</script>
