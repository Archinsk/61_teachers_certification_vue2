<template>
  <header
    :class="[
      'header bg-' + variant,
      { 'sticky-top': sticky },
      { shadow: shadow },
    ]"
  >
    <div class="container">
      <nav
        :class="[
          'navbar',
          expandClass,
          isDarkTheme ? 'navbar-dark' : 'navbar-light',
        ]"
      >
        <SiteLogoBS46 class="navbar-brand" />

        <button
          class="navbar-toggler btn-icon-square"
          type="button"
          data-toggle="collapse"
          data-target="#navbarCollapse"
          aria-controls="navbarCollapse"
        >
          <span class="material-icons">menu</span>
        </button>

        <div class="collapse navbar-collapse" id="navbarCollapse">
          <NavBootstrapCustomBS46 :nav-list="navbarItems" class="navbar-nav">
          </NavBootstrapCustomBS46>
        </div>
      </nav>
    </div>
  </header>
</template>

<script>
import NavBootstrapCustomBS46 from "./universal/NavBootstrapCustomBS46";
import SiteLogoBS46 from "./universal/SiteLogoBS46";

export default {
  name: "TheHeaderBS46",
  components: {
    SiteLogoBS46,
    NavBootstrapCustomBS46,
  },
  props: {
    variant: String,
    navbarItems: Array,
    sticky: Boolean,
    shadow: Boolean,
    expand: Boolean,
    expandWidthPoint: String,
  },

  computed: {
    isDarkTheme() {
      const darkVariants = [
        "primary",
        "secondary",
        "success",
        "danger",
        "dark",
      ];
      return darkVariants.includes(this.variant);
    },
    expandClass() {
      let expandClass = "";
      if (this.expand) {
        expandClass += "navbar-expand";
        if (this.expandWidthPoint) {
          expandClass += "-" + this.expandWidthPoint;
        }
      }
      return expandClass;
    },
  },
};
</script>
