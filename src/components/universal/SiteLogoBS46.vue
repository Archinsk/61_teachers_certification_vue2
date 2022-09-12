<template>
  <router-link to="/" class="logo d-flex align-items-center">
    <img src="images/logo.svg" alt="" class="logo__image" />
    <div class="logo__text font-weight-bold">
      Министерство образования Иркутской области
    </div>
  </router-link>
</template>

<script>
export default {
  name: "SiteLogoBS46",
};
</script>

<style lang="scss" scoped>
.logo {
  font-size: 1rem;
  line-height: 0.9375;
  white-space: normal;
  margin-right: 0;

  &:hover {
    text-decoration: none;
  }

  @media (max-width: 991.98px) {
    width: calc(100% - 6.25rem);
  }

  .logo__image {
    height: 1.875rem;
    margin-right: 1rem;
  }
}
</style>
