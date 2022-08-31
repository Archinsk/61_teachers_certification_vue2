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

        <div class="collapse navbar-collapse" id="navbarCollapse">
          <NavBootstrapCustomBS46
            :nav-list="navbarItems"
            :is-auth-user="isAuthUser"
            class="navbar-nav"
          />
        </div>

        <div class="d-flex">
          <button
            v-if="isAuthUser && messages"
            type="button"
            class="btn btn-outline-secondary position-relative btn-icon-only_square mr-4"
            data-toggle="modal"
            data-target="#messages"
          >
            <!--            @click="$emit('show-messages-list')"-->
            <span class="material-icons">mail</span
            ><span
              v-if="unreadMessages"
              class="badge badge-pill badge-danger position-absolute"
              >{{ unreadMessages }}</span
            >
          </button>
          <button
            class="navbar-toggler btn-icon-square"
            type="button"
            data-toggle="collapse"
            data-target="#navbarCollapse"
            aria-controls="navbarCollapse"
          >
            <span class="material-icons">menu</span>
          </button>
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
    isAuthUser: Boolean,
    messages: Boolean,
    unreadMessages: Number,
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
