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
    <main class="d-flex flex-column">
      <router-view
        :news-list="newsList"
        :url="url"
        :app-loaded="appLoaded"
        :esia-link="esiaLink"
        :user="user"
        :user-selected-role-id="userSelectedRoleId"
        :teacher-info="teacherInfo"
        :expert-info="expertInfo"
        :is-auth-user="isAuthUser"
        :auth-error="authError"
        :auth-loader="authLoader"
        :profile-loader="profileLoader"
        :apps-table="appsTable"
        :app-form="appForm"
        :app-form-options="i18n"
        :apps-loader="appsLoader"
        :messages-table="messagesTable"
        :message-form="messageForm"
        :message-form-options="i18n"
        :messages-loader="messagesLoader"
        :expertises-table="expertisesTable"
        :expertise-form="expertiseForm"
        :expertise-form-options="i18n"
        :expertises-loader="expertisesLoader"
        :analytics-table="analyticsTable"
        :analytic-form-options="i18n"
        :analytics-loader="analyticsLoader"
        :logs-table="logsTable"
        :log-form="logForm"
        :log-form-options="i18n"
        :logs-loader="logsLoader"
        :dictionaries="dictionaries"
        :file-resources="fileResources"
        :expertises-schedule="expertisesSchedule"
        @sign-in-local="signInLocal($event)"
        @sign-in-esia="getLogin"
        @sign-out="signOut"
        @set-role="setRole($event)"
        @edit-profile="editProfile"
        @set-teacher="setTeacher"
        @set-expert="setExpert"
        @create-new-app="createNewApp(appsServiceId)"
        @open-existing-app="openExistingApp(appsServiceId, $event)"
        @create-new-message="createNewMessage(messagesServiceId)"
        @open-existing-message="openExistingMessage(messagesServiceId, $event)"
        @open-existing-expertise="
          openExistingExpertise(expertisesServiceId, $event)
        "
        @open-existing-log="openExistingLog(logsServiceId, $event)"
        @change-apps-page-size="
          changePageSize(appsTable, $event, appsServiceId)
        "
        @change-apps-page="changePage(appsTable, $event, appsServiceId)"
        @change-apps-filter="changeFilter(appsTable.filters, $event)"
        @apply-apps-filter="getApps"
        @clear-apps-filter="clearAppsFilter"
        @change-message-page-size="
          changePageSize(messagesTable, $event, messagesServiceId)
        "
        @change-message-page="
          changePage(messagesTable, $event, messagesServiceId)
        "
        @change-messages-filter="changeFilter(messagesTable.filters, $event)"
        @apply-messages-filter="getMessages"
        @clear-messages-filter="clearMessagesFilter"
        @change-expertises-page-size="
          changePageSize(expertisesTable, $event, expertisesServiceId)
        "
        @change-expertises-page="
          changePage(expertisesTable, $event, expertisesServiceId)
        "
        @change-expertises-filter="
          changeFilter(expertisesTable.filters, $event)
        "
        @apply-expertises-filter="getExpertises"
        @clear-expertises-filter="clearExpertisesFilter"
        @invoke-action="invokeAction($event)"
        @sort-table="sortTable($event.tableName, $event.sortedColumnIndex)"
        @get-home-view-data="getHomeViewData"
        @get-account-view-data="getAccountViewData($event)"
      />
      <ModalBootstrapCustomBS46 modal-id="notify" header>
        <template v-slot:modal-title> Оповещение </template>
        <template v-slot:modal-body>
          <!--          Уведомления!-->
          <template v-if="false">
            <transition-group
              if="false"
              name="fade-out-group"
              tag="div"
              class="accordion"
              id="accordionMessages"
            >
              <MessagesAccordionItemBS46
                v-for="message of sortedMessagesList"
                :key="message.id"
                :message="message"
                @read-message="readMessage(message.id)"
                @delete-message="deleteMessage(message.id)"
              />
            </transition-group>
            <div
              v-if="messagesList.length === 0"
              class="alert alert-secondary mb-0"
              role="alert"
            >
              У вас отсутствуют уведомления!
            </div>
          </template>
          <template v-else>
            <div class="alert alert-secondary mb-0" role="alert">
              Время рабочей сессии завершено. Для продолжения работы необходимо
              повторно пройти авторизацию
            </div>
          </template>
        </template>
      </ModalBootstrapCustomBS46>
    </main>
    <TheFooterBS46 :footer-contacts="footerContacts" />
  </div>
</template>

<script>
import axios from "axios";
import TheHeaderBS46 from "./components/TheHeaderBS46";
import TheFooterBS46 from "./components/TheFooterBS46";
import { Modal } from "bootstrap";
import ModalBootstrapCustomBS46 from "./components/universal/ModalBootstrapCustomBS46";
import MessagesAccordionItemBS46 from "./components/MessagesAccordionItemBS46";
import $ from "jquery";

