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
        @change-message-page-size="
          changePageSize(messagesTable, $event, messagesServiceId)
        "
        @change-message-page="
          changePage(messagesTable, $event, messagesServiceId)
        "
        @invoke-action="invokeAction($event)"
        @sort-table="sortTable($event.tableName, $event.sortedColumnIndex)"
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
      urlAdd: "https://teachers.coko38.ru/api-teacher/api/",
      user: {
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
      appsTable: {
        id: "appsTable",
        service: 2,
        columnsList: [
          { title: "№ заявления", name: "id", sorted: true },
          { title: "Наименование услуги", name: "service" },
          { title: "Дата создания", name: "startDate", sorted: true },
          { title: "Номер ЕПГУ", name: "epguNumber" },
          { title: "Статус", name: "status", sorted: true },
          { title: "Дата изменения статуса", name: "statusLastUpdateDate" },
        ],
        primaryColumn: "№ заявления",
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
          },
          {
            id: "3",
            label: "Дата создания",
            type: "range",
            subtype: "date",
            itemsList: [{ label: " с" }, { label: " по" }],
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
          },
          {
            id: "5",
            label: "Статус",
            type: "select",
            itemsList: [
              { id: 1, text: "Черновик" },
              { id: 2, text: "В работе" },
              { id: 3, text: "Обработано" },
              { id: 4, text: "Архивная" },
            ],
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
          },
          {
            id: "6",
            label: "Дата изменения статуса",
            type: "range",
            subtype: "date",
            itemsList: [{ label: " c" }, { label: " по" }],
            width: 12,
            responsive: "col-sm-8 col-md-6 col-lg-4",
          },
          {
            id: "7",
            label: "Показать архивные",
            type: "checkbox",
            width: 12,
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
      messagesTable: {
        id: "messagesTable",
        service: 1,
        columnsList: [
          { title: "№ сообщения", name: "id", sorted: true },
          { title: "Дата создания сообщения", name: "startDate", sorted: true },
          { title: "Тема", name: "messageTitle" },
          { title: "Дата входа в статус", name: "statusStartDate" },
          { title: "Статус", name: "status", sorted: true },
          { title: "Срок отправки ответа", name: "responseLimit" },
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
          },
          {
            id: "2",
            label: "Дата создания сообщения",
            type: "range",
            subtype: "date",
            itemsList: [{ label: " c" }, { label: " по" }],
            width: 12,
            responsive: "col-sm-8 col-md-6 col-lg-4",
          },
          {
            id: "3",
            label: "Тема",
            type: "input",
            subtype: "Тема",
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
          },
          {
            id: "4",
            label: "Дата входа в статус",
            type: "range",
            subtype: "date",
            itemsList: [{ label: " с" }, { label: " по" }],
            width: 12,
            responsive: "col-sm-8 col-md-6 col-lg-4",
          },
          {
            id: "5",
            label: "Статус",
            type: "select",
            itemsList: [
              { id: 1, text: "Черновик" },
              { id: 2, text: "В работе" },
              { id: 3, text: "Обработано" },
              { id: 4, text: "Архивная" },
            ],
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
          },
          {
            id: "6",
            label: "Срок отправки ответа",
            type: "range",
            subtype: "date",
            itemsList: [{ label: " с" }, { label: " по" }],
            width: 12,
            responsive: "col-sm-8 col-md-6 col-lg-4",
          },
          {
            id: "7",
            label: "Показать только архивные",
            type: "checkbox",
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
      expertisesServiceId: 2,
      expertisesTable: {
        id: "expertisesTable",
        columnsList: [
          { title: "№ заявления", name: "id", sorted: true },
          {
            title: "ФИО педагогического работника",
            name: "teacherFullname",
            sorted: true,
          },
          {
            title: "Дата начала экспертизы",
            name: "expertiseStartDate",
            sorted: true,
          },
          {
            title: "Срок окончания экспертизы",
            name: "expertiseLimit",
            sorted: true,
          },
          {
            title: "Дата окончания экспертизы",
            name: "expertiseFinishDate",
            sorted: true,
          },
          {
            title: "Результат экспертизы",
            name: "expertiseResult",
            sorted: true,
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
        sortColumn: "",
        ascendingSortOrder: false,
        filters: [
          {
            id: "1",
            label: "№ заявления",
            type: "input",
            subtype: "number",
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
          },
          {
            id: "2",
            label: "ФИО пед.работника",
            type: "input",
            subtype: "text",
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
          },
          {
            id: "3",
            label: "Дата начала экспертизы",
            type: "range",
            subtype: "date",
            itemsList: [{ label: " с" }, { label: " по" }],
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
          },
          {
            id: "5",
            label: "Дата окончания экспертизы",
            type: "range",
            subtype: "date",
            itemsList: [{ label: " с" }, { label: " по" }],
            width: 12,
            responsive: "col-sm-8 col-md-6 col-lg-4",
          },
          {
            id: "6",
            label: "Результат экспертизы",
            type: "select",
            itemsList: [
              { id: 1, text: "Положительный" },
              { id: 2, text: "Отрицательный" },
              { id: 3, text: "Прекращена" },
            ],
            width: 12,
            responsive: "col-sm-4 col-md-3 col-lg-2",
          },
          {
            id: "7",
            label: "Статус",
            type: "select",
            itemsList: [
              { id: 1, text: "Черновик" },
              { id: 2, text: "В работе" },
              { id: 3, text: "Обработано" },
              { id: 4, text: "Архивная" },
            ],
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
          { title: "Id записи", name: "id", sorted: true },
          { title: "Дата события", name: "eventDate", sorted: true },
          { title: "Описание", name: "eventDescription", sorted: true },
          { title: "Событие", name: "eventName", sorted: true },
          { title: "Изменения", name: "eventChanges", sorted: true },
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
        sortColumn: "",
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
          title:
            "Адрес: 664025, Иркутская область, город Иркутск, Российская ул, д. 21",
          type: "text",
          link: "/",
        },
        {
          id: 3,
          title: "Телефон: +7 (999) 999-99 99",
          type: "text",
          link: "/",
        },
        {
          id: 4,
          title: "Электронная почта: test@mail.ru",
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
        municipalEntityIrkutsk: [],
        DocumentPersonal: [],
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
  },

  methods: {
    // Косвенная проверка авторизованности пользователя, получение ссылки на вход через ЕСИА (переход на страницу авторизации ЕСИА - вынести во внешний метод)
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
              this.getUserId();
              this.getUserInfo();
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
    getUserId() {
      axios(this.url + "auth/get-user", {
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
    getUserInfo() {
      axios(this.url + "core/get-user", {
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
      if (this.user.shortInfo.roleId === this.teacherRoleId) {
        this.getTeacher();
      } else if (this.user.shortInfo.roleId === this.expertRoleId) {
        this.getExpert();
      }
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

    // Очистка ошибок
    cleanSignInForm() {
      // this.login = "";
      // this.password = "";
      this.authError.type = "";
      this.authError.text = "";
    },

    // Получение списка заявлений пользователя
    getApps(
      page = this.appsTable.pagination.pageSize,
      pageSize = this.appsTable.pagination.pageSize,
      servId = this.appsServiceId,
      sortCol = this.appsTable.sortColumn,
      sortDesc = !this.appsTable.ascendingSortOrder,
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
          this.appsTable.pagination.itemsTotal = response.data.totalElements;
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
      page = this.messagesTable.pagination.pageSize,
      pageSize = this.messagesTable.pagination.pageSize,
      servId = this.messagesServiceId,
      sortCol = this.messagesTable.sortColumn,
      sortDesc = !this.messagesTable.ascendingSortOrder,
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
          console.groupCollapsed("Список сообщений");
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

    // Список записей журнала действий
    getAudit() {
      axios
        .get("https://teachers.coko38.ru/api-teacher/api/data/getAudit", {
          withCredentials: true,
        })
        .then((response) => {
          console.log(response.data);
          let logsTableRows = this.logsConvertToTable(response.data);
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
    getStartForm(serviceId, id) {
      if (serviceId === this.appsServiceId) {
        this.loaderStart(this.appsLoader, "Загрузка формы заявления");
      } else if (serviceId === this.messagesServiceId) {
        this.loaderStart(this.messagesLoader, "Загрузка формы сообщения");
      }
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
        if (serviceId === this.appsServiceId) {
          console.log("Запрос формы существующего заявления");
        } else if (serviceId === this.messagesServiceId) {
          console.log("Запрос формы существующего сообщения");
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
          if (serviceId === this.appsServiceId) {
            this.loaderFinish(this.appsLoader);
          } else if (serviceId === this.messagesServiceId) {
            this.loaderFinish(this.messagesLoader);
          }
          console.log("Опции стартовой формы");
          console.log(this);
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
            this.loaderFinish(this.appsLoader);
          } else if (modelId === this.messagesServiceId) {
            this.loaderFinish(this.messagesLoader);
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

    // Редактирование профиля
    editProfile() {
      this.loaderStart(this.profileLoader, "Редактирование личных данных");
      setTimeout(this.loaderFinish, this.loadersDelay, this.profileLoader);
    },

    // Заявления
    createNewApp(appsServiceId) {
      this.getStartForm(appsServiceId);
      this.loaderStart(this.appsLoader, "Загрузка формы заявления");
      setTimeout(this.loaderFinish, this.loadersDelay, this.appsLoader);
    },
    openExistingApp(appsServiceId, appId) {
      this.getStartForm(appsServiceId, appId);
      this.loaderStart(this.appsLoader, "Загрузка заявления");
      setTimeout(this.loaderFinish, this.loadersDelay, this.appsLoader);
    },

    // Сообщения
    createNewMessage(appsServiceId) {
      this.getStartForm(appsServiceId);
      this.loaderStart(this.messagesLoader, "Загрузка формы сообщения");
      setTimeout(this.loaderFinish, this.loadersDelay, this.messagesLoader);
    },
    openExistingMessage(appsServiceId, appId) {
      console.log(appsServiceId);
      console.log(appId);
      this.getStartForm(appsServiceId, appId);
      this.loaderStart(this.messagesLoader, "Загрузка сообщения");
      setTimeout(this.loaderFinish, this.loadersDelay, this.messagesLoader);
    },

    // "Экспертизы"
    openExistingExpertise(appsServiceId, appId) {
      // this.getStartForm(appsServiceId, appId);
      console.log(appsServiceId + " - " + appId);
      this.loaderStart(this.expertisesLoader, "Загрузка деталей экспертизы");
      setTimeout(this.loaderFinish, this.loadersDelay, this.expertisesLoader);
    },

    // Журнал сообщений
    openExistingLog(appsServiceId, appId) {
      // this.getStartForm(appsServiceId, appId);
      console.log(appsServiceId + " - " + appId);
      this.loaderStart(this.logsLoader, "Загрузка записи журнала");
      setTimeout(this.loaderFinish, this.loadersDelay, this.logsLoader);
    },

    // Получение списка мер
    getServises() {
      axios
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
    getTeacher() {
      axios
        .get("https://teachers.coko38.ru/teachapp/api/teacher/get", {
          withCredentials: true,
        })
        .then((response) => {
          this.teacherInfo = response.data;
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
        .post(
          "https://teachers.coko38.ru/teachapp/api/teacher/edit",
          this.teacherInfo,
          {
            withCredentials: true,
          }
        )
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
    getExpert() {
      axios
        .get("https://teachers.coko38.ru/teachapp/api/expert/get", {
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
      this.expertInfo.subjectArea = JSON.stringify(
        this.teacherInfo.subjectArea
      );
      axios
        .post(
          "https://teachers.coko38.ru/teachapp/api/expert/edit",
          this.expertInfo,
          {
            withCredentials: true,
          }
        )
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
    getAllDictionaries() {
      this.getDictionary("gender", '"Пол"');
      this.getDictionary("subjectArea", '"Предметная область"');
      this.getDictionary(
        "municipalEntityIrkutsk",
        '"Муниципальные образования"'
      );
      this.getDictionary(
        "DocumentPersonal",
        '"Документы, удостоверяющие личность"'
      );
    },
    getDictionary(dictionaryCode, logComment) {
      axios
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

    // Файлы для работы с системой
    getFileResources() {
      axios
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
    getGakJournal() {
      axios
        .get(this.urlAdd + "data/getGakJournal", {
          withCredentials: true,
        })
        .then((response) => {
          // this.dictionaries[dictionaryCode] = this.convertDictionary(
          //   response.data
          // );
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
        this.getApps(
          table.pagination.page,
          table.pagination.pageSize,
          this.appsServiceId,
          columnSortName,
          !table.ascendingSortOrder
        );
      } else if (table.service === this.messagesServiceId) {
        this.getMessages(
          table.pagination.page,
          table.pagination.pageSize,
          this.messagesServiceId,
          columnSortName,
          !table.ascendingSortOrder
        );
      }
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
        this.getAudit();
        // this.authIntervalId = setInterval(this.checkAuth, 30000);
      } else {
        // clearInterval(this.authIntervalId);
      }
    },
  },

  mounted: function () {
    // Событие при закрытии модального окна
    $("#notify").on("hidden.bs.modal", this.afterCloseNotify);

    // Запрос информации о пользователе
    this.getLogin();

    // Получение файлов для работы
    this.getFileResources();

    // Получение расписания аттестации
    this.getGakJournal();

    // Получение справочников
    this.getAllDictionaries();

    // Получение бизнес-процессов (для справки)
    this.getServises();
  },
};
</script>
