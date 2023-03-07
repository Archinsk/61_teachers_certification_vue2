<template>
  <form @submit.prevent>
    <div class="form-group mb-3">
      <label for="inputLogin">Экранное имя</label>
      <input
        autocomplete="on"
        v-model.trim="signInData.login"
        type="text"
        id="inputLogin"
        class="form-control"
      />
      <small
        v-if="authError.type === 'login'"
        id="emailHelp"
        class="form-text text-danger"
      >
        {{ authError.text }}
      </small>
    </div>
    <div class="form-group mb-3">
      <label for="inputPassword" class="form-label">Пароль</label>
      <div class="input-group">
        <input
          autocomplete="off"
          v-model.trim="signInData.password"
          :type="passwordVisibility ? 'text' : 'password'"
          id="inputPassword"
          class="form-control"
          aria-describedby="password-visibility-button"
        />
        <div class="input-group-append">
          <button
            id="password-visibility-button"
            type="button"
            class="btn btn-outline-secondary visibility-button"
            @click="passwordVisibility = !passwordVisibility"
          >
            <span class="material-icons">
              {{ passwordVisibility ? "visibility_off" : "visibility" }}
            </span>
          </button>
        </div>
      </div>
      <small v-if="authError.type === 'password'" class="form-text text-danger">
        {{ authError.text }}
      </small>
    </div>
    <a href="#" class="d-block mt-2 mb-3">Забыли пароль?</a>
    <button
      class="btn btn-primary"
      :disabled="!signInData.login || !signInData.password"
      @click="$emit('sign-in-local', signInData)"
    >
      Войти
    </button>
    <!--    <button class="btn btn-outline-primary btn-block">Отмена</button>-->
    <hr />
    <p class="mb-3">Авторизоваться через портал государственных услуг</p>
    <button
      class="btn btn-primary"
      :disabled="!appLoaded || !esiaLink"
      @click="$emit('sign-in-esia', signInData)"
    >
      <img
        src="../assets/gu_icon.svg"
        style="height: 1.5rem; padding: 0.125rem"
        class="mr-2"
        alt=""
      />Войти с помощью ЕСИА
    </button>
  </form>
</template>

<script>
export default {
  name: "TheSignInFormBS46",
  props: ["appLoaded", "esiaLink", "signData", "authError"],

  data() {
    return {
      signInData: {
        login: this.signData.login,
        password: this.signData.password,
      },
      passwordVisibility: false,
    };
  },
};
</script>

<style lang="scss" scoped>
.visibility-button {
  line-height: 0.75rem;
  padding: 0.375rem;
}
</style>
