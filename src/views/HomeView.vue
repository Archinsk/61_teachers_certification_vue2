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
          <CardBootstrapCustom header class="h-100 shadow">
            <template v-slot:card-header>
              <h5 class="text-uppercase mb-0">
                Материалы для работы с системой
              </h5>
            </template>
            <template v-slot:card-body>
              <div class="row">
                <div class="col-12 mt-2">
                  <CardBootstrapCustom class="shadow-sm">
                    <template v-slot:card-body>
                      <a href="/">
                        <span class="material-icons">description</span>
                        Руководство администратора.doc
                      </a>
                    </template>
                  </CardBootstrapCustom>
                </div>
                <div class="col-12 mt-2">
                  <CardBootstrapCustom class="shadow-sm">
                    <template v-slot:card-body>
                      <a href="/">
                        <span class="material-icons">description</span>
                        Руководство пользователя.doc
                      </a>
                    </template>
                  </CardBootstrapCustom>
                </div>
              </div>
            </template>
          </CardBootstrapCustom>
        </div>
        <div class="col-12 col-sm-6 mt-4">
          <CardBootstrapCustom header class="h-100 shadow">
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
                  <b>Произошла ошибка!</b> {{ authError.text }}
                </div>
                <TheSignInFormBS46
                  v-if="!isAuthUser"
                  :auth-error="authError"
                  @sign-in-local="$emit('sign-in-local', $event)"
                  @sign-in-esia="$emit('sign-in-esia', $event)"
                />
                <template v-else>
                  <p>
                    Вы авторизованы как пользователь
                    <b>{{ user.shortInfo.userName }}</b>
                  </p>
                  <p v-if="user.fullInfo.roles.length <= 1">
                    У пользователя отсутствуют роли
                  </p>
                  <p v-else-if="user.fullInfo.roles.length === 1">
                    Текущая роль - <b>{{ user.fullInfo.roles[0].label }}</b>
                  </p>

                  <form
                    v-else-if="user.fullInfo.roles.length >= 2"
                    @submit.prevent
                  >
                    <div class="form-group">
                      <label for="userRoleSelect">Роль пользователя</label>
                      <select
                        v-model="userRoleIdSelected"
                        class="form-control"
                        id="userRoleSelect"
                        @change="$emit('set-role', userRoleIdSelected)"
                      >
                        <option
                          v-for="role of user.fullInfo.roles"
                          :key="role.id"
                          :value="role.id"
                          :selected="role.id === userRoleIdSelected"
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
          </CardBootstrapCustom>
        </div>
        <div class="col-12 mt-4">
          <CardBootstrapCustom header class="h-100 shadow">
            <template v-slot:card-header>
              <h5 class="text-uppercase mb-0">Новости</h5>
            </template>
            <template v-slot:card-body>
              <div class="row">
                <div
                  class="news col"
                  v-for="listItem of newsList"
                  :key="listItem.id"
                >
                  <h5>{{ listItem.title }}</h5>
                  <p>{{ listItem.text }}</p>
                  <p>{{ listItem.datePublication }}</p>
                </div>
              </div>
            </template>
          </CardBootstrapCustom>
        </div>
        <div class="col-12 mt-4">
          <CardBootstrapCustom header class="h-100 shadow">
            <template v-slot:card-header>
              <h5 class="text-uppercase mb-0">График проведения аттестации</h5>
            </template>
            <template v-slot:card-body>
              <p>Аттестация производится</p>
              <p>ПН: 09:00 - 18:00</p>
              <p>ВТ: 09:00 - 18:00</p>
              <p>СР: 09:00 - 18:00</p>
              <p>ЧТ: 09:00 - 18:00</p>
              <p>ПТ: 09:00 - 18:00</p>
              <p>СБ: 09:00 - 12:00</p>
              <p>ВС: Выходной</p>
            </template>
          </CardBootstrapCustom>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import CardBootstrapCustom from "../components/universal/CardBootstrapCustom";
import TheSignInFormBS46 from "../components/TheSignInFormBS46";
import LoaderBootstrapCustomBS46 from "../components/universal/LoaderBootstrapCustomBS46";

export default {
  name: "HomeView",
  components: {
    LoaderBootstrapCustomBS46,
    TheSignInFormBS46,
    CardBootstrapCustom,
  },
  props: ["url", "user", "isAuthUser", "authLoader", "newsList", "authError"],
  data() {
    return {
      userRoleIdSelected: this.user.selectedRole.id,
    };
  },
  updated() {
    this.userRoleIdSelected = this.user.selectedRole.id;
  },
};
</script>
