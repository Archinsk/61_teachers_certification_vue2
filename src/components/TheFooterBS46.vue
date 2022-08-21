<template>
  <footer class="footer bg-light">
    <div class="container">
      <div class="footer-content row">
        <div class="col-4"><SiteLogoBS46 /></div>
        <div class="col-8">
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

<style scoped></style>