export default {
  name: "TheApp",
  components: {
    MessagesAccordionItemBS46,
    ModalBootstrapCustomBS46,
    TheFooterBS46,
    TheHeaderBS46,
  },

  data() {
    return {
      url: "https://teachers.coko38.ru/api/",
      // url: "http://192.168.17.59:8180/api/",
      urlAdd: "https://teachers.coko38.ru/api-teacher/api/",
      // urlAdd: "http://192.168.17.59:8180/api-teacher/api/",
      user: {
        isAuth: false,
        signInData: {
          login: "",
          password: "",
        },
        shortInfo: {
          orgId: null,
          roleId: null,
          typeAuth: "",
          userId: null,
          userName: "",
        },
        fullInfo: {
          userId: null,
          shortUserName: "",
          roles: [],
          userOrganizations: [],
          userData: {
            oid: null,
            lastName: "",
            firstName: "",
            middleName: "",
            birthDate: "",
            birthPlace: null,
            gender: "",
            snils: "",
            inn: "",
            updatedOn: null,
            citizenship: null,
            status: null,
            trusted: false,
            verifying: false,
            avatar: null,
          },
          contacts: [
            {
              type: "",
              value: "",
              verifyingValue: null,
              vrfStu: null,
              vrfValStu: null,
            },
          ],
          addresses: null,
          esiaDocuments: null,
          other: {},
        },
        selectedRole: {
          id: null,
          key: "",
          label: "",
        },
      },
      userSelectedRoleId: "",
      isFirstLoad: true,
      appLoaded: false,
      loadersDelay: 1000,

      // Авторизация
      esiaLink: "",
      esiaLogoutLink: "",
      authError: {
        type: "",
        text: "",
      },
      authLoader: {
        isLoading: false,
        isResponse: false,
        comment: "",
        theme: "secondary",
      },

      // Профиль пользователя
      profileLoader: {
        isLoading: false,
        isResponse: false,
        comment: "",
        theme: "secondary",
      },

      // Заявления
      appsServiceId: 2,
      appsResponse: [],
      appsTable: {
        id: "appsTable",
        service: 2,
        columnsList: [
          { title: "№ заявления", name: "id", sorted: true },
          { title: "Наименование услуги", name: "service" },
          { title: "Дата создания", name: "createDate", sorted: true },
          { title: "Номер ЕПГУ", name: "epguNum", sorted: true },
          { title: "Статус", name: "status", sorted: true },
          {
            title: "Дата изменения статуса",
            name: "changeDate",
            sorted: true,
          },
        ],
        primaryColumn: "№ заявления",
        rowsList: [],
        sortColumn: "id",
        ascendingSortOrder: false,
        filters: [
          {
            id: "1",
            label: "№ заявления",
            type: "input",
            subtype: "number",
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
            value: null,
          },
          {
            id: "3",
            label: "Дата создания",
            type: "range",
            subtype: "date",
            itemsList: [
              { label: " c", value: null },
              { label: " по", value: null },
            ],
            width: 12,
            responsive: "col-sm-8 col-md-6 col-lg-4",
          },
          {
            id: "4",
            label: "№ ЕПГУ",
            type: "input",
            subtype: "number",
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
            value: null,
          },
          {
            id: "5",
            label: "Статус",
            type: "select",
            itemsList: [
              { id: 1, value: 1, label: "Черновик" },
              { id: 2, value: 2, label: "В работе" },
              { id: 3, value: 3, label: "Обработано" },
              { id: 4, value: 4, label: "Архивная" },
            ],
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
            values: [],
          },
          {
            id: "6",
            label: "Дата изменения статуса",
            type: "range",
            subtype: "date",
            itemsList: [
              { label: " c", value: null },
              { label: " по", value: null },
            ],
            width: 12,
            responsive: "col-sm-8 col-md-6 col-lg-4",
          },
        ],
        pagination: {
          itemsTotal: 0,
          page: 1,
          pageSize: 10,
          itemsPerPage: [10, 25, 50],
        },
      },
      appsLoader: {
        isLoading: false,
        isResponse: false,
        comment: "",
        theme: "secondary",
      },
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

      // Сообщения
      messagesServiceId: 1,
      messagesResponse: [],
      messagesTable: {
        id: "messagesTable",
        service: 1,
        columnsList: [
          { title: "№ сообщения", name: "id", sorted: true },
          {
            title: "Дата создания сообщения",
            name: "createDate",
            sorted: true,
          },
          { title: "Тема", name: "theme" },
          {
            title: "Дата входа в статус",
            name: "changeDate",
            sorted: true,
          },
          { title: "Статус", name: "status", sorted: true },
          {
            title: "Срок отправки ответа",
            name: "responseLimit",
          },
        ],
        primaryColumn: "№ сообщения",
        rowsList: [],
        sortColumn: "id",
        ascendingSortOrder: false,
        filters: [
          {
            id: "1",
            label: "№ сообщения",
            type: "input",
            subtype: "number",
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
            value: null,
          },
          {
            id: "2",
            label: "Дата создания сообщения",
            type: "range",
            subtype: "date",
            itemsList: [
              { label: " c", value: null },
              { label: " по", value: null },
            ],
            width: 12,
            responsive: "col-sm-8 col-md-6 col-lg-4",
          },
          {
            id: "3",
            label: "Тема",
            type: "input",
            subtype: "text",
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
            value: "",
          },
          {
            id: "4",
            label: "Дата входа в статус",
            type: "range",
            subtype: "date",
            itemsList: [
              { label: " с", value: null },
              { label: " по", value: null },
            ],
            width: 12,
            responsive: "col-sm-8 col-md-6 col-lg-4",
          },
          {
            id: "5",
            label: "Статус",
            type: "select",
            itemsList: [
              { id: 1, value: 1, label: "Черновик" },
              { id: 2, value: 2, label: "В работе" },
              { id: 3, value: 3, label: "Обработано" },
              { id: 4, value: 4, label: "Архивная" },
            ],
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
            values: [],
          },
          {
            id: "6",
            label: "Срок отправки ответа",
            type: "range",
            subtype: "date",
            itemsList: [{ label: " с" }, { label: " по" }],
            width: 12,
            responsive: "col-sm-8 col-md-6 col-lg-4",
            value: null,
          },
        ],
        pagination: {
          itemsTotal: 0,
          page: 1,
          pageSize: 10,
          itemsPerPage: [10, 25, 50],
        },
      },
      messagesLoader: {
        isLoading: false,
        isResponse: false,
        comment: "",
        theme: "secondary",
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

      // "Экспертизы"
      expertisesServiceId: 101,
      expertisesResponse: [],
      expertisesTable: {
        id: "expertisesTable",
        service: 101,
        columnsList: [
          { title: "№ заявления", name: "id", sorted: true },
          {
            title: "ФИО педагогического работника",
            name: "teacherFullname",
          },
          {
            title: "Дата начала экспертизы",
            name: "startDate",
          },
          {
            title: "Срок окончания экспертизы",
            name: "expertiseLimit",
          },
          {
            title: "Дата окончания экспертизы",
            name: "expertiseFinishDate",
          },
          {
            title: "Результат экспертизы",
            name: "expertiseResult",
          },
          { title: "Статус", name: "status", sorted: true },
        ],
        primaryColumn: "№ заявления",
        rowsList: [
          [
            "0001",
            "Иванов А.П.",
            "01.08.2022",
            "01.10.2022",
            "---",
            "---",
            "В работе",
          ],
          [
            "0002",
            "Попов И.С.",
            "05.08.2022",
            "05.10.2022",
            "12.08.2022",
            "Положительный",
            "Завершена",
          ],
          [
            "0003",
            "Кузнецова Н.Б.",
            "09.08.2022",
            "09.10.2022",
            "---",
            "---",
            "Черновик",
          ],
        ],
        sortColumn: "id",
        ascendingSortOrder: false,
        filters: [
          {
            id: "1",
            label: "№ заявления",
            type: "input",
            subtype: "number",
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
            value: null,
          },
          {
            id: "2",
            label: "ФИО пед.работника",
            type: "input",
            subtype: "text",
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
            value: null,
          },
          {
            id: "3",
            label: "Дата начала экспертизы",
            type: "range",
            subtype: "date",
            itemsList: [
              { label: " c", value: null },
              { label: " по", value: null },
            ],
            width: 12,
            responsive: "col-sm-8 col-md-6 col-lg-4",
          },
          {
            id: "4",
            label: "Срок окончания экспертизы",
            type: "input",
            subtype: "number",
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
            value: null,
          },
          {
            id: "5",
            label: "Дата окончания экспертизы",
            type: "range",
            subtype: "date",
            itemsList: [
              { label: " c", value: null },
              { label: " по", value: null },
            ],
            width: 12,
            responsive: "col-sm-8 col-md-6 col-lg-4",
          },
          {
            id: "6",
            label: "Результат экспертизы",
            type: "select",
            itemsList: [
              { id: 1, value: 1, label: "Положительный" },
              { id: 2, value: 2, label: "Отрицательный" },
              { id: 3, value: 3, label: "Прекращена" },
            ],
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
            values: [],
          },
          {
            id: "7",
            label: "Статус",
            type: "select",
            itemsList: [
              { id: 1, value: 1, label: "Черновик" },
              { id: 2, value: 2, label: "В работе" },
              { id: 3, value: 3, label: "Обработано" },
              { id: 4, value: 4, label: "Архивная" },
            ],
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
            values: [],
          },
        ],
        pagination: {
          itemsTotal: 0,
          page: 1,
          pageSize: 10,
          itemsPerPage: [10, 25, 50],
        },
      },
      expertisesLoader: {
        isLoading: false,
        isResponse: false,
        comment: "",
        theme: "secondary",
      },
      expertiseForm: {
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

      // "Аналитика"
      analyticsServiceId: 2,
      analyticsTable: {
        id: "analyticsTable",
        columnsList: [
          {
            title: "Количество проведенных экспертиз",
            name: "expertisesTotal",
          },
          {
            title: "Количество положительных результатов",
            name: "expertisesPositiveResults",
          },
          {
            title: "Количество отрицательных результатов",
            name: "expertisesNegativeResults",
          },
          {
            title: "Количество совпадений",
            name: "expertisesEqualResults",
          },
        ],
        primaryColumn: "Количество проведенных экспертиз",
        rowsList: [["30", "5", "10", "15"]],
        sortColumn: "",
        ascendingSortOrder: false,
        filters: [
          {
            id: "1",
            label: "№ заявления",
            type: "input",
            subtype: "number",
            width: 4,
            value: "245",
            required: true,
            disabled: true,
          },
          {
            id: "2",
            label: "Квалификация",
            type: "select",
            itemsList: [
              { id: 1, text: "Вторая" },
              { id: 2, text: "Первая" },
              { id: 3, text: "Высшая" },
            ],
          },
          {
            id: "3",
            label: "Показать только архивные",
            type: "checkbox",
            width: 12,
          },
          {
            id: "4",
            label: "",
            type: "range",
            subtype: "date",
            itemsList: [{ label: "Дата начала" }, { label: "Дата окончания" }],
            width: 6,
          },
        ],
        pagination: {
          itemsTotal: 0,
          page: 1,
          pageSize: 10,
          itemsPerPage: [10, 25, 50],
        },
      },
      analyticsLoader: {
        isLoading: false,
        isResponse: false,
        comment: "",
        theme: "secondary",
      },

      // "Журнал действий"
      logsServiceId: 2,
      logsTable: {
        id: "logsTable",
        columnsList: [
          { title: "Id записи", name: "id" },
          { title: "Дата события", name: "eventDate" },
          { title: "Описание", name: "eventDescription" },
          { title: "Событие", name: "eventName" },
          { title: "Изменения", name: "eventChanges" },
        ],
        primaryColumn: "Id записи",
        rowsList: [
          [
            "0001",
            "01.08.2022",
            "Изменение ошибочной записи",
            "Изменение записи",
            "Изменение даты рождения",
          ],
          [
            "0002",
            "05.08.2022",
            "Изменение статуса",
            "Проведение аттестации",
            "Положительный результат аттестации",
          ],
          [
            "0003",
            "09.08.2022",
            "Изменение статуса",
            "Проведение аттестации",
            "Отрицательный результат аттестации",
          ],
        ],
        sortColumn: "id",
        ascendingSortOrder: false,
        filters: [
          {
            id: "1",
            label: "Id записи",
            type: "input",
            subtype: "number",
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
          },
          {
            id: "2",
            label: "Дата события",
            type: "range",
            subtype: "date",
            itemsList: [{ label: " с" }, { label: " по" }],
            width: 12,
            responsive: "col-sm-8 col-md-6 col-lg-4",
          },
          {
            id: "3",
            label: "Описание",
            type: "input",
            subtype: "text",
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
          },
          {
            id: "4",
            label: "Событие",
            type: "select",
            itemsList: [
              { id: 1, text: "Изменение записи" },
              { id: 2, text: "Проведение аттестации" },
              { id: 3, text: "Удаление записи" },
              { id: 4, text: "Создание записи" },
            ],
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
          },
          {
            id: "5",
            label: "Изменения",
            type: "input",
            subtype: "text",
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
          },
        ],
        pagination: {
          itemsTotal: 0,
          page: 1,
          pageSize: 10,
          itemsPerPage: [10, 25, 50],
        },
      },
      logsLoader: {
        isLoading: false,
        isResponse: false,
        comment: "",
        theme: "secondary",
      },
      logForm: {
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

      // Текущее заявление
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
          submitError: "Пожалуйста исправьте все ошибки прежде чем продолжить.",
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
      messagesList: [
        {
          id: 1,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-11T10:31:33.750+00:00",
          wasRead: false,
          deleted: false,
        },
        {
          id: 2,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-18T10:31:33.750+00:00",
          wasRead: true,
          deleted: false,
        },
        {
          id: 3,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-04T10:31:33.750+00:00",
          wasRead: false,
          deleted: false,
        },
        {
          id: 4,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-10T10:31:33.750+00:00",
          wasRead: false,
          deleted: false,
        },
        {
          id: 5,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-17T10:31:33.750+00:00",
          wasRead: true,
          deleted: false,
        },
        {
          id: 6,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-03T10:31:33.750+00:00",
          wasRead: false,
          deleted: false,
        },
        {
          id: 7,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-09T10:31:33.750+00:00",
          wasRead: false,
          deleted: false,
        },
        {
          id: 8,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-12T10:31:33.750+00:00",
          wasRead: false,
          deleted: false,
        },
        {
          id: 9,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-16T10:31:33.750+00:00",
          wasRead: false,
          deleted: false,
        },
        {
          id: 10,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-11T19:31:33.750+00:00",
          wasRead: false,
          deleted: false,
        },
        {
          id: 11,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-11T18:31:33.750+00:00",
          wasRead: false,
          deleted: false,
        },
        {
          id: 12,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-11T13:31:33.750+00:00",
          wasRead: false,
          deleted: false,
        },
        {
          id: 13,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-11T15:31:33.750+00:00",
          wasRead: false,
          deleted: false,
        },
        {
          id: 14,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-11T14:31:33.750+00:00",
          wasRead: false,
          deleted: false,
        },
        {
          id: 15,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-11T21:00:25.750+00:00",
          wasRead: false,
          deleted: false,
        },
        {
          id: 16,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-11T21:45:45.750+00:00",
          wasRead: false,
          deleted: false,
        },
        {
          id: 17,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-11T22:05:15.750+00:00",
          wasRead: false,
          deleted: false,
        },
        {
          id: 18,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-11T07:35:25.750+00:00",
          wasRead: true,
          deleted: false,
        },
        {
          id: 19,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-11T20:05:50.750+00:00",
          wasRead: true,
          deleted: false,
        },
        {
          id: 20,
          messageTitle: "Заголовок уведомления #",
          messageText:
            "Статус заявления обновлен. Подробная информация направлена на вашу электронную почту.",
          receiptDate: "2022-07-11T14:33:55.750+00:00",
          wasRead: true,
          deleted: false,
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
          type: "text",
          link: "/",
        },
        {
          id: 2,
          title: "Адрес: 664027, Россия, г. Иркутск, ул. Российская, д. 21",
          type: "text",
          link: "/",
        },
        {
          id: 3,
          title: "Приемная:  8 (3952) 33-13-33, Канцелярия: 8 (3952) 24-09-72",
          type: "text",
          link: "/",
        },
        {
          id: 4,
          title: "E-mail: obraz@38edu.ru",
          type: "text",
          link: "/",
        },
        {
          id: 5,
          title:
            "Государственное автономное учреждение Иркутской области «Центр оценки профессионального мастерства, квалификаций педагогов и мониторинга качества образования»",
          type: "text",
          link: "/",
        },
        {
          id: 6,
          title:
            "Адрес: 664023, Иркутская область, город Иркутск, улица Лыткина, дом 75 «а»",
          type: "text",
          link: "/",
        },
        {
          id: 7,
          title: "Телефон: +7 (3952) 500-287",
          type: "text",
          link: "/",
        },
        {
          id: 8,
          title: "Email: info@coko38.ru",
          type: "text",
          link: "/",
        },
      ],

      modal: null,
      vueForm: null,

      // newTeacher: {
      //   id: 39502,
      //   familyName: "Васильев",
      //   name: "Михаил",
      //   middleName: "Анатольевич",
      //   birthday: "1980-03-08T00:00:00.000+00:00",
      //   snils: "07397683725",
      //   citizenship: "Российская Федерация",
      //   mail: "mgp-nsk@yandex.ru",
      //   phone: "+7(903)1234567",
      //   gender: "1",
      //   municipality: "Город Зима",
      //   subjectArea: "Математика",
      //   regAddress: "ул.Коммунистическая",
      //   factAddress: "ул.Новая",
      //   docname: "Паспорт гражданина РФ",
      //   docSeries: "5000",
      //   docNumber: "123456",
      //   issueDate: "2001-02-15T00:00:00.000+00:00",
      //   docValidate: "2051-02-15T00:00:00.000+00:00",
      //   issueOrg: "010",
      //   mailAgree: false,
      //   workHistoryList: [],
      //   attestationsResultList: [],
      //   fileList: [],
      // },
      // newExpert: {
      //   id: 39502,
      //   familyName: "Васильев",
      //   name: "Михаил",
      //   middleName: "Анатольевич",
      //   snils: "07397683725",
      //   mail: "mgp-nsk@yandex.ru",
      //   phone: "+7(903)1234567",
      //   position: "Специалист отдела образования",
      //   municipality: "Город Зима",
      //   organization: "Администрация города Зима",
      //   subjectArea: "Математика",
      //   rating: "5",
      //   status: true,
      //   mailAgree: true,
      //   numberExaminations: 32,
      //   numberPositiveResult: 16,
      //   numberNegativeResult: 4,
      //   numberCoincidences: 12,
      // },

      teacherRoleId: 39707,
      expertRoleId: 39927,
      teacherInfo: {
        id: 0,
        familyName: "",
        name: "",
        middleName: "",
        birthday: "",
        snils: "",
        citizenship: "",
        mail: "",
        phone: "",
        gender: "",
        municipality: "",
        subjectArea: "",
        regAddress: "",
        factAddress: "",
        docName: "",
        docSeries: "",
        docNumber: "",
        issueDate: "",
        docValidate: "",
        issueOrg: "",
        mailAgree: false,
        workHistoryList: [],
        attestationsResultList: [],
        fileList: [],
      },
      expertInfo: {
        id: 0,
        familyName: "",
        name: "",
        middleName: "",
        snils: "",
        mail: "",
        phone: "",
        position: "",
        municipality: "",
        organization: "",
        subjectArea: "",
        rating: "",
        status: false,
        mailAgree: false,
        numberExaminations: 0,
        numberPositiveResult: 0,
        numberNegativeResult: 0,
        numberCoincidences: 0,
      },

      dictionaries: {
        gender: [],
        subjectArea: [],
        jobGroups: [],
        municipalEntityIrkutsk: [],
        DocumentPersonal: [],
        statusModel_2: [],
        statusModel_1: [],
        statusModel_101: [],
        resultExpert: [],
        topic: [],
      },

      // Документы для работы с системой
      fileResources: [],

      expertisesSchedule: "",

      authIntervalId: null,
    };
  },

  computed: {
    isAuthUser: function () {
      return !!this.user.shortInfo.userId;
    },
    unreadMessages: function () {
      return this.messagesList.filter((item) => !item.wasRead).length;
    },
    sortedMessagesList: function () {
      let sortedMessages = [];
      this.messagesList.forEach(function (item) {
        sortedMessages.push(item);
      });
      sortedMessages.sort(function (a, b) {
        if (a.receiptDate > b.receiptDate) return 1; // если первое значение больше второго
        if (a.receiptDate == b.receiptDate) return 0; // если равны
        if (a.receiptDate < b.receiptDate) return -1; // если первое значение меньше второго
      });
      return sortedMessages.reverse();
    },
    messageRequestQuery: function () {
      let messageRequestQuery = "app/messages?";
      if (this.user.shortInfo.userId) {
        messageRequestQuery += "userId=" + this.user.shortInfo.userId;
      }
      if (this.messagesTable.filters[0].value) {
        messageRequestQuery += "&id=" + this.messagesTable.filters[0].value;
      }
      if (this.messagesTable.filters[1].itemsList[0].value) {
        messageRequestQuery +=
          "&createDate_start=" +
          this.formatDateForRequest(
            this.messagesTable.filters[1].itemsList[0].value
          );
      }
      if (this.messagesTable.filters[1].itemsList[1].value) {
        messageRequestQuery +=
          "&createDate_end=" +
          this.formatDateForRequest(
            this.messagesTable.filters[1].itemsList[1].value
          );
      }
      if (this.messagesTable.filters[2].value) {
        messageRequestQuery += "&theme=" + this.messagesTable.filters[2].value;
      }
      if (this.messagesTable.filters[3].itemsList[0].value) {
        messageRequestQuery +=
          "&enterStatusDate_start=" +
          this.formatDateForRequest(
            this.messagesTable.filters[3].itemsList[0].value
          );
      }
      if (this.messagesTable.filters[3].itemsList[1].value) {
        messageRequestQuery +=
          "&enterStatusDate_end=" +
          this.formatDateForRequest(
            this.messagesTable.filters[3].itemsList[1].value
          );
      }
      if (this.messagesTable.filters[4].values[0]) {
        messageRequestQuery +=
          "&status=" + this.messagesTable.filters[4].values[0];
      }
      if (this.messagesTable.filters[5].itemsList[0].value) {
        messageRequestQuery +=
          "&sendDate_start=" +
          this.formatDateForRequest(
            this.messagesTable.filters[5].itemsList[0].value
          );
      }
      if (this.messagesTable.filters[5].itemsList[1].value) {
        messageRequestQuery +=
          "&sendDate_end=" +
          this.formatDateForRequest(
            this.messagesTable.filters[5].itemsList[1].value
          );
      }

      messageRequestQuery +=
        "&pageNum=" + (this.messagesTable.pagination.page - 1);

      messageRequestQuery +=
        "&pageSize=" + this.messagesTable.pagination.pageSize;
      messageRequestQuery += "&sortCol=" + this.messagesTable.sortColumn;
      messageRequestQuery +=
        "&sortDesc=" + this.messagesTable.ascendingSortOrder;
      return messageRequestQuery;
    },
    appRequestQuery: function () {
      let appRequestQuery = "app/teachers?";
      if (this.user.shortInfo.userId) {
        appRequestQuery += "userId=" + this.user.shortInfo.userId;
      }
      if (this.appsTable.filters[0].value) {
        appRequestQuery += "&id=" + this.appsTable.filters[0].value;
      }
      if (this.appsTable.filters[1].itemsList[0].value) {
        appRequestQuery +=
          "&createDate_start=" +
          this.formatDateForRequest(
            this.appsTable.filters[1].itemsList[0].value
          );
      }
      if (this.appsTable.filters[1].itemsList[1].value) {
        appRequestQuery +=
          "&createDate_end=" +
          this.formatDateForRequest(
            this.appsTable.filters[1].itemsList[1].value
          );
      }
      if (this.appsTable.filters[2].value) {
        appRequestQuery += "&epguNum=" + this.appsTable.filters[2].value;
      }
      if (this.appsTable.filters[3].values[0]) {
        appRequestQuery += "&status=" + this.appsTable.filters[3].values[0];
      }
      if (this.appsTable.filters[4].itemsList[0].value) {
        appRequestQuery +=
          "&changeStatusDate_start=" +
          this.formatDateForRequest(
            this.appsTable.filters[4].itemsList[0].value
          );
      }
      if (this.appsTable.filters[4].itemsList[1].value) {
        appRequestQuery +=
          "&changeStatusDate_end=" +
          this.formatDateForRequest(
            this.appsTable.filters[4].itemsList[1].value
          );
      }

      appRequestQuery += "&pageNum=" + (this.appsTable.pagination.page - 1);
      appRequestQuery += "&pageSize=" + this.appsTable.pagination.pageSize;
      appRequestQuery += "&sortCol=" + this.appsTable.sortColumn;
      appRequestQuery += "&sortDesc=" + this.appsTable.ascendingSortOrder;
      return appRequestQuery;
    },
    expertiseRequestQuery: function () {
      let expertiseRequestQuery = "app/expertise?";
      if (this.user.shortInfo.userId) {
        expertiseRequestQuery += "userId=" + this.user.shortInfo.userId;
      }
      // expertiseRequestQuery +=
      //   "&archive=" + this.expertisesTable.filters[6].value;
      if (this.expertisesTable.filters[0].value) {
        expertiseRequestQuery += "&id=" + this.expertisesTable.filters[0].value;
      }
      if (this.expertisesTable.filters[1].value) {
        expertiseRequestQuery +=
          "&teacherFIO=" + this.expertisesTable.filters[1].value;
      }
      if (this.expertisesTable.filters[2].itemsList[0].value) {
        expertiseRequestQuery +=
          "&createDate_start=" +
          this.formatDateForRequest(
            this.expertisesTable.filters[2].itemsList[0].value
          );
      }
      if (this.expertisesTable.filters[2].itemsList[1].value) {
        expertiseRequestQuery +=
          "&createDate_end=" +
          this.formatDateForRequest(
            this.expertisesTable.filters[2].itemsList[1].value
          );
      }
      if (this.expertisesTable.filters[3].value) {
        expertiseRequestQuery +=
          "&timeToEnd=" + this.expertisesTable.filters[3].value;
      }
      // if (this.expertisesTable.filters[4].itemsList[0].value) {
      //   expertiseRequestQuery +=
      //     "&???_start=" +
      //     this.formatDateForRequest(
      //       this.expertisesTable.filters[4].itemsList[0].value
      //     );
      // }
      // if (this.expertisesTable.filters[4].itemsList[1].value) {
      //   expertiseRequestQuery +=
      //     "&???_end=" +
      //     this.formatDateForRequest(
      //       this.expertisesTable.filters[4].itemsList[1].value
      //     );
      // }
      if (this.expertisesTable.filters[5].values[0]) {
        expertiseRequestQuery +=
          "&expertiseResult=" + this.expertisesTable.filters[5].values[0];
      }
      if (this.expertisesTable.filters[6].values[0]) {
        expertiseRequestQuery +=
          "&status=" + this.expertisesTable.filters[6].values[0];
      }

      expertiseRequestQuery +=
        "&pageNum=" + (this.expertisesTable.pagination.page - 1);
      expertiseRequestQuery +=
        "&pageSize=" + this.expertisesTable.pagination.pageSize;
      expertiseRequestQuery += "&sortCol=" + this.expertisesTable.sortColumn;
      expertiseRequestQuery +=
        "&sortDesc=" + this.expertisesTable.ascendingSortOrder;
      return expertiseRequestQuery;
    },
  },

  methods: {
    // Косвенная проверка авторизованности пользователя, получение ссылки на вход через ЕСИА (переход на страницу авторизации ЕСИА - вынести во внешний метод)
    async getLogin() {
      await axios(this.url + "auth/get-login", {
        withCredentials: true,
      })
        .then((response) => {
          if (this.isFirstLoad) {
            this.isFirstLoad = false;
            this.esiaLink = response.data.url;
            console.groupCollapsed("Ссылка на авторизацию ЕСИА");
            console.log(response.data);
            console.groupEnd();
          } else {
            console.log(response);
            location.href = response.data.url;
            this.esiaLink = "";
          }
        })
        .catch((error) => {
          if (error.response) {
            if (error.response.status === 406) {
              console.log("Пользователь уже авторизован");
              this.user.isAuth = true;
            } else if (error.response.status === 404) {
              console.log(
                "Проверка авторизованности пользователя не удалась. Ошибка при составлении ссылки на вход ЕСИА со стороны закрытой части"
              );
            } else if (error.response.status === 500) {
              console.log(
                "Проверка авторизованности пользователя не удалась. Ошибка со стороны открытой части при отправке запроса получения ссылки на вход ЕСИА на закрытую часть"
              );
            } else {
              console.log(
                "Непредвиденная ошибка при выполнении запроса auth/get-login"
              );
            }
          }
        });
    },
    // Вход по логину и паролю
    signInLocal(signInData) {
      this.loaderStart(this.authLoader, "Проверка данных пользователя");
      this.user.signInData = signInData;
      axios
        .post(this.url + "auth/local-login", signInData, {
          withCredentials: true,
        })
        .then(() => {
          this.getUserId();
          this.getUserInfo();
          this.signInDataReset();
          // this.$refs["nav-sidebar"].hide();
        })
        .catch((error) => {
          if (error.response.status === 401) {
            this.authErrorLog("password", "Неверно указан пароль!");
          } else if (error.response.status === 404) {
            this.authErrorLog(
              "login",
              "Пользователь с указанным логином не зарегистрирован!"
            );
          } else if (error.response.status === 406) {
            console.log(
              "Попытка авторизованного пользователя авторизоваться ещё раз"
            );
            this.authErrorLog(
              "client",
              "Пользователь уже авторизован. Повторная авторизация не требуется"
            );
          } else if (error.response.status === 500) {
            console.log(
              "Ошибка открытого контура при выполнении запроса локальной авторизации на закрытый контур"
            );
            this.authErrorLog(
              "server",
              "Произошла ошибка при выполнении запроса авторизации. Пожалуйста обратитесь к администратору системы. Код ошибки - 500"
            );
          } else if (error.response.status === 501) {
            console.log(
              "Ошибка закрытого контура при выполнении запроса локальной авторизации"
            );
            this.authErrorLog(
              "server",
              "Произошла ошибка при выполнении запроса авторизации. Пожалуйста обратитесь к администратору системы. Код ошибки - 501"
            );
          } else {
            console.log(
              "Непредвиденная ошибка при выполнении запроса локальной авторизации"
            );
            this.authErrorLog(
              "server",
              "Произошла непредвиденная ошибка при выполнении запроса авторизации. Пожалуйста обратитесь к администратору системы"
            );
          }
        })
        .then(() => {
          setTimeout(this.loaderFinish, this.loadersDelay, this.authLoader);
        });
    },
    // Запись ошибок авторизации
    authErrorLog(errorType, errorComment) {
      this.authError.type = errorType;
      this.authError.text = errorComment;
    },
    // Сброс данных авторизации и ошибок авторизации
    signInDataReset() {
      this.user.signInData = {
        login: "",
        password: "",
      };
      this.authErrorLog("", "");
    },

    // Выбор роли пользователя при авторизации по логину/паролю
    // signInWithRole(role, hideModal) {
    signInWithRole(role) {
      axios
        .put(this.url + "core/put-metadata?orgId=0&roleId=" + role.id, "", {
          withCredentials: true,
        })
        .then((response) => {
          this.user.shortInfo = response.data;
          this.user.selectedRole = role;
          this.cleanSignInForm();
          console.groupCollapsed("Пользователь авторизован с ролью");
          console.log(role);
          console.groupEnd();
        });
    },

    // Получение информации о пользователе
    async getUserId() {
      await axios(this.url + "auth/get-user", {
        withCredentials: true,
      })
        .then((response) => {
          console.groupCollapsed("Идентификатор пользователя");
          console.log(response.data);
          console.groupEnd();
          this.user.shortInfo = response.data;
        })
        .catch((error) => {
          if (error.response) {
            if (error.response.status === 404) {
              console.log("Данные текущего пользователя не найдены");
            } else {
              console.log(
                "Непредвиденная ошибка при выполнении запроса auth/get-user"
              );
            }
          }
        });
    },
    async getUserInfo() {
      await axios(this.url + "core/get-user", {
        withCredentials: true,
      })
        .then((response) => {
          console.groupCollapsed("Данные пользователя");
          console.log(response.data);
          console.groupEnd();
          this.user.fullInfo = response.data;
          if (this.user.fullInfo.roles.length === 0) {
            // this.$refs["modal-auth"].hide();
            console.log("У пользователя отсутствуют роли");
          } else {
            let currentRole = this.getRoleById(this.user.shortInfo.roleId);
            if (currentRole) {
              this.initUserRole(currentRole);
            }
          }
        })
        .catch((error) => {
          if (error.response) {
            if (error.response.status === 404) {
              console.log(
                "Ошибка запроса данных текущего пользователя неавторизованным пользователем"
              );
            } else if (error.response.status === 401) {
              console.log(
                "Ошибка получения данных при выполнении запроса данных текущего пользователя"
              );
            } else {
              console.log(
                "Непредвиденная ошибка при выполнении запроса core/get-user"
              );
            }
          }
        });
    },
    // Присвоение текущей роли пользователю
    initUserRole(role) {
      this.user.selectedRole = role;
      this.userSelectedRoleId = this.user.shortInfo.roleId;
      console.groupCollapsed("Пользователь проинициализирован с ролью");
      console.log(role);
      console.groupEnd();
    },

    // Смена роли пользователя
    setRole(roleId) {
      this.loaderStart(this.authLoader, "Смена роли пользователя");
      axios
        .put(this.url + "core/put-metadata?orgId=0&roleId=" + roleId, "", {
          withCredentials: true,
        })
        .then((response) => {
          // this.$emit("change-user-short-info", response.data);
          // this.$emit("select-role", role);
          this.user.shortInfo = response.data;
          let role = this.getRoleById(roleId);
          this.user.selectedRole = role;
          this.userSelectedRoleId = role.id;
          console.log(
            "Роль пользователя изменена на роль с идентификатором " +
              role.label +
              "(id = " +
              role.id +
              ")"
          );
          if (this.user.shortInfo.roleId === this.teacherRoleId) {
            this.getTeacher();
          } else if (this.user.shortInfo.roleId === this.expertRoleId) {
            this.getExpert();
          }
        })
        .then(() => {
          setTimeout(this.loaderFinish, this.loadersDelay, this.authLoader);
        });
    },
    getRoleById(roleId) {
      let role;
      this.user.fullInfo.roles.some(function (roleItem) {
        if (roleItem.id === roleId) {
          role = roleItem;
          return roleItem;
        }
      });
      if (!role) {
        console.log(`Роль по идентификатору ${roleId} не найдена `);
      }
      return role;
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
    // Локальный выход
    signOutLocal() {
      this.loaderStart(this.authLoader, "Выход из системы");
      axios(this.url + "auth/local-logout", {
        withCredentials: true,
      })
        .then(() => {
          this.user = {
            signInData: {
              login: "",
              password: "",
            },
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
        })
        .catch((error) => {
          if (error.response && error.response.status === 401) {
            console.log(
              "Попытка неавторизованного пользователя осуществить локальный выход из системы"
            );
          } else {
            console.log("Непредвиденная ошибка локального выхода из системы");
          }
        })
        .then(() => {
          setTimeout(this.loaderFinish, this.loadersDelay, this.authLoader);
          // this.$refs["nav-sidebar"].hide();
          if (this.$route.path !== "/") {
            this.$router.push("/");
          }
        });
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
          location.href = response.data.url;
        })
        .catch((error) => {
          console.log("Ошибка выхода есиа");
          console.log(error);
        })
        .then(() => {
          this.$router.push("/");
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
    // getApps(
    //   page = this.appsTable.pagination.pageSize,
    //   pageSize = this.appsTable.pagination.pageSize,
    //   servId = this.appsServiceId,
    //   sortCol = this.appsTable.sortColumn,
    //   sortDesc = !this.appsTable.ascendingSortOrder,
    //   userList = true,
    //   active = false
    // ) {
    //   axios
    //     .get(
    //       this.url +
    //         "app/get-apps?pageNum=" +
    //         (page - 1) +
    //         "&pageSize=" +
    //         pageSize +
    //         "&servId=" +
    //         servId +
    //         "&sortCol=" +
    //         sortCol +
    //         "&sortDesc=" +
    //         sortDesc +
    //         "&userList=" +
    //         userList +
    //         "&active=" +
    //         active,
    //       {
    //         withCredentials: true,
    //       }
    //     )
    //     .then((response) => {
    //       this.appsResponse = response.data.content;
    //       this.appsTable.rowsList = this.appsConvertToTable(
    //         response.data.content
    //       );
    //       this.appsTable.pagination.itemsTotal = response.data.totalElements;
    //       console.groupCollapsed("Список заявлений");
    //       console.log(response.data.content);
    //       console.groupEnd();
    //     });
    // },
    async getApps() {
      await axios
        .get(this.urlAdd + this.appRequestQuery, {
          withCredentials: true,
        })
        .then((response) => {
          this.appsResponse = response.data.content;
          this.appsTable.rowsList = this.appsConvertToTable(
            response.data.content
          );
          this.appsTable.pagination.itemsTotal = response.data.totalElements;
          console.groupCollapsed("Список заявлений");
          console.log(response.data.content);
          console.groupEnd();
        });
    },
    // appsConvertToTable(apps) {
    //   let appsTable = [];
    //   apps.forEach(function (app) {
    //     let appsTableItem = [];
    //     appsTableItem.push(app.externalId);
    //     appsTableItem.push(app.servName);
    //     appsTableItem.push(app.startDate.substring(0, 10));
    //     appsTableItem.push("---");
    //     appsTableItem.push(app.status);
    //     appsTableItem.push("---");
    //     appsTable.push(appsTableItem);
    //   });
    //   return appsTable;
    // },
    appsConvertToTable(apps) {
      let appsTable = [];
      let getDictionaryValueById = this.getDictionaryValueById;
      apps.forEach(function (app) {
        let appsTableItem = [];
        if (app.id) {
          appsTableItem.push(app.id);
        } else {
          appsTableItem.push("");
        }
        if (app.serviceName) {
          appsTableItem.push(app.serviceName);
        } else {
          appsTableItem.push("");
        }
        if (app.createDate) {
          appsTableItem.push(app.createDate.substring(0, 10));
        } else {
          appsTableItem.push("");
        }
        if (app.epguNum) {
          appsTableItem.push(app.epguNum);
        } else {
          appsTableItem.push("");
        }
        if (app.status) {
          appsTableItem.push(
            getDictionaryValueById("statusModel_2", app.status)
          );
        } else {
          appsTableItem.push("");
        }
        if (app.changeStatusDate) {
          appsTableItem.push(app.changeStatusDate.substring(0, 10));
        } else {
          appsTableItem.push("");
        }

        appsTable.push(appsTableItem);
      });
      return appsTable;
    },
    clearAppsFilter() {
      this.appsTable.filters[0].value = null;
      this.appsTable.filters[1].itemsList[0].value = null;
      this.appsTable.filters[1].itemsList[1].value = null;
      this.appsTable.filters[2].value = null;
      this.appsTable.filters[3].values = [];
      this.appsTable.filters[4].itemsList[0].value = null;
      this.appsTable.filters[4].itemsList[1].value = null;
      this.appsTable.filters[5].value = false;
      this.getApps();
    },

    // Получение списка заявлений пользователя
    async getMessages() {
      await axios
        .get(this.urlAdd + this.messageRequestQuery, {
          withCredentials: true,
        })
        .then((response) => {
          this.messagesResponse = response.data.content;
          this.messagesTable.rowsList = this.messagesConvertToTable(
            response.data.content
          );
          this.messagesTable.pagination.itemsTotal =
            response.data.totalElements;
          console.groupCollapsed("Список сообщений");
          console.log(response.data.content);
          console.groupEnd();
        });
    },
    messagesConvertToTable(messages) {
      let messagesTable = [];
      let getDictionaryValueById = this.getDictionaryValueById;
      messages.forEach(function (message) {
        let messagesTableItem = [];
        if (message.id) {
          messagesTableItem.push(message.id);
        } else {
          messagesTableItem.push("");
        }
        if (message.createDate) {
          messagesTableItem.push(message.createDate.substring(0, 10));
        } else {
          messagesTableItem.push("");
        }
        if (message.theme && message.theme) {
          messagesTableItem.push(message.theme);
        } else {
          messagesTableItem.push("");
        }
        if (message.dateEnterStatusStart) {
          messagesTableItem.push(message.dateEnterStatusStart.substring(0, 10));
        } else {
          messagesTableItem.push("");
        }
        if (message.status) {
          messagesTableItem.push(
            getDictionaryValueById("statusModel_1", message.status)
          );
        } else {
          messagesTableItem.push("");
        }
        if (message.responseTime) {
          messagesTableItem.push(message.responseTime);
        } else {
          messagesTableItem.push("");
        }
        messagesTable.push(messagesTableItem);
      });
      return messagesTable;
    },

    // Изменения фильтра
    changeFilter(filters, newFilterData) {
      console.log("Изменяется фильтр");
      console.log(filters);
      console.log(newFilterData);
      let filterItem = filters.find(function (item) {
        if (item.id === newFilterData.id) return true;
      });
      console.log(filterItem);
      if (filterItem.type === "select") {
        filterItem.values = [newFilterData.value];
      } else if (filterItem.type === "range") {
        filterItem.itemsList[newFilterData.index].value = newFilterData.value;
      } else {
        filterItem.value = newFilterData.value;
      }
    },
    formatDateForRequest(dateInputFormat) {
      let arrayDateInput = dateInputFormat.split("-");
      let requestFormatDate =
        arrayDateInput[2] + "." + arrayDateInput[1] + "." + arrayDateInput[0];
      return requestFormatDate;
    },
    clearMessagesFilter() {
      this.messagesTable.filters[0].value = null;
      this.messagesTable.filters[1].itemsList[0].value = null;
      this.messagesTable.filters[1].itemsList[1].value = null;
      this.messagesTable.filters[2].value = "";
      this.messagesTable.filters[3].itemsList[0].value = null;
      this.messagesTable.filters[3].itemsList[1].value = null;
      this.messagesTable.filters[4].values = [];
      this.messagesTable.filters[5].itemsList[0].value = null;
      this.messagesTable.filters[5].itemsList[1].value = null;
      this.getMessages();
    },

    // Получение списка заявлений пользователя
    // getExpertises(
    //   page = this.expertisesTable.pagination.pageSize,
    //   pageSize = this.expertisesTable.pagination.pageSize,
    //   servId = this.expertisesServiceId,
    //   sortCol = this.expertisesTable.sortColumn,
    //   sortDesc = !this.expertisesTable.ascendingSortOrder,
    //   userList = true,
    //   active = false
    // ) {
    //   axios
    //     .get(
    //       this.url +
    //         "app/get-apps?pageNum=" +
    //         (page - 1) +
    //         "&pageSize=" +
    //         pageSize +
    //         "&servId=" +
    //         servId +
    //         "&sortCol=" +
    //         sortCol +
    //         "&sortDesc=" +
    //         sortDesc +
    //         "&userList=" +
    //         userList +
    //         "&active=" +
    //         active,
    //       {
    //         withCredentials: true,
    //       }
    //     )
    //     .then((response) => {
    //       this.expertisesResponse = response.data.content;
    //       this.expertisesTable.rowsList = this.expertisesConvertToTable(
    //         response.data.content
    //       );
    //       this.expertisesTable.pagination.itemsTotal =
    //         response.data.totalElements;
    //       console.groupCollapsed("Список экспертиз");
    //       console.log(response.data.content);
    //       console.groupEnd();
    //     });
    // },
    async getExpertises() {
      await axios
        .get(this.urlAdd + this.expertiseRequestQuery, {
          withCredentials: true,
        })
        .then((response) => {
          this.expertisesResponse = response.data.content;
          this.expertisesTable.rowsList = this.expertisesConvertToTable(
            response.data.content
          );
          this.expertisesTable.pagination.itemsTotal =
            response.data.totalElements;
          console.groupCollapsed("Список экспертиз");
          console.log(response.data.content);
          console.groupEnd();
        });
    },
    expertisesConvertToTable(expertises) {
      let expertisesTable = [];
      let getDictionaryValueById = this.getDictionaryValueById;
      expertises.forEach(function (expertise) {
        let expertisesTableItem = [];
        if (expertise.id) {
          expertisesTableItem.push(expertise.id);
        } else {
          expertisesTableItem.push("");
        }
        if (expertise.teacherFIO) {
          expertisesTableItem.push(expertise.teacherFIO);
        } else {
          expertisesTableItem.push("");
        }
        if (expertise.dateExpertiseStart) {
          expertisesTableItem.push(
            expertise.dateExpertiseStart.substring(0, 10)
          );
        } else {
          expertisesTableItem.push("");
        }
        if (expertise.timeToEnd) {
          expertisesTableItem.push(expertise.timeToEnd);
        } else {
          expertisesTableItem.push("");
        }
        if (expertise.dateExpertiseEnd) {
          expertisesTableItem.push(expertise.dateExpertiseEnd.substring(0, 10));
        } else {
          expertisesTableItem.push("");
        }
        if (expertise.resultExpertise) {
          expertisesTableItem.push(
            getDictionaryValueById("resultExpert", expertise.resultExpertise)
          );
        } else {
          expertisesTableItem.push("");
        }
        if (expertise.status) {
          expertisesTableItem.push(
            getDictionaryValueById("statusModel_101", expertise.status)
          );
        } else {
          expertisesTableItem.push("");
        }

        expertisesTable.push(expertisesTableItem);
      });
      return expertisesTable;
    },
    clearExpertisesFilter() {
      this.expertisesTable.filters[0].value = null;
      this.expertisesTable.filters[1].value = null;
      this.expertisesTable.filters[2].itemsList[0].value = null;
      this.expertisesTable.filters[2].itemsList[1].value = null;
      this.expertisesTable.filters[3].value = null;
      this.expertisesTable.filters[4].itemsList[0].value = null;
      this.expertisesTable.filters[4].itemsList[1].value = null;
      this.expertisesTable.filters[5].values = [];
      this.expertisesTable.filters[6].value = false;
      this.getExpertises();
    },

    // Список записей журнала действий
    async getAudit() {
      await axios
        .get(this.urlAdd + "data/getAudit", {
          withCredentials: true,
        })
        .then((response) => {
          let logsTableRows = this.logsConvertToTable(response.data).reverse();
          this.logsTable.rowsList = logsTableRows;
          this.logsTable.pagination.itemsTotal = logsTableRows.length;
          console.groupCollapsed("Список записей журнала");
          console.log(this.logsConvertToTable(response.data));
          console.groupEnd();
        });
    },
    logsConvertToTable(logs) {
      let logsTable = [];
      if (logs.length > 0) {
        logs.forEach((item) => {
          let logItem = [];
          logItem.push(item.id);
          logItem.push(item.eventDate.substring(0, 10));
          logItem.push(item.description);
          logItem.push(item.event);
          let changesList = JSON.parse(item.changes).changes;
          let changesText = "";
          changesList.forEach((changeItem) => {
            changesText += changeItem.name + " - " + changeItem.new_v + "; ";
          });
          logItem.push(changesText);
          logsTable.push(logItem);
        });
      }
      return logsTable;
    },

    // Стартовая форма заявления
    async getStartForm(serviceId, id) {
      if (serviceId === this.appsServiceId) {
        this.loaderStart(this.appsLoader, "Загрузка формы заявления");
      } else if (serviceId === this.messagesServiceId) {
        this.loaderStart(this.messagesLoader, "Загрузка формы сообщения");
      } else if (serviceId === this.expertisesServiceId) {
        this.loaderStart(this.expertisesLoader, "Загрузка формы экспертизы");
      }
      if (id) {
        await this.getForm(serviceId, id);
      } else {
        await this.getForm(serviceId);
      }
    },
    async getForm(serviceId, appId) {
      let requestUrl;
      if (appId) {
        requestUrl = this.url + "app/get-appData?id=" + appId;
        if (serviceId === this.appsServiceId) {
          console.log("Запрос формы существующего заявления");
        } else if (serviceId === this.messagesServiceId) {
          console.log("Запрос формы существующего сообщения");
        } else if (serviceId === this.expertisesServiceId) {
          console.log("Запрос формы существующей экспертизы");
        }
      } else {
        requestUrl =
          // this.url + "serv/get-appData?id=" + this.$route.params.modelId;
          this.url + "serv/get-appData?id=" + serviceId;
        if (serviceId === this.appsServiceId) {
          console.log("Запрос стартовой формы заявления");
        } else if (serviceId === this.messagesServiceId) {
          console.log("Запрос стартовой формы сообщения");
        }
      }
      await axios
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
          } else if (serviceId === this.expertisesServiceId) {
            this.expertiseForm = newForm;
          }
        })
        .then(() => {
          if (serviceId === this.appsServiceId) {
            this.loaderFinish(this.appsLoader);
          } else if (serviceId === this.messagesServiceId) {
            this.loaderFinish(this.messagesLoader);
          } else if (serviceId === this.expertisesServiceId) {
            this.loaderFinish(this.expertisesLoader);
          }
        });
    },

    validateForm() {
      console.log("Проверка валидности формы");
      // console.log(this);
      return this.$children[3].$refs.vueForm.formio.checkValidity(
        this.$children[3].$refs.vueForm.formio.submission.data
      );
    },

    invokeAction(action) {
      console.log("Выполнение действия с id=" + action.actionId);
      this.isFirstLoad = false;
      this.isValidFormData = this.validateForm();
      console.log("Валидность формы:" + this.isValidFormData);
      if (this.isValidFormData) {
        if (action.modelId === this.appsServiceId) {
          this.loaderStart(this.appsLoader, "Выполнение действия по заявлению");
        } else if (action.modelId === this.messagesServiceId) {
          this.loaderStart(
            this.messagesLoader,
            "Выполнение действия по сообщению"
          );
        } else if (action.modelId === this.expertisesServiceId) {
          this.loaderStart(
            this.expertisesLoader,
            "Выполнение действия по экспертизе"
          );
        }
        setTimeout(
          this.invoke,
          1000,
          action.actionId,
          action.backAction,
          action.modelId
        );
      } else {
        this.$children[3].$refs.vueForm.formio.submit();
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
      } else if (modelId === this.expertisesServiceId) {
        formId = this.expertiseForm.id;
        formData = JSON.stringify(this.expertiseForm.data);
      }
      const request = {
        actionId: actionId,
        // userId: this.user.shortInfo.userId,
        appId: formId,
        data: formData,
        // userId: this.user.shortInfo.userId,
        // roleId: this.user.shortInfo.roleId,
        // orgId: this.user.shortInfo.orgId,
      };
      axios
        .post(this.url + "app/action-invoke", request, {
          withCredentials: true,
        })
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
          if (modelId === this.appsServiceId) {
            this.getApps();
            this.loaderFinish(this.appsLoader);
          } else if (modelId === this.messagesServiceId) {
            this.getMessages();
            this.loaderFinish(this.messagesLoader);
          } else if (modelId === this.expertisesServiceId) {
            this.getExpertises();
            this.loaderFinish(this.expertisesLoader);
          }
          this.isFirstLoad = true;
        });
    },

    // Переход к следующей форме (стандартное действие)
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
      } else if (modelId === this.expertisesServiceId) {
        this.expertiseForm = nextForm;
      }
      this.successComment = "Заявление отправлено!";
      console.log(this.$children[3].$refs.vueForm);
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
      console.log(list);
      console.log(newPageInfo);
      console.log(serviceId);
      list.pagination.page = newPageInfo[0];
      list.pagination.pageSize = newPageInfo[1];
      if (serviceId === this.appsServiceId) {
        this.getApps();
      } else if (serviceId === this.messagesServiceId) {
        this.getMessages();
      } else if (serviceId === this.expertisesServiceId) {
        this.getExpertises();
      }
    },
    changePage(list, page, serviceId) {
      list.pagination.page = page;
      if (serviceId === this.appsServiceId) {
        this.getApps();
      } else if (serviceId === this.messagesServiceId) {
        this.getMessages();
      } else if (serviceId === this.expertisesServiceId) {
        this.getExpertises();
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

    // Редактирование профиля
    editProfile() {
      this.loaderStart(this.profileLoader, "Редактирование личных данных");
      setTimeout(this.loaderFinish, this.loadersDelay, this.profileLoader);
    },

    // Заявления
    async createNewApp(appsServiceId) {
      this.loaderStart(this.appsLoader, "Загрузка формы заявления");
      await this.getStartForm(appsServiceId);
      this.loaderFinish(this.appsLoader);
    },
    async openExistingApp(appsServiceId, externalAppId) {
      this.loaderStart(this.appsLoader, "Загрузка заявления");
      let appId = this.appsResponse.find(function (item) {
        if (item.id === externalAppId) return true;
      }).appId;
      await this.getStartForm(appsServiceId, appId);
      this.loaderFinish(this.appsLoader);
    },

    // Сообщения
    async createNewMessage(appsServiceId) {
      this.loaderStart(this.messagesLoader, "Загрузка формы сообщения");
      await this.getStartForm(appsServiceId);
      this.loaderFinish(this.messagesLoader);
    },
    async openExistingMessage(appsServiceId, externalAppId) {
      this.loaderStart(this.messagesLoader, "Загрузка сообщения");
      let appId = this.messagesResponse.find(function (item) {
        if (item.id === externalAppId) return true;
      }).appId;
      await this.getStartForm(appsServiceId, appId);
      this.loaderFinish(this.messagesLoader);
    },

    // "Экспертизы"
    async openExistingExpertise(appsServiceId, externalAppId) {
      this.loaderStart(this.expertisesLoader, "Загрузка деталей экспертизы");
      let appId = this.expertisesResponse.find(function (item) {
        if (item.id === externalAppId) return true;
      }).appId;
      await this.getStartForm(appsServiceId, appId);
      this.loaderFinish(this.expertisesLoader);
    },

    // Журнал сообщений
    openExistingLog(appsServiceId, appId) {
      // this.getStartForm(appsServiceId, appId);
      console.log(appsServiceId + " - " + appId);
      this.loaderStart(this.logsLoader, "Загрузка записи журнала");
      setTimeout(this.loaderFinish, this.loadersDelay, this.logsLoader);
    },

    // Получение списка мер
    async getServises() {
      await axios
        .get(
          this.url +
            "serv/get-services?pageNum=0&pageSize=100&sortCol=id&sortDesc=false&active=true",
          {
            withCredentials: true,
          }
        )
        .then((response) => {
          // this.measuresCardsList = response.data.content;
          // this.itemsTotal = response.data.totalElements;
          console.groupCollapsed("Список мер поддержки");
          console.log(response.data);
          console.groupEnd();
          // window.scrollTo(0, 0);
        });
    },

    // Уведомления
    readMessage(messageId) {
      console.log(messageId);
      let message = this.messagesList.find((item) => item.id === messageId);
      if (!message.wasRead) {
        message.wasRead = true;
      }
      message.wasRead = true;
    },
    deleteMessage(messageId) {
      console.log(messageId);
      let messageIndex = this.messagesList.findIndex(
        (item) => item.id === messageId
      );
      this.messagesList.splice(messageIndex, 1);
    },

    // Данные личного кабинета
    async getTeacher() {
      await axios
        .get(this.urlAdd + "teacher/get", {
          withCredentials: true,
        })
        .then((response) => {
          this.teacherInfo = response.data;
          if (!response.data.birthday) {
            this.teacherInfo.birthday = "";
          }
          if (!response.data.issueDate) {
            this.teacherInfo.issueDate = "";
          }
          if (!response.data.docValidate) {
            this.teacherInfo.docValidate = "";
          }
          this.teacherInfo.subjectArea = JSON.parse(response.data.subjectArea);
          console.groupCollapsed("Личные данные педагога");
          console.log(response.data);
          console.groupEnd();
        });
    },
    setTeacher() {
      this.loaderStart(this.profileLoader, "Сохранение личных данных");
      this.teacherInfo.subjectArea = JSON.stringify(
        this.teacherInfo.subjectArea
      );
      axios
        .post(this.urlAdd + "teacher/edit", this.teacherInfo, {
          withCredentials: true,
        })
        .then((response) => {
          this.getTeacher();
          console.groupCollapsed("Ответ на редактирование данных педагога");
          console.log(response.data);
          console.groupEnd();
        })
        .catch(() => {
          console.log(
            "Непредвиденная шибка при сохранении личных данных учителя"
          );
        })
        .then(() => {
          setTimeout(this.loaderFinish, this.loadersDelay, this.profileLoader);
        });
    },
    async getExpert() {
      await axios
        .get(this.urlAdd + "expert/get", {
          withCredentials: true,
        })
        .then((response) => {
          this.expertInfo = response.data;
          this.analyticsTable.rowsList.length = 0;
          this.analyticsTable.rowsList.push(
            this.analyticsConvertToTable(response.data)
          );
          this.expertInfo.subjectArea = JSON.parse(response.data.subjectArea);
          console.groupCollapsed("Личные данные эксперта");
          console.log(response.data);
          console.groupEnd();
        });
    },
    setExpert() {
      this.loaderStart(this.profileLoader, "Сохранение личных данных");
      this.expertInfo.subjectArea = JSON.stringify(this.expertInfo.subjectArea);
      axios
        .post(this.urlAdd + "expert/edit", this.expertInfo, {
          withCredentials: true,
        })
        .then((response) => {
          this.getExpert();
          console.groupCollapsed("Ответ на редактирование данных эксперта");
          console.log(response.data);
          console.groupEnd();
        })
        .catch(() => {
          console.log(
            "Непредвиденная шибка при сохранении личных данных эксперта'"
          );
        })
        .then(() => {
          setTimeout(this.loaderFinish, this.loadersDelay, this.profileLoader);
        });
    },
    analyticsConvertToTable(expertData) {
      let analyticsRowsList = [];
      analyticsRowsList.push(expertData.numberExaminations);
      analyticsRowsList.push(expertData.numberPositiveResult);
      analyticsRowsList.push(expertData.numberNegativeResult);
      analyticsRowsList.push(expertData.numberCoincidences);
      return analyticsRowsList;
    },

    // Словари
    async getAllDictionaries() {
      Promise.all([
        this.getDictionary("gender", '"Пол"'),
        this.getDictionary("subjectArea", '"Предметная область"'),
        this.getDictionary("jobGroups", '"Должность"'),
        this.getDictionary(
          "municipalEntityIrkutsk",
          '"Муниципальные образования"'
        ),
        this.getDictionary(
          "DocumentPersonal",
          '"Документы, удостоверяющие личность"'
        ),
        this.getDictionary("statusModel_2", '"Статусы заявлений"'),
        this.getDictionary("statusModel_1", '"Статусы сообщений"'),
        this.getDictionary("statusModel_101", '"Статусы экспертиз"'),
        this.getDictionary("resultExpert", '"Результаты экспертиз"'),
      ]).then(() => {
        console.log("Справочники загружены");
      });
      // this.getDictionary("topic", '"Темы сообщений"'),
    },
    async getDictionary(dictionaryCode, logComment) {
      await axios
        .get(this.url + "core/get-dictdata?code=" + dictionaryCode, {
          withCredentials: true,
        })
        .then((response) => {
          this.dictionaries[dictionaryCode] = this.convertDictionary(
            response.data
          );
          console.groupCollapsed("Справочник" + logComment);
          console.log(response.data);
          console.groupEnd();
        })
        .then(() => {
          this.setDictionaryToSelect(dictionaryCode);
        })
        .catch(() => {
          console.log(
            'Непредвиденная ошибка при выполнении запроса справочника "' +
              logComment +
              '"'
          );
        });
    },
    convertDictionary(dictionary) {
      let convertedDictionary = [];
      dictionary.forEach((dictionaryItem) => {
        let convertedItem = {};
        convertedItem.id = dictionaryItem.value;
        convertedItem.label = dictionaryItem.label;
        convertedItem.value = dictionaryItem.value;
        convertedDictionary.push(convertedItem);
      });
      return convertedDictionary;
    },
    getDictionaryValueById(dictionaryName, id) {
      let dictionary = this.dictionaries[dictionaryName];
      let dictionaryItem = dictionary.find((item) => item.value === id);
      return dictionaryItem.label;
    },
    setDictionaryToSelect(dictionaryCode) {
      if (dictionaryCode === "statusModel_1") {
        this.messagesTable.filters[4].itemsList =
          this.dictionaries.statusModel_1;
      }
      if (dictionaryCode === "statusModel_2") {
        this.appsTable.filters[3].itemsList = this.dictionaries.statusModel_2;
      }
      if (dictionaryCode === "statusModel_101") {
        this.expertisesTable.filters[6].itemsList =
          this.dictionaries.statusModel_101;
      }
      if (dictionaryCode === "resultExpert") {
        this.expertisesTable.filters[5].itemsList =
          this.dictionaries.resultExpert;
      }
      // if (dictionaryCode === "topic") {
      //   this.messagesTable.filters[2].itemsList = this.dictionaries.topic;
      // }
    },

    // Файлы для работы с системой
    async getFileResources() {
      await axios
        .get(this.urlAdd + "data/getFileResources", {
          withCredentials: true,
        })
        .then((response) => {
          this.fileResources = response.data.content;
          console.groupCollapsed("Файлы для работы с системой");
          console.log(response.data);
          console.groupEnd();
        })
        .catch(() => {
          console.log(
            "Непредвиденная ошибка при выполнении запроса файлов для работы с системой"
          );
        });
    },
    // График аттестации
    async getGakJournal() {
      await axios
        .get(this.urlAdd + "data/getGakJournal", {
          withCredentials: true,
        })
        .then((response) => {
          this.expertisesSchedule = this.parseGakJournal(response.data);
          console.groupCollapsed("Расписание аттестации");
          console.log(response.data);
          console.groupEnd();
        })
        .catch(() => {
          console.log(
            "Непредвиденная ошибка при выполнении запроса расписания аттестации"
          );
        });
    },

    parseGakJournal(xml) {
      let startIndex = xml.indexOf("<table");
      let finishIndex = xml.indexOf("</table>") + 8;
      let tableString = xml.slice(startIndex, finishIndex);
      return tableString;
    },

    openNotify() {
      $("#notify").modal("show");
    },
    afterCloseNotify() {
      console.log("Модалка закрылась");
      console.log(this);
      if (this.$route.path !== "/") {
        this.$router.push("/");
        // window.location.reload();
      }
    },

    checkAuth() {
      axios(this.url + "auth/get-login", {
        withCredentials: true,
      }).then(() => {
        this.openNotify();
      });
    },

    sortTable(tableName, columnIndex) {
      let table = this[tableName];
      let columnSortName = table.columnsList[columnIndex].name;
      table.pagination.page = 1;
      if (table.sortColumn === columnSortName) {
        table.ascendingSortOrder = !table.ascendingSortOrder;
      } else {
        table.ascendingSortOrder = true;
        table.sortColumn = columnSortName;
      }
      if (table.service === this.appsServiceId) {
        this.getApps();
      } else if (table.service === this.messagesServiceId) {
        this.getMessages(
          table.pagination.page,
          table.pagination.pageSize,
          this.messagesServiceId,
          columnSortName,
          !table.ascendingSortOrder
        );
      } else if (table.service === this.expertisesServiceId) {
        this.getExpertises(
          table.pagination.page,
          table.pagination.pageSize,
          this.expertisesServiceId,
          columnSortName,
          !table.ascendingSortOrder
        );
      }
    },

    async getHomeViewData() {
      await Promise.all([this.getFileResources(), this.getGakJournal()]).then(
        () => {
          console.log(
            "Получены общедоступные сведения, необходимые для главной страницы"
          );
        }
      );
      if (this.user.isAuth) {
        await Promise.all([this.getUserId(), this.getUserInfo()]).then(() => {
          console.log(
            "Получены сведения авторизованного пользователя, необходимые для главной страницы"
          );
        });
      }
    },
    async getAccountViewData(tabName) {
      console.log("getAccountViewData");
      await Promise.all([this.getUserId(), this.getUserInfo()]).then(() => {
        console.log(
          "Получены сведения сведения о пользователе, необходимые для личного кабинета"
        );
      });
      if (tabName === "basic-tab") {
        if (this.user.shortInfo.roleId === this.teacherRoleId) {
          await this.getDictionary("statusModel_2", '"Статусы заявлений"');
          await this.getApps();
          console.log(
            "Получены сведения для вкладки «Мои заявления» личного кабинета педагога"
          );
        } else if (this.user.shortInfo.roleId === this.expertRoleId) {
          await Promise.all([
            this.getDictionary("statusModel_101", '"Статусы экспертиз"'),
            this.getDictionary("resultExpert", '"Результаты экспертиз"'),
          ]);
          await this.getExpertises();
          console.log(
            "Получены сведения для вкладки «Мои экспертизы» личного кабинета эксперта"
          );
        }
      }
      if (tabName === "messages-tab") {
        await this.getDictionary("statusModel_1", '"Статусы сообщений"'),
          await this.getMessages();
        console.log(
          "Получены сведения для вкладки «Мои сообщения» личного кабинета педагога"
        );
      }
      if (tabName === "teacher-info-tab") {
        await this.getTeacher();
        console.log(
          "Получены сведения для вкладки «Личная информация» личного кабинета педагога"
        );
      }
      if (tabName === "expert-info-tab") {
        await Promise.all([
          this.getDictionary(
            "municipalEntityIrkutsk",
            '"Муниципальные образования"'
          ),
          this.getDictionary("jobGroups", '"Должность"'),
          this.getDictionary("subjectArea", '"Предметная область"'),
        ]);
        await this.getExpert();
        console.log(
          "Получены сведения для вкладки «Личная информация» личного кабинета эксперта"
        );
      }
      if (tabName === "analytics-tab") {
        console.log(
          "Получены сведения для вкладки «Аналитика» личного кабинета эксперта"
        );
      }
      if (tabName === "actions-journal-tab") {
        await this.getAudit();
        console.log(
          "Получены сведения для вкладки «Журнал действий» личного кабинета эксперта"
        );
      }
    },
  },

  watch: {
    // Получение заявлений
    // isAuthUser: function () {
    //   if (this.isAuthUser) {
    //     this.getMessages();
    //     this.getApps();
    //     this.getExpertises();
    //     this.getAudit();
    //   }
    // },
    // Заполнение селектов фильтров
  },

  async created() {
    await this.getLogin().then(() => {
      this.appLoaded = true;
    });
  },

  mounted: async function () {
    // Событие при закрытии модального окна
    $("#notify").on("hidden.bs.modal", this.afterCloseNotify);

    // Получение общих сведений
    /*Promise.all([
      this.getLogin(),
      this.getFileResources(),
      this.getGakJournal(),
      this.getAllDictionaries(),
      this.getServises(),
    ]).then(() => {
      console.log("Приложение загружено");
      this.appLoaded = true;
    });*/
  },
};
</script>
