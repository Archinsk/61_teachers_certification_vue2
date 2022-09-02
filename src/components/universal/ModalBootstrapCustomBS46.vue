<template>
  <div
    class="modal fade"
    :id="modalId"
    tabindex="-1"
    :aria-labelledby="modalId + 'Label'"
    aria-hidden="true"
  >
    <div :class="modalDialogClass">
      <div class="modal-content">
        <div v-if="header" class="modal-header">
          <h5 class="modal-title" :id="modalId + 'Label'">
            <slot name="modal-title"></slot>
          </h5>
          <button
            type="button"
            class="close"
            data-dismiss="modal"
            aria-label="Close"
          >
            <span aria-hidden="true">&times;</span>
          </button>
        </div>
        <div class="modal-body">
          <slot name="modal-body"></slot>
        </div>
        <div v-if="footer" class="modal-footer">
          <button type="button" class="btn btn-secondary" data-dismiss="modal">
            Отмена
          </button>
          <button type="button" class="btn btn-primary">ОК</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ModalBootstrapCustomBS46",
  props: {
    modalId: String,
    header: Boolean,
    noBody: Boolean,
    footer: Boolean,
    scrollable: Boolean,
    verticalCenter: Boolean,
    size: String,
  },
  computed: {
    modalDialogClass: function () {
      let modalDialogClass = "modal-dialog";
      if (this.scrollable) {
        modalDialogClass += " modal-dialog-scrollable";
      }
      if (this.verticalCenter) {
        modalDialogClass += " modal-dialog-centered";
      }
      if (this.size && this.size !== "md") {
        modalDialogClass += " modal-" + this.size;
      }
      return modalDialogClass;
    },
  },
};
</script>

<style scoped></style>
