<template>
  <div id="account" class="d-flex flex-column flex-grow-1">
    <div class="container d-flex flex-column flex-grow-1">
      <!--      Вкладки педагога-->
      <template v-if="user.shortInfo.roleId === 39707">
        <ul class="nav nav-tabs" id="teacher-tab" role="tablist">
          <li class="nav-item" role="presentation">
            <button
              class="nav-link active"
              id="app-teacher-tab"
              data-toggle="tab"
              data-target="#app-teacher-tab-pane"
              type="button"
              role="tab"
              aria-controls="app-teacher-tab-pane"
              aria-selected="false"
            >
              Мои заявления
            </button>
          </li>
          <li class="nav-item" role="presentation">
            <button
              class="nav-link"
              id="messages-teacher-tab"
              data-toggle="tab"
              data-target="#messages-teacher-tab-pane"
              type="button"
              role="tab"
              aria-controls="messages-teacher-tab-pane"
              aria-selected="false"
            >
              Мои сообщения
            </button>
          </li>
          <li class="nav-item" role="presentation">
            <button
              class="nav-link"
              id="profile-teacher-tab"
              data-toggle="tab"
              data-target="#profile-teacher-tab-pane"
              type="button"
              role="tab"
              aria-controls="profile-teacher-tab-pane"
              aria-selected="true"
            >
              Личная информация
            </button>
          </li>
        </ul>
        <div
          class="tab-content flex-grow-1 position-relative"
          id="teacherTabContent"
        >
          <div
            class="tab-pane fade show active pt-3"
            id="app-teacher-tab-pane"
            role="tabpanel"
            aria-labelledby="app-teacher-tab"
            tabindex="0"
          >
            <button
              class="btn btn-primary mb-3"
              @click="
                showAppForm('Новое заявление', 'Загрузка формы заявления', 2)
              "
            >
              Подать заявление
            </button>
            <TableBootstrapCustomBS46
              hover
              bordered
              pagination
              filter
              :table-data="appsTable"
              :items-total="appsTable.pagination.itemsTotal"
              :page="appsTable.pagination.page"
              :page-size="appsTable.pagination.pageSize"
              :items-per-page="appsTable.pagination.itemsPerPage"
              @row-click="
                showAppForm('Детали заявления', 'Загрузка заявления', 2, $event)
              "
              @sort-table="
                $emit('sort-table', {
                  tableName: 'appsTable',
                  sortedColumnIndex: $event,
                })
              "
              @change-page-size="$emit('change-apps-page-size', $event)"
              @change-page="$emit('change-apps-page', $event)"
              @change-filter="$emit('change-apps-filter', $event)"
              @apply-filter="$emit('apply-apps-filter')"
              @clear-filter="$emit('clear-apps-filter')"
            />
          </div>
          <div
            class="tab-pane fade pt-3"
            id="messages-teacher-tab-pane"
            role="tabpanel"
            aria-labelledby="messages-teacher-tab"
            tabindex="0"
          >
            <button
              class="btn btn-primary mb-3"
              @click="
                showAppForm('Новое сообщение', 'Загрузка формы сообщения', 1)
              "
            >
              Отправить сообщение
            </button>
            <TableBootstrapCustomBS46
              pagination
              filter
              hover
              bordered
              :table-data="messagesTable"
              :items-total="messagesTable.pagination.itemsTotal"
              :page="messagesTable.pagination.page"
              :page-size="messagesTable.pagination.pageSize"
              :items-per-page="messagesTable.pagination.itemsPerPage"
              :dictionaries="dictionaries"
              @row-click="
                showAppForm('Детали сообщения', 'Загрузка сообщения', 1, $event)
              "
              @sort-table="
                $emit('sort-table', {
                  tableName: 'messagesTable',
                  sortedColumnIndex: $event,
                })
              "
              @change-page-size="$emit('change-message-page-size', $event)"
              @change-page="$emit('change-message-page', $event)"
              @change-filter="$emit('change-messages-filter', $event)"
              @apply-filter="$emit('apply-messages-filter')"
              @clear-filter="$emit('clear-messages-filter')"
            />
          </div>
          <div
            class="tab-pane fade pt-3"
            id="profile-teacher-tab-pane"
            role="tabpanel"
            aria-labelledby="profile-teacher-tab"
            tabindex="0"
          >
            <LoaderBootstrapCustomBS46
              v-if="profileLoader.isLoading && !profileLoader.isResponse"
              :comment="profileLoader.comment"
              :theme="profileLoader.theme"
              class="position-absolute"
            />
            <template v-else>
              <div class="alert alert-danger mb-3" role="alert">
                Данный раздел находится в разработке. Просьба изменения не
                вносить!
              </div>
              <div class="accordion" id="accordionTeacherInfo">
                <div class="card">
                  <div class="card-header p-0" id="teacherBasicInfoHeader">
                    <button
                      class="btn btn-link btn-block p-3 text-left"
                      type="button"
                      data-toggle="collapse"
                      data-target="#teacherBasicInfo"
                      aria-expanded="true"
                      aria-controls="teacherBasicInfo"
                    >
                      Основные сведения
                    </button>
                  </div>
                  <div
                    id="teacherBasicInfo"
                    class="collapse show pt-3 px-3 pb-0"
                    aria-labelledby="teacherBasicInfoHeader"
                    data-parent="#teacherBasicInfo"
                  >
                    <div class="accordion-body">
                      <form class="row">
                        <InputBootstrapCustomBS46
                          label="Фамилия"
                          id="familyName"
                          :value="teacherProfile.familyName"
                          type="text"
                          :width-group="12"
                          :responsive="'col-sm-4 col-md-3 col-lg-2'"
                          disabled
                        />
                        <InputBootstrapCustomBS46
                          label="Имя"
                          id="name"
                          :value="teacherProfile.name"
                          type="text"
                          :width-group="12"
                          :responsive="'col-sm-4 col-md-3 col-lg-2'"
                          disabled
                        />
                        <InputBootstrapCustomBS46
                          label="Отчество"
                          id="middleName"
                          :value="teacherProfile.middleName"
                          type="text"
                          :width-group="12"
                          :responsive="'col-sm-4 col-md-3 col-lg-2'"
                          disabled
                        />
                        <InputBootstrapCustomBS46
                          label="Дата рождения"
                          id="birthday"
                          :value="teacherProfile.birthday.substr(0, 10)"
                          type="date"
                          :width-group="12"
                          :responsive="'col-sm-4 col-md-3 col-xl-2'"
                          disabled
                        />
                        <InputBootstrapCustomBS46
                          label="СНИЛС"
                          id="snils"
                          :value="teacherProfile.snils"
                          type="text"
                          :width-group="12"
                          :responsive="'col-sm-4 col-md-3 col-lg-2'"
                          disabled
                        />
                        <!--                        <InputBootstrapCustomBS46-->
                        <!--                          label="Гражданство"-->
                        <!--                          id="citizenship"-->
                        <!--                          :value="teacherProfile.citizenship"-->
                        <!--                          type="text"-->
                        <!--                          :width-group="12"-->
                        <!--                          :responsive="'col-sm-6 col-md-4 col-lg-3'"-->
                        <!--                          disabled-->
                        <!--                        />-->
                        <InputBootstrapCustomBS46
                          label="Электронная почта"
                          id="mail"
                          :value="teacherProfile.mail"
                          type="text"
                          :width-group="12"
                          :responsive="'col-sm-6 col-md-5 col-lg-3'"
                          :disabled="!editableProfile"
                          @input="teacherProfile.mail = $event"
                        />
                        <InputBootstrapCustomBS46
                          label="Телефон"
                          id="phone"
                          :value="teacherProfile.phone"
                          type="text"
                          :width-group="12"
                          :responsive="'col-sm-6 col-md-4 col-lg-3'"
                          :disabled="!editableProfile"
                          @input="teacherProfile.phone = $event"
                        />
                        <!--                        <SelectBootstrapCustomBS46-->
                        <!--                          label="Пол"-->
                        <!--                          id="gender"-->
                        <!--                          :itemsList="dictionaries.gender"-->
                        <!--                          :value="teacherProfile.gender"-->
                        <!--                          :width-group="12"-->
                        <!--                          :responsive="'col-sm-4 col-md-3 col-lg-2'"-->
                        <!--                          disabled-->
                        <!--                        />-->
                        <!--                        <SelectBootstrapCustomBS46-->
                        <!--                          label="Муниципальное образование"-->
                        <!--                          id="municipality"-->
                        <!--                          :itemsList="dictionaries.municipalEntityIrkutsk"-->
                        <!--                          :value="teacherProfile.municipality"-->
                        <!--                          :width-group="12"-->
                        <!--                          :responsive="'col-sm-6 col-md-5 col-lg-4'"-->
                        <!--                          :disabled="!editableProfile"-->
                        <!--                          @change="teacherProfile.municipality = $event"-->
                        <!--                        />-->
                        <!--                        <SelectBootstrapCustomBS46-->
                        <!--                          label="Предметная область"-->
                        <!--                          id="subjectArea"-->
                        <!--                          :itemsList="dictionaries.subjectArea"-->
                        <!--                          :value="teacherProfile.subjectArea"-->
                        <!--                          :width-group="12"-->
                        <!--                          :responsive="'col-sm-6 col-md-5 col-lg-4'"-->
                        <!--                          :disabled="!editableProfile"-->
                        <!--                          multiple-->
                        <!--                          badges-->
                        <!--                          @change="teacherProfile.subjectArea = $event"-->
                        <!--                        />-->
                      </form>
                    </div>
                  </div>
                </div>
                <div class="card d-none">
                  <div class="card-header p-0" id="teacherAddressHeader">
                    <button
                      class="btn btn-link btn-block p-3 text-left"
                      type="button"
                      data-toggle="collapse"
                      data-target="#teacherAddress"
                      aria-expanded="true"
                      aria-controls="teacherAddress"
                    >
                      Адрес
                    </button>
                  </div>
                  <div
                    id="teacherAddress"
                    class="collapse show pt-3 px-3 pb-0"
                    aria-labelledby="teacherAddressHeader"
                    data-parent="#teacherBasicInfo"
                  >
                    <div class="accordion-body">
                      <form class="row">
                        <InputBootstrapCustomBS46
                          label="Адрес регистрации"
                          id="regAddress"
                          :value="teacherProfile.regAddress"
                          type="text"
                          :width-group="12"
                          :responsive="'col-md-6'"
                          :disabled="!editableProfile"
                          @input="teacherProfile.regAddress = $event"
                        />
                        <InputBootstrapCustomBS46
                          label="Адрес места жительства"
                          id="factAddress"
                          :value="teacherProfile.factAddress"
                          type="text"
                          :width-group="12"
                          :responsive="'col-md-6'"
                          :disabled="!editableProfile"
                          @input="teacherProfile.factAddress = $event"
                        />
                      </form>
                    </div>
                  </div>
                </div>
                <div class="card d-none">
                  <div class="card-header p-0" id="teacherDocsHeader">
                    <button
                      class="btn btn-link btn-block p-3 text-left"
                      type="button"
                      data-toggle="collapse"
                      data-target="#teacherDocs"
                      aria-expanded="true"
                      aria-controls="teacherDocs"
                    >
                      Документ, удостоверяющий личность
                    </button>
                  </div>
                  <div
                    id="teacherDocs"
                    class="collapse show pt-3 px-3 pb-0"
                    aria-labelledby="teacherDocsHeader"
                    data-parent="#teacherBasicInfo"
                  >
                    <div class="accordion-body">
                      <form class="row">
                        <SelectBootstrapCustomBS46
                          label="Наименование документа"
                          id="docname"
                          :itemsList="dictionaries.DocumentPersonal"
                          :value="teacherProfile.docName"
                          :width-group="12"
                          :responsive="'col-sm-8 col-md-8 col-lg-5'"
                          :disabled="!editableProfile"
                          @change="teacherProfile.docName = $event"
                        />
                        <InputBootstrapCustomBS46
                          label="Серия"
                          id="factAddress"
                          :value="teacherProfile.docSeries"
                          type="text"
                          :width-group="12"
                          :responsive="'col-sm-4 col-md-3 col-lg-2'"
                          :disabled="!editableProfile"
                          @input="teacherProfile.docSeries = $event"
                        />
                        <InputBootstrapCustomBS46
                          label="Номер"
                          id="factAddress"
                          :value="teacherProfile.docNumber"
                          type="text"
                          :width-group="12"
                          :responsive="'col-sm-4 col-md-3 col-lg-2'"
                          :disabled="!editableProfile"
                          @input="teacherProfile.docNumber = $event"
                        />
                        <InputBootstrapCustomBS46
                          label="Дата выдачи"
                          id="factAddress"
                          :value="teacherProfile.issueDate.substr(0, 10)"
                          type="date"
                          :width-group="12"
                          :responsive="'col-sm-4 col-md-3 col-xl-2'"
                          :disabled="!editableProfile"
                          @input="teacherProfile.issueDate = $event"
                        />
                        <InputBootstrapCustomBS46
                          label="Срок действия"
                          id="factAddress"
                          :value="teacherProfile.docValidate.substr(0, 10)"
                          type="date"
                          :width-group="12"
                          :responsive="'col-sm-4 col-md-3 col-xl-2'"
                          :disabled="!editableProfile"
                          @input="teacherProfile.docValidate = $event"
                        />
                        <InputBootstrapCustomBS46
                          label="Кем выдан"
                          id="factAddress"
                          :value="teacherProfile.issueOrg"
                          type="text"
                          :width-group="12"
                          :responsive="'col-md-6 col-md-8'"
                          :disabled="!editableProfile"
                          @input="teacherProfile.issueOrg = $event"
                        />
                      </form>
                    </div>
                  </div>
                </div>
                <div class="card">
                  <div class="card-header p-0" id="teacherMailingHeader">
                    <button
                      class="btn btn-link btn-block p-3 text-left"
                      type="button"
                      data-toggle="collapse"
                      data-target="#teacherMailing"
                      aria-expanded="true"
                      aria-controls="teacherMailing"
                    >
                      Уведомления
                    </button>
                  </div>
                  <div
                    id="teacherMailing"
                    class="collapse show pt-3 px-3 pb-0"
                    aria-labelledby="teacherMailingHeader"
                    data-parent="#teacherBasicInfo"
                  >
                    <div class="accordion-body">
                      <form class="row">
                        <CheckboxBootstrapCustomBS46
                          label="Я согласен(а) на получение уведомлений на электронную почту"
                          id="mailAgree"
                          :value="teacherProfile.mailAgree"
                          :width-group="12"
                          :disabled="!editableProfile"
                          @change="teacherProfile.mailAgree = $event"
                        />
                      </form>
                    </div>
                  </div>
                </div>
              </div>
              <button
                v-if="!editableProfile"
                class="btn btn-primary mt-3"
                @click="editProfile"
              >
                Редактировать профиль
              </button>
              <button v-else class="btn btn-primary mt-3" @click="setTeacher">
                Сохранить профиль
              </button>
            </template>
          </div>
        </div>
      </template>

      <!--      Вкладки эксперта-->
      <template v-if="user.shortInfo.roleId === 39927">
        <ul class="nav nav-tabs" id="expert-tab" role="tablist">
          <li class="nav-item" role="presentation">
            <button
              class="nav-link active"
              id="expertises-expert-tab"
              data-toggle="tab"
              data-target="#expertises-expert-tab-pane"
              type="button"
              role="tab"
              aria-controls="expertises-expert-tab-pane"
              aria-selected="false"
            >
              Мои экспертизы
            </button>
          </li>
          <li class="nav-item" role="presentation">
            <button
              class="nav-link"
              id="profile-expert-tab"
              data-toggle="tab"
              data-target="#profile-expert-tab-pane"
              type="button"
              role="tab"
              aria-controls="profile-expert-tab-pane"
              aria-selected="true"
            >
              Личная информация
            </button>
          </li>
          <li class="nav-item" role="presentation">
            <button
              class="nav-link"
              id="analitics-expert-tab"
              data-toggle="tab"
              data-target="#analitics-expert-tab-pane"
              type="button"
              role="tab"
              aria-controls="analitics-expert-tab-pane"
              aria-selected="false"
            >
              Аналитика
            </button>
          </li>
          <li class="nav-item" role="presentation">
            <button
              class="nav-link"
              id="actions-expert-tab"
              data-toggle="tab"
              data-target="#actions-expert-tab-pane"
              type="button"
              role="tab"
              aria-controls="actions-expert-tab-pane"
              aria-selected="false"
            >
              Журнал действий
            </button>
          </li>
        </ul>
        <div
          class="tab-content flex-grow-1 position-relative"
          id="expertTabContent"
        >
          <div
            class="tab-pane fade show active pt-3"
            id="expertises-expert-tab-pane"
            role="tabpanel"
            aria-labelledby="expertises-expert-tab"
            tabindex="0"
          >
            <TableBootstrapCustomBS46
              pagination
              filter
              hover
              bordered
              :table-data="expertisesTable"
              :items-total="expertisesTable.pagination.itemsTotal"
              :page="expertisesTable.pagination.page"
              :page-size="expertisesTable.pagination.pageSize"
              :items-per-page="expertisesTable.pagination.itemsPerPage"
              @row-click="
                showAppForm(
                  'Детали экспертизы',
                  'Загрузка деталей экспертизы',
                  101,
                  $event
                )
              "
              @sort-table="
                $emit('sort-table', {
                  tableName: 'expertisesTable',
                  sortedColumnIndex: $event,
                })
              "
              @change-page-size="$emit('change-expertises-page-size', $event)"
              @change-page="$emit('change-expertises-page', $event)"
              @change-filter="$emit('change-expertises-filter', $event)"
              @apply-filter="$emit('apply-expertises-filter')"
              @clear-filter="$emit('clear-expertises-filter')"
            />
          </div>
          <div
            class="tab-pane fade pt-3"
            id="profile-expert-tab-pane"
            role="tabpanel"
            aria-labelledby="profile-expert-tab"
            tabindex="0"
          >
            <LoaderBootstrapCustomBS46
              v-if="profileLoader.isLoading && !profileLoader.isResponse"
              :comment="profileLoader.comment"
              :theme="profileLoader.theme"
              class="position-absolute"
            />
            <template v-else>
              <form class="row g-3">
                <InputBootstrapCustomBS46
                  label="Фамилия"
                  id="ex-familyName"
                  :value="expertProfile.familyName"
                  type="text"
                  :width-group="12"
                  :responsive="'col-sm-4 col-md-3 col-lg-2'"
                  disabled
                />
                <InputBootstrapCustomBS46
                  label="Имя"
                  id="ex-name"
                  :value="expertProfile.name"
                  type="text"
                  :width-group="12"
                  :responsive="'col-sm-4 col-md-3 col-lg-2'"
                  disabled
                />
                <InputBootstrapCustomBS46
                  label="Отчество"
                  id="ex-middleName"
                  :value="expertProfile.middleName"
                  type="text"
                  :width-group="12"
                  :responsive="'col-sm-4 col-md-3 col-lg-2'"
                  disabled
                />
                <InputBootstrapCustomBS46
                  label="СНИЛС"
                  id="ex-snils"
                  :value="expertProfile.snils"
                  type="text"
                  :width-group="12"
                  :responsive="'col-sm-4 col-md-3 col-lg-2'"
                  disabled
                />
                <InputBootstrapCustomBS46
                  label="Электронная почта"
                  id="ex-mail"
                  :value="expertProfile.mail"
                  type="text"
                  :width-group="12"
                  :responsive="'col-sm-6 col-md-5 col-lg-3'"
                  :disabled="!editableProfile"
                  @input="expertProfile.mail = $event"
                />
                <InputBootstrapCustomBS46
                  label="Телефон"
                  id="ex-phone"
                  :value="expertProfile.phone"
                  type="text"
                  :width-group="12"
                  :responsive="'col-sm-6 col-md-4 col-lg-3'"
                  :disabled="!editableProfile"
                  @input="expertProfile.phone = $event"
                />
                <SelectBootstrapCustomBS46
                  label="Муниципальное образование"
                  id="ex-municipality"
                  :itemsList="dictionaries.municipalEntityIrkutsk"
                  :value="expertProfile.municipality"
                  :width-group="12"
                  :responsive="'col-sm-6 col-md-5 col-lg-4'"
                  :disabled="!editableProfile"
                  @change="expertProfile.municipality = $event"
                />
                <SelectBootstrapCustomBS46
                  label="Должность"
                  id="ex-position"
                  :itemsList="dictionaries.jobGroups"
                  :value="expertProfile.position"
                  :width-group="12"
                  :responsive="'col-sm-4 col-md-3 col-lg-2'"
                  :disabled="!editableProfile"
                  @change="expertProfile.position = $event"
                />
                <!--                <InputBootstrapCustomBS46-->
                <!--                  label="Должность"-->
                <!--                  id="ex-position"-->
                <!--                  :value="expertProfile.position"-->
                <!--                  type="text"-->
                <!--                  :width-group="12"-->
                <!--                  :responsive="'col-sm-4 col-md-3 col-lg-2'"-->
                <!--                  :disabled="!editableProfile"-->
                <!--                  @input="expertProfile.position = $event"-->
                <!--                />-->
                <InputBootstrapCustomBS46
                  label="Организация"
                  id="ex-organization"
                  :value="expertProfile.organization"
                  type="text"
                  :width-group="12"
                  :responsive="'col-sm-4 col-md-3 col-lg-2'"
                  :disabled="!editableProfile"
                  @input="expertProfile.organization = $event"
                />
                <SelectBootstrapCustomBS46
                  label="Предметная область"
                  id="ex-subjectArea"
                  :itemsList="dictionaries.subjectArea"
                  :value="expertProfile.subjectArea"
                  :width-group="12"
                  :responsive="'col-sm-6 col-md-5 col-lg-4'"
                  :disabled="!editableProfile"
                  multiple
                  badges
                  @change="expertProfile.subjectArea = $event"
                />
                <InputBootstrapCustomBS46
                  label="Рейтинг"
                  id="ex-rating"
                  :value="expertProfile.rating"
                  type="text"
                  :width-group="12"
                  :responsive="'col-sm-4 col-md-3 col-lg-2'"
                  disabled
                />
                <CheckboxBootstrapCustomBS46
                  label="Активен"
                  id="ex-status"
                  :value="expertProfile.status"
                  :width-group="12"
                  :disabled="!editableProfile"
                  @change="expertProfile.status = $event"
                />
                <CheckboxBootstrapCustomBS46
                  label="Я согласен(а) на получение уведомлений на электронную почту"
                  id="ex-mailAgree"
                  :value="expertProfile.mailAgree"
                  :width-group="12"
                  :disabled="!editableProfile"
                  @change="expertProfile.mailAgree = $event"
                />
              </form>
              <button
                v-if="!editableProfile"
                class="btn btn-primary"
                @click="editProfile"
              >
                Редактировать профиль
              </button>
              <button v-else class="btn btn-primary" @click="setExpert">
                Сохранить профиль
              </button>
            </template>
          </div>

          <div
            class="tab-pane fade pt-3"
            id="analitics-expert-tab-pane"
            role="tabpanel"
            aria-labelledby="analitics-expert-tab"
            tabindex="0"
          >
            <TableBootstrapCustomBS46
              hover
              bordered
              :table-data="analyticsTable"
            />
          </div>
          <div
            class="tab-pane fade pt-3"
            id="actions-expert-tab-pane"
            role="tabpanel"
            aria-labelledby="actions-expert-tab"
            tabindex="0"
          >
            <TableBootstrapCustomBS46
              hover
              bordered
              :table-data="logsTable"
              @sort-table="sortTable(logsTable, $event)"
            />
          </div>
        </div>
      </template>

      <ModalBootstrapCustom46
        :modal-id="modal.modalId"
        header
        size="xl"
        scrollable
      >
        <template v-slot:modal-title>
          {{ modal.modalTitle }}
        </template>
        <template v-slot:modal-body>
          <LoaderBootstrapCustomBS46
            v-if="appsLoader.isLoading"
            :comment="appsLoader.comment"
            :theme="appsLoader.theme"
          />
          <div v-show="!appsLoader.isLoading && appsLoader.isResponse">
            <form class="application-form">
              <div class="row">
                <div class="col-10">
                  <vb-alert v-if="actionError" type="danger" class="mb-4">
                    <div>{{ actionError }}</div>
                  </vb-alert>
                  <div id="formio" ref="vueForm"></div>
                </div>
                <div
                  v-if="
                    service.applicationDTO.form.actions &&
                    service.applicationDTO.form.actions.length > 0
                  "
                  class="col-2 action-buttons"
                >
                  <template
                    v-for="action of service.applicationDTO.form.actions"
                  >
                    <template
                      v-if="
                        service.applicationDTO.active || action.alwaysActive
                      "
                    >
                      <button
                        :key="action.id"
                        type="button"
                        class="btn btn-block btn-primary"
                        @click="invokeAction(action)"
                      >
                        {{ action.name }}
                      </button>
                      <!--<button
                        v-if="action.backAction"
                        :key="action.id"
                        type="button"
                        class="btn btn-block btn-primary"
                        @click="
                          $emit('invoke-action', {
                            actionId: action.id,
                            backAction: true,
                            modelId: appForm.modelId,
                          })
                        "
                      >
                        {{ action.name }}
                      </button>
                      <button
                        v-else
                        :key="action.id"
                        type="button"
                        class="btn btn-block btn-primary"
                        @click="
                          $emit('invoke-action', {
                            actionId: action.id,
                            backAction: false,
                            modelId: appForm.modelId,
                          })
                        "
                      >
                        {{ action.name }}
                      </button>-->
                    </template>
                  </template>
                </div>
              </div>
            </form>
          </div>
        </template>
      </ModalBootstrapCustom46>
      <crypto-pro-signature-modal
        modal-id="cryptoProSignatureModal"
        :crypto-pro-signature="cryptoProSignature"
        @get-hash-to-sign="getHashToSign"
      />
      <vb-modal id="confirm-action-modal"
        ><div class="confirm-modal-header">
          {{ currentAction.confirmData.header }}
        </div>
        <div class="confirm-modal-body">
          {{ currentAction.confirmData.message }}
        </div>
        <div class="row row-cols-2">
          <div class="col">
            <button
              type="button"
              class="btn btn-block btn-danger"
              @click="confirmAction"
            >
              {{ currentAction.confirmData.confirmButton }}
            </button>
          </div>
          <div class="col">
            <button
              type="button"
              class="btn btn-block btn-outline-secondary"
              @click="hideModalBS('confirm-action-modal')"
            >
              {{ currentAction.confirmData.cancelButton }}
            </button>
          </div>
        </div></vb-modal
      >
    </div>
  </div>
