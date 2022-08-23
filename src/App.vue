<template>
  <div id="app">
    <TheHeaderBS46
      :navbar-items="headerNavbarList"
      :is-auth-user="isAuthUser"
      variant="light"
      expand
      expand-width-point="lg"
      sticky
      shadow
    />
    <main>
      <router-view
        :news-list="newsList"
        :url="url"
        :user="user"
        :is-auth-user="isAuthUser"
        :auth-error="authError"
        :auth-loader="authLoader"
        :apps-table="appsTable"
        :app-form="appForm"
        :app-form-options="formOptions"
        :messages-table="messagesTable"
        :message-form="messageForm"
        :message-form-options="formOptions"
        @sign-in-local="signInLocal($event)"
        @sign-in-esia="getLogin"
        @sign-out="signOut"
        @create-new-app="getStartForm(appsServiceId)"
        @open-existing-app="getStartForm(appsServiceId, $event)"
        @create-new-message="getStartForm(messagesServiceId)"
        @open-existing-message="getStartForm(messagesServiceId, $event)"
        @change-message-page-size="
          changePageSize(messagesTable, $event, messagesServiceId)
        "
        @change-message-page="
          changePage(messagesTable, $event, messagesServiceId)
        "
        @invoke-action="invokeAction($event)"
      />
    </main>
    <TheFooterBS46 :footer-contacts="footerContacts" />
  </div>
</template>

<script>
import axios from "axios";
import TheHeaderBS46 from "./components/TheHeaderBS46";
import TheFooterBS46 from "./components/TheFooterBS46";
import { Modal } from "bootstrap";

