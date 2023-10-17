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
              @click="createNewApp('Новое заявление')"
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
              @row-click="openExistingApp('Детали заявления', $event)"
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
              @click="createNewMessage('Новое сообщение')"
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
              @row-click="openExistingMessage('Детали сообщения', $event)"
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
              @row-click="openExistingExpertise('Детали экспертизы', $event)"
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
            v-if="appsLoader.isLoading && !appsLoader.isResponse"
            :comment="appsLoader.comment"
            :theme="appsLoader.theme"
          />
          <div v-show="!appsLoader.isLoading && appsLoader.isResponse">
            <!--          Форма нового заявления-->
            <form
              v-if="modal.modalTitle === 'Новое заявление'"
              class="application-form"
            >
              <!--              <div v-if="appForm.form.id" class="row">-->
              <div class="row">
                <div class="col-10">
                  <!--<Form
                    :form="appForm.form.scheme"
                    :submission="appForm"
                    language="ru"
                    :options="{
                      i18n: appFormOptions,
                      readOnly: !appForm.active,
                    }"
                    ref="vueForm"
                  />-->
                  <!--suppress XmlDuplicatedId -->
                  <div id="formio" ref="vueForm"></div>
                </div>

                <div
                  v-if="appForm.form.actions && appForm.form.actions.length > 0"
                  class="col-2 action-buttons"
                >
                  <template v-for="action of appForm.form.actions">
                    <template v-if="appForm.active || action.alwaysActive">
                      <button
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
                      </button>
                    </template>
                  </template>
                </div>
              </div>
            </form>
            <!--          Заполненная форма заявления-->
            <form
              v-if="modal.modalTitle === 'Детали заявления'"
              class="application-form"
            >
              <div v-if="appForm.form.id" class="row">
                <div class="col-10">
                  <!--<Form
                    :form="appForm.form.scheme"
                    :submission="appForm"
                    language="ru"
                    :options="{
                      i18n: appFormOptions,
                      readOnly: !appForm.active,
                    }"
                    ref="vueForm"
                  />-->
                  <!--suppress XmlDuplicatedId -->
                  <div id="formio" ref="vueForm"></div>
                </div>
                <div
                  v-if="appForm.form.actions && appForm.form.actions.length > 0"
                  class="col-2 action-buttons"
                >
                  <template v-for="action of appForm.form.actions">
                    <template v-if="appForm.active || action.alwaysActive">
                      <button
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
                      </button>
                    </template>
                  </template>
                </div>
              </div>
            </form>
          </div>

          <LoaderBootstrapCustomBS46
            v-if="messagesLoader.isLoading && !messagesLoader.isResponse"
            :comment="messagesLoader.comment"
            :theme="messagesLoader.theme"
          />
          <template v-else>
            <!--          Форма нового сообщения-->
            <form
              v-if="modal.modalTitle === 'Новое сообщение'"
              class="application-form"
            >
              <div v-if="messageForm.form.id" class="row">
                <div class="col-10">
                  <!--<Form
                    :form="messageForm.form.scheme"
                    :submission="messageForm"
                    language="ru"
                    :options="{
                      i18n: messageFormOptions,
                      readOnly: !messageForm.active,
                    }"
                    ref="vueForm"
                  />-->
                  <!--suppress XmlDuplicatedId -->
                  <div id="formio" ref="vueForm"></div>
                </div>
                <div
                  v-if="
                    messageForm.form.actions &&
                    messageForm.form.actions.length > 0
                  "
                  class="col-2 action-buttons"
                >
                  <template v-for="action of messageForm.form.actions">
                    <template v-if="messageForm.active || action.alwaysActive">
                      <button
                        v-if="action.backAction"
                        :key="action.id"
                        type="button"
                        class="btn btn-block btn-primary"
                        @click="
                          $emit('invoke-action', {
                            actionId: action.id,
                            backAction: true,
                            modelId: messageForm.modelId,
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
                            modelId: messageForm.modelId,
                          })
                        "
                      >
                        {{ action.name }}
                      </button>
                    </template>
                  </template>
                </div>
              </div>
            </form>
            <!--          Заполненная форма сообщения-->
            <form
              v-if="modal.modalTitle === 'Детали сообщения'"
              class="application-form"
            >
              <div v-if="messageForm.form.id" class="row">
                <div class="col-10">
                  <!--<Form
                    :form="messageForm.form.scheme"
                    :submission="messageForm"
                    language="ru"
                    :options="{
                      i18n: messageFormOptions,
                      readOnly: !messageForm.active,
                    }"
                    ref="vueForm"
                  />-->
                  <!--suppress XmlDuplicatedId -->
                  <div id="formio" ref="vueForm"></div>
                </div>
                <div
                  v-if="
                    messageForm.form.actions &&
                    messageForm.form.actions.length > 0
                  "
                  class="col-2 action-buttons"
                >
                  <template v-for="action of messageForm.form.actions">
                    <template v-if="messageForm.active || action.alwaysActive">
                      <button
                        v-if="action.backAction"
                        :key="action.id"
                        type="button"
                        class="btn btn-block btn-primary"
                        @click="
                          $emit('invoke-action', {
                            actionId: action.id,
                            backAction: true,
                            modelId: messageForm.modelId,
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
                            modelId: messageForm.modelId,
                          })
                        "
                      >
                        {{ action.name }}
                      </button>
                    </template>
                  </template>
                </div>
              </div>
            </form>
          </template>

          <LoaderBootstrapCustomBS46
            v-if="expertisesLoader.isLoading && !expertisesLoader.isResponse"
            :comment="expertisesLoader.comment"
            :theme="expertisesLoader.theme"
          />
          <template v-else>
            <!--          Заполненная форма экспертизы-->
            <form
              v-if="modal.modalTitle === 'Детали экспертизы'"
              class="application-form"
            >
              <div v-if="expertiseForm.form.id" class="row">
                <div class="col-10">
                  <!--<Form
                    :form="expertiseForm.form.scheme"
                    :submission="expertiseForm"
                    language="ru"
                    :options="{
                      i18n: expertiseFormOptions,
                      readOnly: !expertiseForm.active,
                    }"
                    ref="vueForm"
                  />-->
                  <!--suppress XmlDuplicatedId -->
                  <div id="formio" ref="vueForm"></div>
                </div>
                <div
                  v-if="
                    expertiseForm.form.actions &&
                    expertiseForm.form.actions.length > 0
                  "
                  class="col-2 action-buttons"
                >
                  <template v-for="action of expertiseForm.form.actions">
                    <template
                      v-if="expertiseForm.active || action.alwaysActive"
                    >
                      <button
                        v-if="action.backAction"
                        :key="action.id"
                        type="button"
                        class="btn btn-block btn-primary"
                        @click="
                          $emit('invoke-action', {
                            actionId: action.id,
                            backAction: true,
                            modelId: expertiseForm.modelId,
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
                            modelId: expertiseForm.modelId,
                          })
                        "
                      >
                        {{ action.name }}
                      </button>
                    </template>
                  </template>
                </div>
              </div>
            </form>
          </template>

          <LoaderBootstrapCustomBS46
            v-if="logsLoader.isLoading && !logsLoader.isResponse"
            :comment="logsLoader.comment"
            :theme="logsLoader.theme"
          />
          <template v-else>
            <!--          Заполненная форма действия-->
            <form v-if="modal.modalTitle === 'Детали действия'">
              <div class="row">
                <div class="col-6">
                  <label for="logs-form-view-number" class="form-label"
                    >Id записи</label
                  >
                  <input
                    type="text"
                    class="form-control"
                    id="logs-form-view-number"
                    disabled
                    value="0001"
                  />
                </div>
                <div class="col-6">
                  <label for="logs-form-view-date" class="form-label"
                    >Дата события</label
                  >
                  <input
                    type="date"
                    class="form-control"
                    id="logs-form-view-date"
                    disabled
                    value="2022-08-01"
                  />
                </div>
                <div class="col-6">
                  <label for="logs-form-view-description" class="form-label"
                    >Описание</label
                  >
                  <input
                    type="text"
                    class="form-control"
                    id="logs-form-view-description"
                    disabled
                    value="Изменения ошибочной записи"
                  />
                </div>
                <div class="col-6">
                  <label for="logs-form-view-event" class="form-label"
                    >Событие</label
                  >
                  <input
                    type="text"
                    class="form-control"
                    id="logs-form-view-event"
                    disabled
                    value="Изменение записи"
                  />
                </div>
                <div class="col-6">
                  <label for="logs-form-view-changes" class="form-label"
                    >Изменения</label
                  >
                  <input
                    type="text"
                    class="form-control"
                    id="logs-form-view-changes"
                    disabled
                    value="Изменение даты рождения"
                  />
                </div>
              </div>
            </form>
          </template>
        </template>
      </ModalBootstrapCustom46>
    </div>
  </div>