</template>

<script>
import $ from "jquery";
import axios from "axios";
import { Modal } from "bootstrap";
import ModalBootstrapCustom46 from "../components/universal/ModalBootstrapCustomBS46";
import TableBootstrapCustomBS46 from "../components/universal/TableBootstrapCustomBS46";
import LoaderBootstrapCustomBS46 from "../components/universal/LoaderBootstrapCustomBS46";
import InputBootstrapCustomBS46 from "../components/universal/Forms/InputBootstrapCustomBS46";
import CheckboxBootstrapCustomBS46 from "../components/universal/Forms/CheckboxBootstrapCustomBS46";
import SelectBootstrapCustomBS46 from "../components/universal/Forms/SelectBootstrapCustomBS46";
import VbAlert from "../components/universal/BS46Alert";
import VbModal from "../components/universal/BS46Modal";
import CryptoProSignatureModal from "../components/CryptoProSignatureModal";

export default {
  name: "AccountView",
  components: {
    CryptoProSignatureModal,
    VbModal,
    VbAlert,
    SelectBootstrapCustomBS46,
    CheckboxBootstrapCustomBS46,
    InputBootstrapCustomBS46,
    LoaderBootstrapCustomBS46,
    TableBootstrapCustomBS46,
    ModalBootstrapCustom46,
  },
  props: [
    "user",
    "isAuthUser",
    "teacherInfo",
    "expertInfo",
    "profileLoader",
    "appsTable",
    "appForm",
    "appFormOptions",
    "appsLoader",
    "messagesTable",
    "messageForm",
    "messageFormOptions",
    "messagesLoader",
    "expertisesTable",
    "expertiseForm",
    "expertiseFormOptions",
    "expertisesLoader",
    "analyticsTable",
    "analyticForm",
    "analyticFormOptions",
    "analyticsLoader",
    "logsTable",
    "logForm",
    "logFormOptions",
    "logsLoader",
    "dictionaries",
    // From 72
    "url",
    "service",
    "loadedServiceForm",
  ],
  data() {
    return {
      modal: {
        modalId: "modalId",
        modalTitle: "",
        instance: null,
      },
      selectedRole: "",
      editableProfile: false,
      // teacherProfile: {
      //   basic: {
      //     lastName: this.user.fullInfo.userData.lastName,
      //     firstName: this.user.fullInfo.userData.firstName,
      //     middleName: this.user.fullInfo.userData.middleName,
      //     birthDate: this.user.fullInfo.userData.birthDate.substr(0, 10),
      //     snils: this.user.fullInfo.userData.snils,
      //     citizenship: "Российская Федерация",
      //     email: this.user.fullInfo.contacts[0].value,
      //     phone: "8-903-903-9000",
      //     gender: "Женский",
      //     region: "Новосибирская область",
      //     educationSubject: "Русский язык и литература",
      //   },
      //   address: {
      //     registrationAddress: "г.Новосибирск, ул.Советская, д.23, кв.45",
      //     livingAddress: "г.Новосибирск, ул.Ленина, д.12, кв.23",
      //   },
      //   document: {
      //     docType: "Паспорт гражданина РФ",
      //     docSeries: "5000",
      //     docNumber: "345678",
      //     docIssueDate: "2000-05-05",
      //     docFinishDate: "2050-05-04",
      //     docIssuer: "УВД Центрального района г.Новосибирска",
      //   },
      //   mailing: true,
      // },
      // expertProfile: {
      //   lastName: this.user.fullInfo.userData.lastName,
      //   firstName: this.user.fullInfo.userData.firstName,
      //   middleName: this.user.fullInfo.userData.middleName,
      //   snils: this.user.fullInfo.userData.snils,
      //   email: this.user.fullInfo.contacts[0].value,
      //   phone: "8-913-913-9999",
      //   region: "Иркутская область",
      //   workPosition: "Специалист отдела образования",
      //   organization: "Министерство образования",
      //   educationSubject: "Математика",
      //   rating: 5,
      //   activity: "Активен",
      //   mailing: false,
      // },
      teacherProfile: {
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
      expertProfile: {
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
      tableExpertises: {
        columnsList: [
          "№ заявления",
          "ФИО педагогического работника",
          "Дата начала экспертизы",
          "Срок окончания экспертизы",
          "Дата окончания экспертизы",
          "Результат экспертизы",
          "Статус",
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
        filters: [],
      },
      tableAnalytics: {
        columnsList: [
          "Количество проведенных экспертиз",
          "Количество положительных результатов",
          "Количество отрицательных результатов",
          "Количество совпадений",
        ],
        primaryColumn: "Количество проведенных экспертиз",
        rowsList: [["30", "5", "10", "15"]],
        sortColumn: "",
        ascendingSortOrder: false,
        filters: [],
      },
      tableLogs: {
        columnsList: [
          "Id записи",
          "Дата события",
          "Описание",
          "Событие",
          "Изменения",
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
        filters: [],
      },
      filterData: [
        { name: "№ сообщения", type: "input", subtype: "number", width: 6 },
        {
          name: "Наименование услуги",
          type: "select",
          optionsList: [
            { name: "Выберите...", selected: true, disabled: true },
            {
              name: "Аттестация педагогических работников",
              selected: false,
              disabled: false,
            },
            {
              name: "Субсидирование педагогических работников",
              selected: false,
              disabled: false,
            },
            {
              name: "предоставление жилья педагогическим работникам",
              selected: false,
              disabled: false,
            },
          ],
          subtype: "",
          width: 6,
        },
        { name: "Дата создания", type: "input", subtype: "date", width: 6 },
        {
          name: "Диапазон дат",
          type: "input",
          subtype: "date",
          startDate: "",
          finishDate: "",
          isInclusive: true,
          width: 6,
        },
      ],
      testItemsList: [
        {
          id: 1,
          text: "Математика",
        },
        { id: 2, text: "Литература" },
        { id: 3, text: "Русский язык" },
      ],
      testValue: "Русский язык",

      // From 72
      isFirstLoad: true,
      formCreationCompleted: false,
      validatedForm: false,
      changedForm: false,
      formInstance: null,
      currentAction: {
        info: {
          alwaysActive: false,
          backAction: false,
          confirmData: null,
          downloadPrinted: false,
          formId: null,
          hidden: false,
          id: null,
          modelId: null,
          name: "",
          notRequiredAction: false,
          onActionCode: "",
          printAction: false,
          roles: null,
          signAction: false,
          version: null,
          visibility: 0,
          weight: 0,
        },
        confirmData: {
          cancelButton: "",
          header: "",
          message: "",
          confirmButton: "",
        },
        confirmedAction: false,
      },
      cryptoProSignature: {
        dataForStamp: {
          thumbprint: null,
          subject: null,
          from: null,
          validDue: null,
        },
        dataForSign: {
          signature: null,
          hashToSign: null,
          filename: null,
        },
      },
      actionError: "",
    };
  },

  computed: {
    // From 72
    // ----------Методы для формио из formio.full.min.js----------
    backUrl() {
      return this.url + "#/account";
    },
  },

  methods: {
    editProfile() {
      this.editableProfile = true;
      this.$emit("edit-profile");
    },
    setTeacher() {
      this.editableProfile = false;
      this.$emit("set-teacher");
    },
    setExpert() {
      this.editableProfile = false;
      this.$emit("set-expert");
    },
    showAppForm(modalTitle, loaderComment, serviceId, appId) {
      if (appId) {
        this.$emit("show-app-form", { serviceId, appId, loaderComment });
      } else {
        this.$emit("show-service-first-form", { serviceId, loaderComment });
      }
      this.openModal(modalTitle);
    },

    openModal(modalTitle) {
      console.log(modalTitle);
      this.modal.modalTitle = modalTitle;
      this.modal.instance.show();
    },
    sortTable(table, column) {
      if (table.sortColumn === table.columnsList[column]) {
        table.ascendingSortOrder = !table.ascendingSortOrder;
      } else {
        table.ascendingSortOrder = true;
      }
      table.sortColumn = table.columnsList[column];
    },

    // From 72
    // ----------Методы для формио из formio.full.min.js----------
    // ----------Методы для формио из formio.full.min.js----------
    createAppForm() {
      console.log("Начало создания формы");
      console.log(document.getElementById("formio"));
      this.formInstance = window.Formio.createForm(
        document.getElementById("formio"),
        this.service.applicationDTO.form.scheme,
        { readOnly: !this.service.applicationDTO.active, language: "ru" }
      )
        .then((form) => {
          console.log("---Форма заполняется данными---");
          console.log(this.service.applicationDTO);
          form.submission = this.service.applicationDTO;
          this.formInstance = form;
          form.on("change", (submission) => {
            console.log("Сработало событие change формы formio");
            let applicationDTO = submission;
            if (!this.validatedForm) {
              console.log("первое изменение формы");
              console.log(submission);
              form.checkValidity(form.submission.data);
              this.validatedForm = true;
            } else if (this.isFirstLoad) {
              console.log("Изменение формы связанное с валидацией");
              console.log(submission);
              this.isFirstLoad = false;
              this.formCreationCompleted = true;
            } else {
              console.log("Настоящее изменение формы");
              console.log(submission);
              this.$emit("change-app-form", applicationDTO);
              this.changedForm = true;
            }
          });
        })
        .then(() => {
          window.formInstance = this.formInstance;
          this.formCreationCompleted = true;
          console.log("---Создание и наполнение формы formio завершено---");
        });
    },
    async invokeAction(action) {
      console.groupCollapsed("Выполнение действия " + action.id);
      console.log(action);
      console.groupEnd();
      this.currentAction.info = action;
      window.currentAction = action;
      if (action.confirmData && !this.currentAction.confirmedAction) {
        this.currentAction.confirmData = action.confirmData;
        this.showModalBS("confirm-action-modal");
      } else {
        this.currentAction.confirmedAction = false;
        if (!action.version) {
          console.log("Действие 1 версии");
          if (action.signAction) {
            try {
              this.requestStartDefaultHandler();
              await this.successDefaultHandler();
              this.camundaAsyncFunctionInvoker(
                this.currentAction.info,
                this.formInstance
              );
            } catch (error) {
              await this.errorDefaultHandler(error);
            }
            return;
          }
          if (!action.notRequiredAction) {
            // console.log("Действие требует проверки");
            if (
              this.formInstance.checkValidity(this.formInstance.submission.data)
            ) {
              this.requestStartDefaultHandler();
              let response = await this.makeRequest(true);
              if (action.printAction) {
                const printResponse = response.data.responseObject;
                console.log("---handlePrintActionResponse");
                console.log(printResponse);
                this.handlePrintActionResponse(printResponse);
              }
              await this.successDefaultHandler(response);
              if (action.backAction) {
                window.location.href = this.backUrl;
              }
            } else {
              this.formInstance.submit();
            }
          } else {
            this.requestStartDefaultHandler();
            let response = await this.makeRequest(true);
            if (action.printAction) {
              const printResponse = response.data.responseObject;
              console.log("---handlePrintActionResponse");
              console.log(printResponse);
              this.handlePrintActionResponse(printResponse);
            }
            await this.successDefaultHandler(response);
            if (action.backAction) {
              window.location.href = this.backUrl;
            }
          }
        } else if (action.version === 2) {
          console.log("Действие 2 версии");
          console.log(action.onActionCode);
          window.invokeActionCode(action.onActionCode);
          /*let actionV2 = function () {
          console.log("Запуск actionV2");
          action.onActionCode;
        };
        actionV2();*/
          // await window.eval(action.onActionCode);
          // await this.runOnCodeAction(action.onActionCode);

          /*var AsyncFunction = Object.getPrototypeOf(
          async function () {}
        ).constructor;
        var fn = AsyncFunction(action.onActionCode);
        console.log(fn);
        fn();*/
        }
      }
    },
    confirmAction() {
      this.currentAction.confirmedAction = true;
      this.invokeAction(this.currentAction.info);
      this.hideModalBS("confirm-action-modal");
    },

    // Методы действий v2
    async errorDefaultHandler(error) {
      if (error?.response?.data?.errors) {
        this.actionError = error.response.data.errors.join(". ");
      } else {
        this.actionError = "";
      }
      if (error.response) {
        this.successDefaultHandler(error.response);
      }
    },
    requestStartDefaultHandler() {
      console.log("Начало выполнения действия");
      this.$emit("show-app-loader", "Выполнение действия по обращению");
    },
    async makeRequest(v1 = false) {
      console.log("Выполнение запроса");
      const request = {
        actionId: this.currentAction.info.id,
        userId: 0,
        roleId: 0,
        orgId: 0,
        appId: +this.$route.params.appId,
        data: JSON.stringify(this.service.applicationDTO.data),
      };
      return await axios
        .post(this.url + "api/app/action-invoke", request, {
          headers: { "Content-Type": "application/json" },
          withCredentials: true,
        })
        .then(async (response) => {
          this.actionError = "";
          if (response.data.responseObject) {
            let responseObject = JSON.parse(response.data.responseObject);
            if (v1) {
              console.log("Распарсить объект v1");
              if (responseObject) {
                response.data.payload_response = responseObject;
                console.log(response);
              }
              response.data.responseObject = responseObject;
            } else {
              if (responseObject.payload_response) {
                response.data.payload_response =
                  responseObject.payload_response;
                console.log(response);
              }
            }
          }
          await this.$emit("update-registries");
          return response;
        });
    },
    async successDefaultHandler(response) {
      console.log("Окончание выполнения действия");
      console.log(response);
      if (this.currentAction.info.backAction) {
        window.location.href = this.backUrl;
      } else if (response) {
        console.groupCollapsed("Ответ на запрос выполнения действия");
        console.log(response);
        console.groupEnd();
        if (response.data.applicationDTO) {
          this.setFormResponse(response);
        }
      }
      this.$emit("hide-app-loader");
    },
    async setFormResponse(response) {
      let nextForm = response.data.applicationDTO;
      nextForm.data = JSON.parse(nextForm.data);
      nextForm.form.scheme = JSON.parse(nextForm.form.scheme);
      this.$emit("set-application-stamp", nextForm);
      await this.$emit("change-app-form", nextForm);
      this.createAppForm();
    },
    discardSavedFormioFiles() {
      console.log("Сброс значений формы к ранее полученным");
      this.$emit("reset-app-form");
    },
    handlePrintActionResponse(printResponse) {
      console.log("----------");
      console.log(printResponse);
      if (
        ((printResponse.fileName && printResponse.fileName.endsWith(".pdf")) ||
          (printResponse.name && printResponse.name.endsWith(".pdf"))) &&
        !this.currentAction.info.downloadPrinted
      ) {
        const file = this.base64toFile(
          printResponse.fileData,
          "application/pdf",
          printResponse.fileName
        );
        const fileUrl = URL.createObjectURL(file);
        window.open(fileUrl, "_blank").focus();
      } else {
        let link = document.createElement("a");
        if (printResponse.fileName) {
          link.setAttribute("download", printResponse.fileName);
        }
        if (printResponse.name) {
          link.setAttribute("download", printResponse.name);
        }
        link.setAttribute(
          "href",
          "data:application/octet-stream;base64," + printResponse.fileData
        );
        link.click();
      }
    },
    base64toFile(b64Data, contentType = "", fileName, sliceSize = 512) {
      const byteCharacters = atob(b64Data);
      const byteArrays = [];

      for (
        let offset = 0;
        offset < byteCharacters.length;
        offset += sliceSize
      ) {
        const slice = byteCharacters.slice(offset, offset + sliceSize);

        const byteNumbers = new Array(slice.length);
        for (let i = 0; i < slice.length; i++) {
          byteNumbers[i] = slice.charCodeAt(i);
        }
        const byteArray = new Uint8Array(byteNumbers);
        byteArrays.push(byteArray);
      }
      let file = new File(byteArrays, fileName, { type: contentType });

      return file;
    },
    camundaAsyncFunctionInvoker(currentAction, formInstance) {
      console.log("camundaAsyncFunctionInvoker");
      console.log(currentAction);
      console.log(formInstance);
      this.signActionId = currentAction.id;
      this.showModalBS("cryptoProSignatureModal");
    },
    // Методы печати с подписью
    async getHashToSign() {
      this.hideModalBS("cryptoProSignatureModal");
      this.$emit("show-app-loader", "Выполнение действия по обращению");
      const request = {
        certificate: {
          thumbprint: this.cryptoProSignature.dataForStamp.thumbprint,
          subject: this.cryptoProSignature.dataForStamp.subject,
          from: this.cryptoProSignature.dataForStamp.from,
          validDue: this.cryptoProSignature.dataForStamp.validDue,
        },
        actionPayloadDTO: {
          actionId: this.currentAction.info.id,
          userId: 0,
          roleId: 0,
          orgId: 0,
          appId: this.service.applicationDTO.id,
          data: JSON.stringify(this.service.applicationDTO.data),
        },
      };
      console.log(request);
      axios
        .post(this.url + "api/app/action-pdfstamp", request, {
          headers: { "Content-Type": "application/json" },
          withCredentials: true,
        })
        .then((response) => {
          console.log("Ответ на экшн");
          console.log(response);
          console.log("Содержимое data.applicationDTO.data");
          console.log(JSON.parse(response.data.applicationDTO.data));
          console.log("Содержимое data.responseObject");
          console.log(JSON.parse(response.data.responseObject));
          if (!this.currentAction.info.version) {
            // Действие v1
            this.cryptoProSignature.dataForSign.hashToSign = JSON.parse(
              response.data.responseObject
            ).hashToSign;
            this.cryptoProSignature.dataForSign.filename = JSON.parse(
              response.data.responseObject
            ).fileName;
            // Установка хеша для подписи в скрытое поле формы
            document.getElementById("DataToSignTxtBox").innerHTML = JSON.parse(
              response.data.responseObject
            ).hashToSign;
          } else {
            // Действие v2 и выше
            this.cryptoProSignature.dataForSign.hashToSign = JSON.parse(
              response.data.responseObject
            ).payload_response.hashToSign;
            this.cryptoProSignature.dataForSign.filename = JSON.parse(
              response.data.responseObject
            ).payload_response.fileName;
            // Установка хеша для подписи в скрытое поле формы
            document.getElementById("DataToSignTxtBox").innerHTML = JSON.parse(
              response.data.responseObject
            ).payload_response.hashToSign;
          }
          window.Common_SignCadesBES("CertListBox");
          let hashField = document.getElementById("DataToSignTxtBox");
          console.log(hashField);
        })
        .catch((error) => this.errorDefaultHandler(error));
    },
    async signCryptoPro() {
      console.log(window.dataToSign);
      const request = {
        applicationId: this.service.applicationDTO.id,
        signature: this.cryptoProSignature.dataForSign.signature,
        hashToSign: this.cryptoProSignature.dataForSign.hashToSign,
        filename: this.cryptoProSignature.dataForSign.filename,
        actionId: this.currentAction.info.id,
      };
      console.log("Данные отправляемые для подписанного файла");
      console.log(request);
      await axios
        .post(this.url + "api/app/action-insert-signdata", request, {
          headers: { "Content-Type": "application/json" },
          withCredentials: true,
        })
        .then(async (response) => {
          console.log(response);
          if (response.data.fileData) {
            console.log(response.data);
            console.log("JSON подписанного файла");
            console.log(JSON.stringify(response.data));
            this.handlePrintActionResponse(response.data);
            // Обнуление информации о сертификате и сведений о подписи
            document.getElementById("cert_info").style.display = "none";
            document.getElementById("DataToSignTxtBox").innerHTML = "";
            document.getElementById("SignatureTxtBox").innerHTML = "";
            await this.$emit("show-app-form", {
              serviceId: this.$route.params.modelId,
              appId: this.$route.params.appId,
            });
            this.cryptoProSignature = {
              dataForStamp: {
                thumbprint: null,
                subject: null,
                from: null,
                validDue: null,
              },
              dataForSign: {
                signature: null,
                hashToSign: null,
                filename: null,
              },
            };
            this.$emit("hide-app-loader");
          }
        });
    },

    // Модальные окна Bootstrap
    showModalBS(modalId) {
      $(`#${modalId}`).modal({
        backdrop: true,
      });
      $(`#${modalId}`).modal("show");
    },
    hideModalBS(modalId) {
      $(`#${modalId}`).modal("hide");
    },
  },

  created() {
    console.log("Создан компонент AccountView");

    this.teacherProfile = this.teacherInfo;
    this.expertProfile = this.expertInfo;

    // From 72
    window.HAS_FORM = true;
    window.errorDefaultHandler = this.errorDefaultHandler;
    window.requestStartDefaultHandler = this.requestStartDefaultHandler;
    window.makeRequest = this.makeRequest;
    window.successDefaultHandler = this.successDefaultHandler;
    window.handlePrintActionResponse = this.handlePrintActionResponse;
    window.camundaAsyncFunctionInvoker = this.camundaAsyncFunctionInvoker;
    window.invokeAction = this.invokeAction;
    window.backUrl = this.backUrl;

    // Фейковые заглушки
    window.formSubmission = {};
    window.applicationAdapter = { variables: {} };
    window.discardSavedFormioFiles = this.discardSavedFormioFiles;
  },

  mounted() {
    console.log("Смонтирован компонент AccountView");

    this.modal.instance = new Modal(
      document.getElementById(this.modal.modalId)
    );

    // From 72
    // Загрузка файлов КриптоПро при открытии модального окна
    $("#cryptoProSignatureModal").on("shown.bs.modal", function () {
      var file6 = document.createElement("script");
      file6.setAttribute("type", "text/javascript");
      file6.setAttribute("src", "cryptopro/load_extension.js");
      document.getElementsByTagName("body")[0].appendChild(file6);
      var file7 = document.createElement("script");
      file7.setAttribute("type", "text/javascript");
      file7.setAttribute("src", "cryptopro/highlight.js");
      document.getElementsByTagName("body")[0].appendChild(file7);
    });
    // Установка связи данных для печати с подписью с глобальной переменной
    window.cryptoProSignature = this.cryptoProSignature;
  },

  destroyed() {
    // From 72
    /*this.$emit("disable-keep-alive");*/
  },

  watch: {
    teacherInfo: function () {
      if (this.isAuthUser) {
        this.teacherProfile = this.teacherInfo;
      }
    },
    expertInfo: function () {
      if (this.isAuthUser) {
        this.expertProfile = this.expertInfo;
      }
    },

    // From 72
    loadedServiceForm: function () {
      console.log("Изменен маркер загрузки формы");
      this.createAppForm();
      // this.hideLoader();
    },
    "cryptoProSignature.dataForSign": {
      handler: function () {
        if (
          this.cryptoProSignature.dataForSign.signature &&
          this.cryptoProSignature.dataForSign.hashToSign &&
          this.cryptoProSignature.dataForSign.filename
        ) {
          this.signCryptoPro();
        }
      },
      deep: true,
    },
  },
};
</script>

<style lang="scss" scoped>
.from-closed-server {
  color: green;
}
</style>
