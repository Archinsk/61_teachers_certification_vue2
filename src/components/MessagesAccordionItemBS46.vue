<template>
  <div class="card">
    <div class="card-header" :id="'heading-' + message.id">
      <h2 class="mb-0">
        <div class="d-flex">
          <button
            class="btn btn-link btn-block pl-0 text-left"
            type="button"
            data-toggle="collapse"
            :data-target="'#collapse-' + message.id"
            aria-expanded="true"
            :aria-controls="'collapse-' + message.id"
            @click="$emit('read-message')"
          >
            {{ message.messageTitle + message.id }}
          </button>
          <transition name="fade-out">
            <div
              v-if="!message.wasRead"
              class="btn btn-icon-only_square btn-fade"
            >
              <span class="material-icons text-muted">mail</span>
            </div>
          </transition>
          <button
            class="btn btn-icon-only_square"
            @click="$emit('delete-message')"
          >
            <span class="material-icons">close</span>
          </button>
        </div>
      </h2>
      <div class="small text-muted">
        Получено {{ receiptDate }} в
        {{ receiptTime }}
      </div>
    </div>

    <div
      :id="'collapse-' + message.id"
      class="collapse"
      :aria-labelledby="'heading-' + message.id"
      data-parent="#accordionMessages"
    >
      <div class="card-body">
        {{ message.messageText }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "MessagesAccordionItemBS46",
  props: ["message"],
  computed: {
    receiptDate: function () {
      const options = {};
      options.day = "2-digit";
      options.month = "2-digit";
      options.year = "numeric";
      return new Intl.DateTimeFormat("ru-RU", options).format(
        new Date(Date.parse(this.message.receiptDate))
      );
    },
    receiptTime: function () {
      const options = {};
      options.hour = "2-digit";
      options.minute = "2-digit";
      options.second = "2-digit";
      return new Intl.DateTimeFormat("ru-RU", options).format(
        new Date(Date.parse(this.message.receiptDate))
      );
    },
  },
};
</script>