</template>

<script>
// import $ from "jquery";
import { Modal } from "bootstrap";
import ModalBootstrapCustom46 from "../components/universal/ModalBootstrapCustomBS46";
import TableBootstrapCustomBS46 from "../components/universal/TableBootstrapCustomBS46";
// import { Form } from "vue-formio";
import LoaderBootstrapCustomBS46 from "../components/universal/LoaderBootstrapCustomBS46";
import InputBootstrapCustomBS46 from "../components/universal/Forms/InputBootstrapCustomBS46";
import CheckboxBootstrapCustomBS46 from "../components/universal/Forms/CheckboxBootstrapCustomBS46";
import SelectBootstrapCustomBS46 from "../components/universal/Forms/SelectBootstrapCustomBS46";

export default {
  name: "AccountView",
  components: {
    SelectBootstrapCustomBS46,
    CheckboxBootstrapCustomBS46,
    InputBootstrapCustomBS46,
    LoaderBootstrapCustomBS46,
    TableBootstrapCustomBS46,
    ModalBootstrapCustom46,
    // Form,
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
      loader: {
        isLoading: false,
        comment: "Загружается обращение",
        startTimeStamp: null,
      },
      actionError: "",
    };
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
    openExistingApp(modalTitle, appId) {
      this.$emit("open-existing-app", appId);
      this.openModal(modalTitle);
    },
    createNewApp(modalTitle) {
      // this.$emit("create-new-app");
      this.$emit("show-service-first-form", 2);
      this.openModal(modalTitle);
    },
    openExistingMessage(modalTitle, appId) {
      this.$emit("open-existing-message", appId);
      this.openModal(modalTitle);
    },
    createNewMessage(modalTitle) {
      this.$emit("create-new-message");
      this.openModal(modalTitle);
    },
    openExistingExpertise(modalTitle, appId) {
      this.$emit("open-existing-expertise", appId);
      this.openModal(modalTitle);
    },
    openExistingLog(modalTitle, appId) {
      this.$emit("open-existing-log", appId);
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
  },

  created() {
    console.log("Создан компонент AccountView");

    this.teacherProfile = this.teacherInfo;
    this.expertProfile = this.expertInfo;

    // From 72
    /*window.HAS_FORM = true;
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

    if (this.user.auth) {
      if (!+this.$route.params.appId) {
        if (!this.service.info.id) {
          this.loader.comment = "Загрузка формы обращения";
          console.log("Запрос загрузки первой формы из AppView");
          this.$emit("show-service-first-form", this.$route.params.modelId);
        }
      } else {
        this.loader.comment = "Загрузка обращения";
        this.$emit("show-app-form", {
          serviceId: this.$route.params.modelId,
          appId: this.$route.params.appId,
        });
      }
      this.showLoader();
    } else {
      this.$router.push("/");
    }*/
  },

  mounted() {
    console.log("Смонтирован компонент AccountView");

    this.modal.instance = new Modal(
      document.getElementById(this.modal.modalId)
    );

    // From 72
    // Загрузка файлов КриптоПро при открытии модального окна
    /*$("#cryptoProSignatureModal").on("shown.bs.modal", function () {
      var file6 = document.createElement("script");
      file6.setAttribute("type", "text/javascript");
      file6.setAttribute("src", "cryptopro/load_extension.js");
      document.getElementsByTagName("body")[0].appendChild(file6);
      var file7 = document.createElement("script");
      file7.setAttribute("type", "text/javascript");
      file7.setAttribute("src", "cryptopro/highlight.js");
      document.getElementsByTagName("body")[0].appendChild(file7);
    });*/
    // Установка связи данных для печати с подписью с глобальной переменной
    /*window.cryptoProSignature = this.cryptoProSignature;*/
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
    /*"cryptoProSignature.dataForSign": {
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
    },*/
  },
};
</script>

<style lang="scss" scoped>
.from-closed-server {
  color: green;
}
</style>
