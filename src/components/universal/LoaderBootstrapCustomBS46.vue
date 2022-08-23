<template>
  <div class="card pt-2">
    <div class="card-body text-center">
      <div :class="loaderStyleClass" role="status">
        <span class="sr-only">Loading...</span>
      </div>
      <div v-if="loadingComment">{{ loadingComment }}</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "LoaderBootstrapCustomBS46",
  props: {
    loadingComment: String,
    theme: String,
    type: String,
  },
  computed: {
    loaderStyleClass: function () {
      let loaderClass = "";
      if (this.type) {
        loaderClass += "spinner-" + this.type;
      } else {
        loaderClass += "spinner-border";
      }
      if (this.theme) {
        loaderClass += " text-" + this.theme;
      } else {
        loaderClass += " text-primary";
      }
      return loaderClass;
    },
  },
};
</script>