export default {
  name: "TheApp",
  components: {
    TheFooterBS46,
    TheHeaderBS46,
  },
  props: ["unreadMessages"],

  data() {
    return {
      url: "https://teachers.coko38.ru/api/",
      user: {
        shortInfo: {
          userId: null,
          userName: "",
          roleId: null,
          orgId: null,
          typeAuth: "",
        },
        fullInfo: {
          roles: [],
        },
        selectedRole: {
          id: null,
          key: "",
          label: "",
        },
      },
      isFirstLoad: true,
      esiaLink: "",
      esiaLogoutLink: "",
      userInfoFromResponse: {
        shortInfo: {
          userId: null,
          userName: "",
          typeAuth: "",
        },
        fullInfo: {
          roles: [],
        },
        selectedRole: {},
      },
      authError: {
        type: "",
        text: "",
      },
      authLoader: {
        isLoading: false,
        isResponse: false,
        comment: "",
      },

      // Заявления
      appsServiceId: 2,
      appsTable: {
        columnsList: [
          "№ сообщения",
          "Наименование услуги",
          "Дата создания",
          "Номер ЕПГУ",
          "Статус",
          "Дата изменения статуса",
        ],
        primaryColumn: "№ сообщения",
        rowsList: [
          [
            "0001",
            "Аттестация педагогических работников",
            "01.08.2022",
            "540118",
            "В работе",
            "05.08.2022",
          ],
          [
            "0002",
            "Аттестация педагогических работников",
            "05.08.2022",
            "540115",
            "В работе",
            "05.08.2022",
          ],
          [
            "0003",
            "Аттестация педагогических работников",
            "09.08.2022",
            "540120",
            "Черновик",
            "10.08.2022",
          ],
        ],
        sortColumn: "",
        ascendingSortOrder: false,
        filters: [],
        pagination: {
          itemsTotal: 0,
          page: 1,
          pageSize: 10,
          itemsPerPage: [10, 25, 50],
        },
      },

      // Сообщения
      messagesServiceId: 1,
      messagesTable: {
        columnsList: [
          "№ сообщения",
          "Дата создания сообщения",
          "Тема",
          "Дата входа в статус",
          "Статус",
          "Срок отправки ответа",
        ],
        primaryColumn: "№ сообщения",
        rowsList: [
          [
            "0123",
            "01.08.2022",
            "Детали аттестации",
            "07.08.2022",
            "В работе",
            "05.08.2022",
          ],
          [
            "0125",
            "05.08.2022",
            "Аттестация педагогических работников",
            "05.08.2022",
            "В работе",
            "07.08.2022",
          ],
          [
            "0128",
            "09.08.2022",
            "Аттестация педагогических работников",
            "03.08.2022",
            "Черновик",
            "10.08.2022",
          ],
        ],
        sortColumn: "",
        ascendingSortOrder: false,
        filters: [],
        pagination: {
          itemsTotal: 0,
          page: 1,
          pageSize: 10,
          itemsPerPage: [10, 25, 50],
        },
      },

      // Текущее заявление
      appForm: {
        active: false,
        data: {},
        form: {
          actions: [],
          id: 0,
          modelId: 0,
          scheme: {},
        },
        id: 0,
        orderId: "",
        status: "",
      },
      messageForm: {
        active: false,
        data: {},
        form: {
          actions: [],
          id: 0,
          modelId: 0,
          scheme: {},
        },
        id: 0,
        orderId: "",
        status: "",
      },
      formOptions: {
        i18n: {
          lng: "ru",
          ru: {
            Name: "Имя",
            "Last name": "Фамилия",
            dict: "Тип заявителя",
            "Type to search": "Поиск...",
            "Last name is required": "Фамилия - это обязательное поле",
            "No results found": "Поиск не дал результатов",
            "is required": "обязательное поле",
            Number: "Число",
            Submit: "Подтвердить",
            Layout: "Расположение",
            "Drag and Drop a form component": "Переместите компонент сюда",
            "No Matches Found": "Ничего не найдено",
            "Text Field": "Текстовое поле",
            Email: "Электронная почта",
            "Text Area": "Текстовая область",
            "Phone Number": "Номер телефона",
            Checkbox: "Флажок",
            Select: "Выпадающий список",
            Radio: "Радио кнопка",
            Url: "Ссылка",
            "Data Map": "Ключ - Значение",
            "Data Grid": "Динамический список",
            "Edit Grid": "Сетка данных",
            Table: "Таблица",
            "Date / Time": "Дата / Время",
            Day: "День",
            Time: "Время",
            File: "Файл",
            Signature: "Подпись",
            Content: "Контент",
            Columns: "Столбцы",
            "Field Set": "Набор полей",
            Panel: "Панель",
            Tabs: "Вкладки",
            Well: "Лист",
            Label: "Название",
            "Please fix the following errors before submitting":
              "Пожалуйста исправьте ошибки перед теп как продолжить",
            "Email: Email must be a valid email.": "Не правильный e-mail",
            Placeholder: "Заполнитель",
            Description: "Описание",
            Tooltip: "Подсказка",
            "To add a tooltip to this field,enter text here.":
              "Введите подсказку здесь",
            "Input Mask": "Маска ввода",
            Hidden: "Скрытый",
            "Hide Label": "Скрыть название",
            Save: "Сохранить",
            Cancel: "Отмена",
            Remove: "Удалить",
            Preview: "Предварительный просмотр",
            Disabled: "Отключен",
            Validation: "Проверка",
            Data: "Данные",
            "Property Name": "Имя переменной",
            Display: "Отображение",
            Widget: "Тип компонента",
            required: "обязательно для заполнения",
            pattern: "не соответствует маске!",
            error: "Пожалуйста исправьте ошибки прежде чем продолжить.",
            submitError:
              "Пожалуйста исправьте все ошибки прежде чем продолжить.",
            invalid_regex: "не соответствует маске!",
            mask: "{{field}} не соответствует маске.",
            valueIsNotAvailable: "неправильное значение.",
            Edit: "Редактировать",
            "Label Position": "Расположение",
            "Label Width": "Ширина",
            "Label Margin": "Отступ",
            Prefix: "Прификс",
            Suffix: "Суффикс",
            "Custom CSS Class": "CSS класс",
            "Show Word Counter": "Показать счетчик слов",
            "Show Character Counter": "Показать счетчик символов",
            "Hide Input": "Скрыть ввод",
            "Initial Focus": "Начальный фокус",
            "Allow Spellcheck": "Проверка орфографии",
            "Modal Edit": "Показать во всплывающем окне",
            "Tab Index": "Индекс вкладки",
            Autocomplete: "Автозавершение",
            month: "Месяц",
            day: "День",
            year: "Год",
            january: "Январь",
            february: "Февраль",
            march: "Март",
            april: "Апрель",
            may: "Май",
            june: "Июнь",
            july: "Июль",
            July: "Июль",
            august: "Август",
            september: "Сентябрь",
            october: "Октябрь",
            november: "Ноябрь",
            december: "Декабрь",
            next: "Далее",
            previous: "Назад",
            cancel: "Отмена",
            submit: "Отправить",
            "File Name": "Имя файла",
            Size: "Размер",
            "Add Another": "Добавить",
            "Drop files to attach,": "Перетащите файл сюда,",
            or: "или",
            browse: "выберите с диска",
          },
        },
      },
      isLoading: false,
      loadingComment: "Загрузка заявления",
      successComment: "Заявление успешно загружено!",
      isResponse: false,
      isAlertVisible: false,
      isValidFormData: false,

      isResponseByForm: false,

      headerNavbarList: [
        {
          id: 1,
          title: "Личный кабинет",
          type: "link",
          link: "/account",
          forAuthUser: true,
        },
        // { id: 2, title: "Вторая", type: "link", link: "/account" },
        // { id: 3, title: "Третья", type: "link", link: "/account" },
        // {
        //   id: 4,
        //   title: "Выпадашка",
        //   type: "dropdown",
        //   items: [
        //     {
        //       id: 5,
        //       title: "Пункт 1 выпадашки",
        //       type: "link",
        //       link: "/account",
        //     },
        //     { id: 6, title: "Пункт 2", type: "link", link: "/account" },
        //     { id: 7, title: "Пункт 3", type: "link", link: "/account" },
        //   ],
        // },
      ],
      newsList: [
        {
          id: 1,
          title:
            'Разработан и подключен ВС "Передача статусов и результатов предоставления государственных и муниципальных услуг"',
          text: 'Протестировано взаимодействие с ЕЛК по формату вида сведений "Передача статусов и результатов предоставления государственных и муниципальных услуг".',
          datePublication: "28.07.2022",
        },
        {
          id: 2,
          title: "Активирована новая версия услуги",
          text:
            'В системе активирована новая версия услуги "Организация отдыха детей в каникулярное время" по версии 1.0.2' +
            "При личном обращении заявителей требуется оформлять заявления по нововй версии формы заявления.",
          datePublication: "01.06.2022",
        },
        {
          id: 3,
          title: "Зарегистрирована новая версия услуги",
          text:
            'В системе зарегистрирована новая версия услуги "Организация отдыха детей в каникулярное время" по версии 1.0.2' +
            "Ведутся работы по выводу новой версии в продуктивную работу.",
          datePublication: "27.05.2022",
        },
      ],
      organization: {
        addresses: [],
        contacts: [],
      },
      footerContacts: [
        {
          id: 1,
          title: "Министерство образования Иркутской области",
          type: "link",
          link: "/",
        },
        {
          id: 2,
          title:
            "Адрес: 664025, Иркутская область, город Иркутск, Российская ул, д. 21",
          type: "link",
          link: "/",
        },
        {
          id: 3,
          title: "Телефон: +7 (999) 999-99 99",
          type: "link",
          link: "/",
        },
        {
          id: 4,
          title: "Электронная почта: test@mail.ru",
          type: "link",
          link: "/",
        },
      ],

      modal: null,
    };
  },

  computed: {
    isAuthUser: function () {
      return !!this.user.shortInfo.userId;
    },
  },

  methods: {
    // Вход
    // Входчерез ЕСИА
    getLogin() {
      axios(this.url + "auth/get-login", {
        withCredentials: true,
      })
        .then((response) => {
          if (this.isFirstLoad) {
            this.isFirstLoad = false;
            console.groupCollapsed("Ссылка на авторизацию ЕСИА");
            console.log(response.data);
            console.groupEnd();
          } else {
            console.log(response);
            location.href = response.data.url;
          }
        })
        .catch((error) => {
          if (error.response) {
            if (error.response.status === 406) {
              console.log("Пользователь уже авторизован");
            }
          }
          this.getUserId();
          this.getUserInfo();
        });
    },
    // Вход по логину и паролю
    signInLocal(signInData) {
      console.log(signInData.login);
      console.log(signInData.password);
      const request = {
        login: signInData.login,
        password: signInData.password,
      };
      axios
        .post(this.url + "auth/local-login", request, {
          withCredentials: true,
        })
        .then(() => {
          this.loaderStart(this.authLoader, "Проверка данных пользователя");
          setTimeout(this.getUserId, 5000);
          setTimeout(this.getUserInfo, 5000);
          // this.getUserId();
          // this.getUserInfo();
        })
        .then(() => {
          this.user = this.userInfoFromResponse;
          // this.$refs["nav-sidebar"].hide();
        })
        .then(() => {
          this.loaderFinish(this.authLoader);
        })
        .catch((error) => {
          if (error.response.status === 401) {
            this.authError.type = "password";
            this.authError.text = "Неверно указан пароль!";
          }
          if (error.response.status === 404) {
            this.authError.type = "login";
            this.authError.text =
              "Пользователь с указанным логином не зарегистрирован!";
          }
        });
    },

    // Выбор роли пользователя при авторизации по логину/паролю
    // signInWithRole(role, hideModal) {
    signInWithRole(role) {
      axios
        .put(this.url + "core/put-metadata?orgId=0&roleId=" + role.id, "", {
          withCredentials: true,
        })
        .then((response) => {
          this.userInfoFromResponse.shortInfo = response.data;
          this.userInfoFromResponse.selectedRole = role;
          this.cleanSignInForm();
          console.log('Пользователь авторизован с ролью "' + role.label + '"');
        });
    },
    selectRoleById(roles, roleId) {
      for (let i = 0; i < roles.length; i++) {
        if (roleId === roles[i].id) {
          console.groupCollapsed("Пользователь уже авторизован с ролью");
          console.log(roles[i]);
          console.groupEnd();
          return roles[i];
        }
      }
    },

    // Получение информации о пользователе
    getUserId() {
      axios(this.url + "auth/get-user", {
        withCredentials: true,
      }).then((response) => {
        console.groupCollapsed("Идентификатор пользователя");
        console.log(response.data);
        console.groupEnd();
        this.userInfoFromResponse.shortInfo = response.data;
      });
    },
    // getUserInfo(hideModal = true) {
    getUserInfo() {
      axios(this.url + "core/get-user", {
        withCredentials: true,
      }).then((response) => {
        console.groupCollapsed("Данные пользователя");
        console.log(response.data);
        console.groupEnd();
        this.userInfoFromResponse.fullInfo = response.data;
        this.user = this.userInfoFromResponse;
        if (this.userInfoFromResponse.fullInfo.roles.length === 0) {
          // this.$refs["modal-auth"].hide();
          console.log("У пользователя отсутствуют роли");
        } else if (this.userInfoFromResponse.fullInfo.roles.length === 1) {
          // this.$refs["modal-auth"].hide();
          this.signInWithRole(this.userInfoFromResponse.fullInfo.roles[0]);
          console.groupCollapsed(
            "Пользователь авторизован с единственной имеющейся ролью"
          );
          console.log(this.userInfoFromResponse.fullInfo.roles[0]);
          console.groupEnd();
        } else {
          let currentRole = this.selectRoleById(
            response.data.roles,
            this.userInfoFromResponse.shortInfo.roleId
          );
          if (currentRole) {
            // this.signInWithRole(currentRole, hideModal);
            this.signInWithRole(currentRole);
          }
        }
      });
    },

    // Выход
    signOut() {
      if (this.user.shortInfo.typeAuth === "local") {
        this.signOutLocal();
      }
      if (this.user.shortInfo.typeAuth === "esia") {
        this.getLogout();
      }
    },
    // Выход через ЕСИА
    getLogout() {
      axios(this.url + "auth/get-logout", {
        withCredentials: true,
      })
        .then((response) => {
          console.log("Ссылка на выход ЕСИА");
          console.log(response);
          // location.href = response.data.url;
          this.esiaLogoutLink = response.data.url;
          this.user.shortInfo = {
            userId: null,
            userName: "",
            typeAuth: "",
          };
          this.user.fullInfo = {
            roles: [],
          };
          this.signOutEsia();
        })
        .catch((error) => {
          console.log("Ошибка выхода есиа");
          console.log(error);
        })
        .then(() => {
          this.$router.push("/");
        });
    },
    signOutEsia() {
      axios(this.esiaLogoutLink, {
        withCredentials: true,
      }).then((response) => {
        // this.$refs["nav-sidebar"].hide();
        console.log("Ответ на запрос о выходе из ЕСИА");
        console.log(response);
      });
    },
    // Локальный выход
    signOutLocal() {
      axios(this.url + "auth/local-logout", {
        withCredentials: true,
      })
        .then((response) => {
          if (response.status === 200) {
            this.user = {
              shortInfo: {
                userId: null,
                userName: "",
                roleId: null,
                orgId: null,
                typeAuth: "",
              },
              fullInfo: {
                roles: [],
                userData: {
                  lastName: "",
                  firstName: "",
                  middleName: "",
                  birthDate: "",
                  citizenship: "",
                  gender: "",
                  snils: "",
                },
                contacts: [{ value: "" }],
              },
              selectedRole: {
                id: null,
                key: "",
                label: "",
              },
            };
            console.log("Выход пользователя из системы");
          }
        })
        .then(() => {
          // this.$refs["nav-sidebar"].hide();
          if (this.$route.path !== "/") {
            this.$router.push("/");
          }
        })
        .catch((error) => {
          if (error.response && error.response.status === 401) {
            console.log(
              "Попытка неавторизованного пользователя осуществить выход из системы"
            );
          }
        });
    },
    // Очистка ошибок
    cleanSignInForm() {
      // this.login = "";
      // this.password = "";
      this.authError.type = "";
      this.authError.text = "";
    },

    // Получение списка заявлений пользователя
    getApps(
      page,
      pageSize,
      servId = this.appsServiceId,
      sortCol = "id",
      sortDesc = true,
      userList = true,
      active = false
    ) {
      axios
        .get(
          this.url +
            "app/get-apps?pageNum=" +
            (page - 1) +
            "&pageSize=" +
            pageSize +
            "&servId=" +
            servId +
            "&sortCol=" +
            sortCol +
            "&sortDesc=" +
            sortDesc +
            "&userList=" +
            userList +
            "&active=" +
            active,
          {
            withCredentials: true,
          }
        )
        .then((response) => {
          this.appsTable.rowsList = this.appsConvertToTable(
            response.data.content
          );
          this.appsTable.pagination.appsItemsTotal =
            response.data.totalElements;
          console.groupCollapsed("Список заявлений");
          console.log(response.data.content);
          console.groupEnd();
        });
    },
    appsConvertToTable(apps) {
      let appsTable = [];
      apps.forEach(function (app) {
        let appsTableItem = [];
        appsTableItem.push(app.id);
        appsTableItem.push(app.servName);
        appsTableItem.push(app.startDate.substring(0, 10));
        appsTableItem.push("---");
        appsTableItem.push(app.status);
        appsTableItem.push("---");
        appsTable.push(appsTableItem);
      });
      return appsTable;
    },

    // Получение списка заявлений пользователя
    getMessages(
      page,
      pageSize,
      servId = this.messagesServiceId,
      sortCol = "id",
      sortDesc = true,
      userList = true,
      active = false
    ) {
      axios
        .get(
          this.url +
            "app/get-apps?pageNum=" +
            (page - 1) +
            "&pageSize=" +
            pageSize +
            "&servId=" +
            servId +
            "&sortCol=" +
            sortCol +
            "&sortDesc=" +
            sortDesc +
            "&userList=" +
            userList +
            "&active=" +
            active,
          {
            withCredentials: true,
          }
        )
        .then((response) => {
          this.messagesTable.rowsList = this.messagesConvertToTable(
            response.data.content
          );
          this.messagesTable.pagination.itemsTotal =
            response.data.totalElements;
          console.groupCollapsed("Список заявлений");
          console.log(response.data.content);
          console.groupEnd();
        });
    },
    messagesConvertToTable(messages) {
      let messagesTable = [];
      messages.forEach(function (message) {
        let messagesTableItem = [];
        messagesTableItem.push(message.id);
        messagesTableItem.push(message.startDate.substring(0, 10));
        messagesTableItem.push("---");
        messagesTableItem.push("---");
        messagesTableItem.push(message.status);
        messagesTableItem.push("---");
        messagesTable.push(messagesTableItem);
      });
      return messagesTable;
    },

    // Стартовая форма заявления
    getStartForm(serviceId, id) {
      console.log("Внутри диспетчера форм");
      console.log(id);
      this.isResponse = false;
      this.isLoading = true;
      if (id) {
        setTimeout(this.getForm, 500, serviceId, id);
      } else {
        setTimeout(this.getForm, 500, serviceId);
      }
    },
    getForm(serviceId, appId) {
      let requestUrl;
      if (appId) {
        requestUrl = this.url + "app/get-appData?id=" + appId;
      } else {
        requestUrl =
          // this.url + "serv/get-appData?id=" + this.$route.params.modelId;
          this.url + "serv/get-appData?id=" + serviceId;
      }
      axios
        .get(requestUrl, {
          withCredentials: true,
        })
        .then((response) => {
          console.groupCollapsed("Стартовая форма");
          console.log(response.data.applicationDTO);
          console.groupEnd();
          const newForm = response.data.applicationDTO;
          newForm.data = JSON.parse(newForm.data);
          newForm.form.scheme = JSON.parse(newForm.form.scheme);
          if (serviceId === this.appsServiceId) {
            this.appForm = newForm;
          } else if (serviceId === this.messagesServiceId) {
            this.messageForm = newForm;
          }
        })
        .then(() => {
          this.isResponse = true;
          this.isLoading = false;
        });
    },

    validateForm() {
      // console.log("Проверка валидности формы");
      return this.$children[2].$refs.vueForm.formio.checkValidity(
        this.$children[2].$refs.vueForm.formio.submission.data
      );
    },

    invokeAction(action) {
      console.log("Выполнение действия");
      console.log(action);
      this.isFirstLoad = false;
      this.isValidFormData = this.validateForm();
      console.log("Валидность формы:" + this.isValidFormData);
      if (this.isValidFormData) {
        this.loadingComment = "Отправка данных заявления";
        this.isResponse = false;
        this.isLoading = true;
        setTimeout(
          this.invoke,
          1000,
          action.actionId,
          action.backAction,
          action.modelId
        );
      } else {
        this.$children[2].$refs.vueForm.formio.submit();
      }
    },
    invoke(actionId, isBackAction = false, modelId) {
      let formId;
      let formData;
      if (modelId === this.appsServiceId) {
        formId = this.appForm.id;
        formData = JSON.stringify(this.appForm.data);
      } else if (modelId === this.messagesServiceId) {
        formId = this.messageForm.id;
        formData = JSON.stringify(this.messageForm.data);
      }
      const request = {
        actionId: actionId,
        userId: 13,
        appId: formId,
        data: formData,
      };
      axios
        .post(this.url + "app/action-invoke", request)
        .then((response) => {
          console.log("Ответ на экшн");
          console.log(response);
          if (response.data.responseObject) {
            let fileApp = JSON.parse(response.data.responseObject);
            console.log("Объект файла");
            console.log(fileApp);
            let link = document.createElement("a");
            link.setAttribute("download", fileApp.fileName);
            link.setAttribute(
              "href",
              "data:application/octet-stream;base64," + fileApp.fileData
            );
            link.click();
          } else {
            if (isBackAction) {
              this.cleanAppForm();
            } else {
              this.getNextForm(actionId, response, modelId);
            }
          }
        })
        .then(() => {
          this.isResponse = true;
          this.isLoading = false;
          this.isAlertVisible = true;
          this.isFirstLoad = true;
          setTimeout(this.hideAlert, 3000);
        });
    },

    // Переход к следующей форме (стандартное дейтвие)
    getNextForm(actionId, response, modelId) {
      console.log("Следующая форма");
      console.log(response);
      let nextForm = response.data.applicationDTO;
      nextForm.data = JSON.parse(nextForm.data);
      nextForm.form.scheme = JSON.parse(nextForm.form.scheme);
      if (modelId === this.appsServiceId) {
        this.appForm = nextForm;
      } else if (modelId === this.messagesServiceId) {
        this.messageForm = nextForm;
      }
      this.successComment = "Заявление отправлено!";
    },

    cleanAppForm() {
      // this.modal.hide();
      let modal = new Modal(document.getElementById("modalId"));
      console.log(modal);
      modal.hide();
      console.log("Очищаю форму");
      console.log(this);
      this.isLoading = false;
      this.loadingComment = "Загрузка формы заявления";
      this.successComment = "Форма заявления успешно загружена!";
      this.isResponse = true;
      this.isAlertVisible = true;
      this.isValidFormData = false;
      this.isFirstLoad = true;
    },

    // Пагинация
    changePageSize(list, newPageInfo, serviceId) {
      list.pagination.page = newPageInfo[0];
      list.pagination.pageSize = newPageInfo[1];
      if (serviceId === this.appsServiceId) {
        this.getApps(
          this.appsTable.pagination.page,
          this.appsTable.pagination.pageSize
        );
      } else if (serviceId === this.messagesServiceId) {
        this.getMessages(
          this.messagesTable.pagination.page,
          this.messagesTable.pagination.pageSize
        );
      }
    },
    changePage(list, page, serviceId) {
      console.log(page);
      list.pagination.page = page;
      if (serviceId === this.appsServiceId) {
        this.getApps(
          this.appsTable.pagination.page,
          this.appsTable.pagination.pageSize
        );
      } else if (serviceId === this.messagesServiceId) {
        this.getMessages(
          this.messagesTable.pagination.page,
          this.messagesTable.pagination.pageSize
        );
      }
    },

    // Лоадер
    loaderStart(loader, loaderComment) {
      loader.isResponse = false;
      loader.isLoading = true;
      loader.comment = loaderComment;
    },
    loaderFinish(loader) {
      loader.isResponse = true;
      loader.isLoading = false;
    },
  },

  watch: {
    // Получение заявлений
    isAuthUser: function () {
      if (this.isAuthUser) {
        this.getApps(
          this.appsTable.pagination.page,
          this.appsTable.pagination.pageSize
        );
        this.getMessages(
          this.messagesTable.pagination.page,
          this.messagesTable.pagination.pageSize
        );
      }
    },
  },

  mounted: function () {
    // Запрос информации о пользователе
    this.getLogin();
  },
};
</script>
