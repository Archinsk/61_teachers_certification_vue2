<template>
  <ul class="nav justify-content-end">
    <template v-for="listItem of navList">
      <li v-if="listItem.type === 'link'" :key="listItem.id" class="nav-item">
        <router-link class="nav-link" :to="listItem.link">
          {{ listItem.title }}</router-link
        >
      </li>
      <li
        v-else-if="listItem.type === 'dropdown'"
        :key="listItem.id"
        class="nav-item dropdown"
      >
        <a
          class="nav-link dropdown-toggle"
          href="#"
          role="button"
          data-toggle="dropdown"
          aria-expanded="false"
        >
          {{ listItem.title }}</a
        >
        <div v-if="listItem.items.length > 0" class="dropdown-menu">
          <router-link
            v-for="item of listItem.items"
            :key="item.id"
            class="dropdown-item"
            :to="item.link"
            >{{ item.title }}</router-link
          >
        </div>
      </li>
    </template>

    <slot></slot>
  </ul>
</template>

<script>
export default {
  name: "NavBootstrapCustom46",
  props: ["navList"],
};
</script>
