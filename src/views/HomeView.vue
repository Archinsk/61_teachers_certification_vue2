<template>
  <div id="home">
    <div class="banner text-white text-center py-4">
      <div class="container">
        <div class="text-uppercase font-weight-bold">
          Аттестация педагогических работников
        </div>
        <div>
          организаций, осуществляющих образовательную деятельность и находящихся
          в ведении Иркутской области, муниципальных и частных организаций,
          осуществляющих образовательную деятельность
        </div>
      </div>
    </div>
    <div class="container">
      <div class="row my-0 mb-4">
        <div class="col-12 col-sm-6 mt-4">
          <CardBootstrapCustomBS46 header class="h-100 shadow">
            <template v-slot:card-header>
              <h5 class="text-uppercase mb-0">
                Материалы для работы с системой
              </h5>
            </template>
            <template v-slot:card-body>
              <div v-if="fileResources.length > 0" class="row">
                <div
                  v-for="fileItem of fileResources"
                  :key="fileItem.id"
                  class="col-12 mt-2"
                >
                  <CardBootstrapCustomBS46 class="shadow-sm">
                    <template v-slot:card-body>
                      <div class="d-flex align-items-center">
                        <span class="material-icons text-primary mr-3"
                          >description</span
                        >
                        <a :href="url + fileItem.url.slice(5)" download>
                          {{ fileItem.title }}
                        </a>
                      </div>
                    </template>
                  </CardBootstrapCustomBS46>
                </div>
              </div>
              <div v-else class="alert alert-secondary mb-0" role="alert">
                Материалы для работы с системой ещё не загружены
              </div>
            </template>
          </CardBootstrapCustomBS46>
        </div>
        <div class="col-12 col-sm-6 mt-4">
          <CardBootstrapCustomBS46 header class="h-100 shadow">
            <template v-slot:card-header>
              <h5 class="text-uppercase mb-0">Авторизация</h5>
            </template>
            <template v-slot:card-body>
              <LoaderBootstrapCustomBS46
                v-if="authLoader.isLoading && !authLoader.isResponse"
                :comment="authLoader.comment"
                :theme="authLoader.theme"
              />
              <template v-else>
                <div
                  v-if="
                    authError.type === 'server' || authError.type === 'client'
                  "
                  class="alert alert-danger"
                  role="alert"
                >
                  <b>Ошибка!</b> {{ authError.text }}
                </div>
                <TheSignInFormBS46
                  v-if="!isAuthUser"
                  :sign-data="user.signInData"
                  :auth-error="authError"
                  @sign-in-local="$emit('sign-in-local', $event)"
                  @sign-in-esia="$emit('sign-in-esia', $event)"
                />
                <template v-else>
                  <p>
                    Вы авторизованы как пользователь
                    <b>{{ user.shortInfo.userName }}</b>
                  </p>
                  <p v-if="user.fullInfo.roles.length === 0">
                    У пользователя отсутствуют роли
                  </p>
                  <p
                    v-else-if="
                      user.fullInfo.roles.length === 1 &&
                      user.shortInfo.roleId === user.fullInfo.roles[0].label
                    "
                  >
                    Текущая роль - <b>{{ user.fullInfo.roles[0].label }}</b>
                  </p>
                  <form v-else @submit.prevent>
                    <div class="form-group">
                      <label for="userRoleSelect">Роль пользователя</label>
                      <select
                        v-model="userRoleIdSelected"
                        class="form-control"
                        id="userRoleSelect"
                        @change="$emit('set-role', userRoleIdSelected)"
                      >
                        <option
                          v-if="
                            !userRoleIdSelected ||
                            (user.fullInfo.roles.length === 1 &&
                              user.shortInfo.roleId !==
                                user.fullInfo.roles[0].id)
                          "
                          value=""
                          selected
                          disabled
                        >
                          Выберите роль
                        </option>
                        <option
                          v-for="role of user.fullInfo.roles"
                          :key="role.id"
                          :value="role.id"
                          :selected="role.id === userSelectedRoleId"
                        >
                          {{ role.label }}
                        </option>
                      </select>
                    </div>
                  </form>
                  <button class="btn btn-primary" @click="$emit('sign-out')">
                    Выход
                  </button>
                </template>
              </template>
            </template>
          </CardBootstrapCustomBS46>
        </div>
        <div class="col-12 mt-4">
          <CardBootstrapCustomBS46 header class="h-100 shadow">
            <template v-slot:card-header>
              <h5 class="text-uppercase mb-0">График проведения аттестации</h5>
            </template>
            <template v-slot:card-body>
              <div class="table-responsive">
                <div v-html="expertisesSchedule"></div>
              </div>
            </template>
          </CardBootstrapCustomBS46>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import CardBootstrapCustomBS46 from "../components/universal/CardBootstrapCustomBS46";
import TheSignInFormBS46 from "../components/TheSignInFormBS46";
import LoaderBootstrapCustomBS46 from "../components/universal/LoaderBootstrapCustomBS46";

export default {
  name: "HomeView",
  components: {
    LoaderBootstrapCustomBS46,
    TheSignInFormBS46,
    CardBootstrapCustomBS46,
  },
  props: [
    "url",
    "user",
    "userSelectedRoleId",
    "isAuthUser",
    "authLoader",
    "newsList",
    "authError",
    "messagesTable",
    "fileResources",
    "expertisesSchedule",
  ],
  data() {
    return {
      userRoleIdSelected: "",
    };
  },

  watch: {
    userSelectedRoleId: function () {
      this.userRoleIdSelected = this.userSelectedRoleId;
    },
  },

  mounted: function () {
    this.userRoleIdSelected = this.userSelectedRoleId;
  },
};
</script>
