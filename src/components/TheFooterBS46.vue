<template>
  <footer class="footer bg-light">
    <div class="container">
      <div class="footer-content row">
        <div class="col-12 col-md-4 pb-2"><SiteLogoBS46 /></div>
        <div class="col-12 col-md-8">
          <NavBootstrapCustomBS46
            :nav-list="footerContacts"
            class="flex-column"
          />
        </div>
      </div>
      <hr />
      <div class="footer-copyright text-center">
        &copy; Информационные системы и сервисы, 2022
      </div>
    </div>
  </footer>
</template>

<script>
import NavBootstrapCustomBS46 from "./universal/NavBootstrapCustomBS46";
import SiteLogoBS46 from "./universal/SiteLogoBS46";
export default {
  name: "TheFooterBS46",
  components: { SiteLogoBS46, NavBootstrapCustomBS46 },
  props: ["footerContacts"],
};
</script>

<style lang="scss" scoped>
.logo {
  color: rgba(0, 0, 0, 0.9);
}
</style>
