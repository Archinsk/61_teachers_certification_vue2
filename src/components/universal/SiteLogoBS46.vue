<template>
  <router-link to="/" class="logo d-flex align-items-center">
    <img src="@/assets/iss_logo.svg" alt="" class="logo__image" />
    <div class="logo__text ml-2 font-weight-bold">Логотип</div>
  </router-link>
</template>

<script>
export default {
  name: "SiteLogoBS46",
};
</script>

<style lang="scss" scoped>
.logo {
  text-decoration: none;
  .logo__image {
    height: 1.875rem;
  }

  .logo__text {
    font-size: 1rem;
  }
}
</style>
