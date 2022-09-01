<template>
  <div id="account">
    <div class="container">
      <!--      Вкладки педагога-->
      <template v-if="user.shortInfo.roleId === 39707">
        <ul class="nav nav-tabs" id="teacher-tab" role="tablist">
          <li class="nav-item" role="presentation">
            <button
              class="nav-link active"
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
          <li class="nav-item" role="presentation">
            <button
              class="nav-link"
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
        </ul>
        <div class="tab-content" id="teacherTabContent">
          <div
            class="tab-pane fade show active pt-3"
            id="profile-teacher-tab-pane"
            role="tabpanel"
            aria-labelledby="profile-teacher-tab"
            tabindex="0"
          >
            <LoaderBootstrapCustomBS46
              v-if="profileLoader.isLoading && !profileLoader.isResponse"
              :comment="profileLoader.comment"
              :theme="profileLoader.theme"
            />
            <template v-else>
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
                        <div class="col-6 form-group">
                          <label
                            for="lastName"
                            class="form-label from-closed-server"
                            >Фамилия</label
                          >
                          <input
                            type="text"
                            class="form-control"
                            id="lastName"
                            disabled
                            v-model="teacherProfile.basic.lastName"
                          />
                        </div>
                        <div class="col-6 form-group">
                          <label
                            for="firstName"
                            class="form-label from-closed-server"
                            >Имя</label
                          >
                          <input
                            type="text"
                            class="form-control"
                            id="firstName"
                            disabled
                            v-model="teacherProfile.basic.firstName"
                          />
                        </div>
                        <div class="col-6 form-group">
                          <label
                            for="middleName"
                            class="form-label from-closed-server"
                            >Отчество</label
                          >
                          <input
                            type="text"
                            class="form-control"
                            id="middleName"
                            disabled
                            v-model="teacherProfile.basic.middleName"
                          />
                        </div>
                        <div class="col-6 form-group">
                          <label
                            for="birthDate"
                            class="form-label from-closed-server"
                            >Дата рождения</label
                          >
                          <input
                            type="date"
                            class="form-control"
                            id="birthDate"
                            disabled
                            v-model="teacherProfile.basic.birthDate"
                          />
                        </div>
                        <div class="col-6 form-group">
                          <label
                            for="snils"
                            class="form-label from-closed-server"
                            >СНИЛС</label
                          >
                          <input
                            type="text"
                            class="form-control"
                            id="snils"
                            disabled
                            v-model="teacherProfile.basic.snils"
                          />
                        </div>
                        <div class="col-6 form-group">
                          <label for="citizenship" class="form-label"
                            >Гражданство</label
                          >
                          <input
                            type="text"
                            class="form-control"
                            id="citizenship"
                            disabled
                            v-model="teacherProfile.basic.citizenship"
                          />
                        </div>
                        <div class="col-6 form-group">
                          <label
                            for="email"
                            class="form-label from-closed-server"
                            >Электронная почта</label
                          >
                          <input
                            type="email"
                            class="form-control"
                            id="email"
                            :disabled="!editableProfile"
                            v-model="teacherProfile.basic.email"
                          />
                        </div>
                        <div class="col-6 form-group">
                          <label for="phone" class="form-label">Телефон</label>
                          <input
                            type="tel"
                            class="form-control"
                            id="phone"
                            :disabled="!editableProfile"
                            v-model="teacherProfile.basic.phone"
                          />
                        </div>
                        <div class="col-6 form-group">
                          <label for="gender" class="form-label">Пол</label>
                          <select
                            id="gender"
                            class="form-control"
                            disabled
                            v-model="teacherProfile.basic.gender"
                          >
                            <option selected>Выберите...</option>
                            <option>Мужской</option>
                            <option>Женский</option>
                          </select>
                        </div>
                        <div class="col-6 form-group">
                          <label for="region" class="form-label"
                            >Муниципальное образование</label
                          >
                          <select
                            id="region"
                            class="form-control"
                            :disabled="!editableProfile"
                            v-model="teacherProfile.basic.region"
                          >
                            <option selected>Выберите...</option>
                            <option>Новосибирская область</option>
                            <option>Республика Алтай</option>
                            <option>Алтайский край</option>
                          </select>
                        </div>
                        <div class="col-12 form-group">
                          <label for="educationSubject" class="form-label"
                            >Предметная область</label
                          >
                          <select
                            id="educationSubject"
                            class="form-control"
                            :disabled="!editableProfile"
                            v-model="teacherProfile.basic.educationSubject"
                          >
                            <option selected>Выберите...</option>
                            <option>Русский язык и литература</option>
                            <option>Математика</option>
                            <option>Химия и биология</option>
                          </select>
                        </div>
                      </form>
                    </div>
                  </div>
                </div>
                <div class="card">
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
                        <div class="col-12 form-group">
                          <label for="registrationAddress" class="form-label"
                            >Адрес регистрации</label
                          >
                          <input
                            type="text"
                            class="form-control"
                            id="registrationAddress"
                            :disabled="!editableProfile"
                            v-model="teacherProfile.address.registrationAddress"
                          />
                        </div>
                        <div class="col-12 form-group">
                          <label for="livingAddress" class="form-label"
                            >Адрес места жительства</label
                          >
                          <input
                            type="text"
                            class="form-control"
                            id="livingAddress"
                            :disabled="!editableProfile"
                            v-model="teacherProfile.address.livingAddress"
                          />
                        </div>
                      </form>
                    </div>
                  </div>
                </div>
                <div class="card">
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
                        <div class="col-12 form-group">
                          <label for="docType" class="form-label"
                            >Наименование документа</label
                          >
                          <select
                            id="docType"
                            class="form-control"
                            :disabled="!editableProfile"
                            v-model="teacherProfile.document.docType"
                          >
                            <option selected>Выберите...</option>
                            <option>Паспорт гражданина РФ</option>
                            <option>Паспорт иностранного гражданина</option>
                          </select>
                        </div>
                        <div class="col-6 form-group">
                          <label for="docSeries" class="form-label"
                            >Серия</label
                          >
                          <input
                            type="email"
                            class="form-control"
                            id="docSeries"
                            :disabled="!editableProfile"
                            v-model="teacherProfile.document.docSeries"
                          />
                        </div>
                        <div class="col-6 form-group">
                          <label for="docNumber" class="form-label"
                            >Номер</label
                          >
                          <input
                            type="text"
                            class="form-control"
                            id="docNumber"
                            :disabled="!editableProfile"
                            v-model="teacherProfile.document.docNumber"
                          />
                        </div>
                        <div class="col-6 form-group">
                          <label for="docIssueDate" class="form-label"
                            >Дата выдачи</label
                          >
                          <input
                            type="date"
                            class="form-control"
                            id="docIssueDate"
                            :disabled="!editableProfile"
                            v-model="teacherProfile.document.docIssueDate"
                          />
                        </div>
                        <div class="col-6 form-group">
                          <label for="docFinishDate" class="form-label"
                            >Срок действия</label
                          >
                          <input
                            type="date"
                            class="form-control"
                            id="docFinishDate"
                            :disabled="!editableProfile"
                            v-model="teacherProfile.document.docFinishDate"
                          />
                        </div>
                        <div class="col-12 form-group">
                          <label for="docIssuer" class="form-label"
                            >Кем выдан</label
                          >
                          <input
                            type="text"
                            class="form-control"
                            id="docIssuer"
                            :disabled="!editableProfile"
                            v-model="teacherProfile.document.docIssuer"
                          />
                        </div>
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
                        <div class="col-12 form-group">
                          <div class="form-check">
                            <input
                              class="form-check-input"
                              type="checkbox"
                              id="mailing"
                              :disabled="!editableProfile"
                              v-model="teacherProfile.mailing"
                            />
                            <label class="form-check-label" for="mailing">
                              Я согласен(а) на получение уведомлений на
                              электронную почту
                            </label>
                          </div>
                        </div>
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
              <button v-else class="btn btn-primary mt-3" @click="saveProfile">
                Сохранить профиль
              </button>
            </template>
          </div>
          <div
            class="tab-pane fade pt-3"
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
            <TableBootstrapCustom
              hover
              bordered
              filter
              pagination
              :table-data="appsTable"
              @row-click="openExistingApp('Детали заявления', $event)"
              @sort-table="sortTable(appsTable, $event)"
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
            <TableBootstrapCustom
              pagination
              filter
              hover
              bordered
              :table-data="messagesTable"
              :items-total="messagesTable.pagination.itemsTotal"
              :page="messagesTable.pagination.page"
              :page-size="messagesTable.pagination.pageSize"
              :items-per-page="messagesTable.pagination.itemsPerPage"
              @row-click="openExistingMessage('Детали сообщения', $event)"
              @sort-table="sortTable(messagesTable, $event)"
              @change-page-size="$emit('change-message-page-size', $event)"
              @change-page="$emit('change-message-page', $event)"
            />
          </div>
        </div>
      </template>

      <!--      Вкладки эксперта-->
      <template v-if="user.shortInfo.roleId === 39927">
        <ul class="nav nav-tabs" id="expert-tab" role="tablist">
          <li class="nav-item" role="presentation">
            <button
              class="nav-link active"
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
        <div class="tab-content" id="expertTabContent">
          <div
            class="tab-pane fade show active pt-3"
            id="profile-expert-tab-pane"
            role="tabpanel"
            aria-labelledby="profile-expert-tab"
            tabindex="0"
          >
            <form class="row g-3">
              <div class="col-6 form-group">
                <label for="ex-lastName" class="form-label from-closed-server"
                  >Фамилия</label
                >
                <input
                  type="text"
                  class="form-control"
                  id="ex-lastName"
                  disabled
                  v-model="expertProfile.lastName"
                />
              </div>
              <div class="col-6 form-group">
                <label for="ex-firstName" class="form-label from-closed-server"
                  >Имя</label
                >
                <input
                  type="text"
                  class="form-control"
                  id="ex-firstName"
                  disabled
                  v-model="expertProfile.firstName"
                />
              </div>
              <div class="col-6 form-group">
                <label for="ex-middleName" class="form-label from-closed-server"
                  >Отчество</label
                >
                <input
                  type="text"
                  class="form-control"
                  id="ex-middleName"
                  disabled
                  v-model="expertProfile.middleName"
                />
              </div>
              <div class="col-6 form-group">
                <label for="ex-snils" class="form-label from-closed-server"
                  >СНИЛС</label
                >
                <input
                  type="text"
                  class="form-control"
                  id="ex-snils"
                  disabled
                  v-model="expertProfile.snils"
                />
              </div>
              <div class="col-6 form-group">
                <label for="ex-email" class="form-label from-closed-server"
                  >Электронная почта</label
                >
                <input
                  type="email"
                  class="form-control"
                  id="ex-email"
                  :disabled="!editableProfile"
                  v-model="expertProfile.email"
                />
              </div>
              <div class="col-6 form-group">
                <label for="ex-phone" class="form-label">Телефон</label>
                <input
                  type="tel"
                  class="form-control"
                  id="ex-phone"
                  :disabled="!editableProfile"
                  v-model="expertProfile.phone"
                />
              </div>
              <div class="col-6 form-group">
                <label for="ex-region" class="form-label"
                  >Муниципальное образование</label
                >
                <select
                  id="ex-region"
                  class="form-control"
                  :disabled="!editableProfile"
                  v-model="expertProfile.region"
                >
                  <option selected disabled>Выберите...</option>
                  <option>Иркутская область</option>
                  <option>Республика Бурятия</option>
                  <option>Забайкальский край</option>
                </select>
              </div>
              <div class="col-6 form-group">
                <label for="ex-workPosition" class="form-label"
                  >Должность</label
                >
                <select
                  id="ex-workPosition"
                  class="form-control"
                  :disabled="!editableProfile"
                  v-model="expertProfile.workPosition"
                >
                  <option selected disabled>Выберите...</option>
                  <option>Специалист отдела образования</option>
                  <option>Начальник отдела</option>
                  <option>Министр</option>
                </select>
              </div>
              <div class="col-6 form-group">
                <label for="ex-organization" class="form-label"
                  >Организация</label
                >
                <select
                  id="ex-organization"
                  class="form-control"
                  :disabled="!editableProfile"
                  v-model="expertProfile.organization"
                >
                  <option selected disabled>Выберите...</option>
                  <option>Министерство образования</option>
                  <option>Отдел образования Администрации</option>
                </select>
              </div>
              <div class="col-6 form-group">
                <label for="ex-educationSubject" class="form-label"
                  >Предметная область</label
                >
                <select
                  id="ex-educationSubject"
                  class="form-control"
                  :disabled="!editableProfile"
                  v-model="expertProfile.educationSubject"
                >
                  <option selected disabled>Выберите...</option>
                  <option>Русский язык и литература</option>
                  <option>Математика</option>
                  <option>Химия и биология</option>
                </select>
              </div>
              <div class="col-6 form-group">
                <label for="rating" class="form-label">Рейтинг</label>
                <input
                  type="number"
                  class="form-control"
                  id="rating"
                  disabled
                  v-model="expertProfile.rating"
                />
              </div>
              <div class="col-6 form-group">
                <label for="ex-activity" class="form-label">
                  Статус активности</label
                >
                <select
                  id="ex-activity"
                  class="form-control"
                  :disabled="!editableProfile"
                  v-model="expertProfile.activity"
                >
                  <option selected disabled>Выберите...</option>
                  <option>Активен</option>
                  <option>Не активен</option>
                </select>
              </div>
              <div class="col-12 form-group">
                <div class="form-check">
                  <input
                    class="form-check-input"
                    type="checkbox"
                    id="ex-mailing"
                    v-model="expertProfile.mailing"
                  />
                  <label class="form-check-label" for="ex-mailing">
                    Я согласен(а) на получение уведомлений на электронную почту
                  </label>
                </div>
              </div>
            </form>
            <button
              class="btn btn-primary"
              @click="editableProfile = !editableProfile"
            >
              {{
                editableProfile ? "Сохранить профиль" : "Редактировать профиль"
              }}
            </button>
          </div>
          <div
            class="tab-pane fade pt-3"
            id="expertises-expert-tab-pane"
            role="tabpanel"
            aria-labelledby="expertises-expert-tab"
            tabindex="0"
          >
            <TableBootstrapCustom
              hover
              bordered
              :table-data="expertisesTable"
              @row-click="openExistingExpertise('Детали экспертизы')"
              @sort-table="sortTable(expertisesTable, $event, $event)"
            />
          </div>
          <div
            class="tab-pane fade pt-3"
            id="analitics-expert-tab-pane"
            role="tabpanel"
            aria-labelledby="analitics-expert-tab"
            tabindex="0"
          >
            <TableBootstrapCustom hover bordered :table-data="analyticsTable" />
          </div>
          <div
            class="tab-pane fade pt-3"
            id="actions-expert-tab-pane"
            role="tabpanel"
            aria-labelledby="actions-expert-tab"
            tabindex="0"
          >
            <TableBootstrapCustom
              hover
              bordered
              :table-data="logsTable"
              @row-click="openExistingLog('Детали действия', $event)"
              @sort-table="sortTable(logsTable, $event)"
            />
          </div>
        </div>
      </template>

      <ModalBootstrapCustom46 :modal-id="modal.modalId" header>
        <template v-slot:modal-title>
          {{ modal.modalTitle }}
        </template>

        <template v-slot:modal-body>
          <LoaderBootstrapCustomBS46
            v-if="appsLoader.isLoading && !appsLoader.isResponse"
            :comment="appsLoader.comment"
            :theme="appsLoader.theme"
          />
          <template v-else>
            <!--          Форма нового заявления-->
            <form v-if="modal.modalTitle === 'Новое заявление'">
              <div v-if="appForm.form.id" class="row">
                <div class="col-10">
                  <Form
                    :form="appForm.form.scheme"
                    :submission="appForm"
                    language="ru"
                    :options="appFormOptions"
                    ref="vueForm"
                  />
                </div>

                <div
                  v-if="appForm.form.actions && appForm.form.actions.length > 0"
                  class="col-2"
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
            <form v-if="modal.modalTitle === 'Детали заявления'">
              <div v-if="appForm.form.id" class="row">
                <div class="col-10">
                  <Form
                    :form="appForm.form.scheme"
                    :submission="appForm"
                    language="ru"
                    :options="appFormOptions"
                    ref="vueForm"
                  />
                </div>
                <div
                  v-if="appForm.form.actions && appForm.form.actions.length > 0"
                  class="col-2"
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
          </template>

          <LoaderBootstrapCustomBS46
            v-if="messagesLoader.isLoading && !messagesLoader.isResponse"
            :comment="messagesLoader.comment"
            :theme="messagesLoader.theme"
          />
          <template v-else>
            <!--          Форма нового сообщения-->
            <form v-if="modal.modalTitle === 'Новое сообщение'">
              <div v-if="messageForm.form.id" class="row">
                <div class="col-10">
                  <Form
                    :form="messageForm.form.scheme"
                    :submission="messageForm"
                    language="ru"
                    :options="messageFormOptions"
                    ref="vueForm"
                  />
                </div>
                <div
                  v-if="
                    messageForm.form.actions &&
                    messageForm.form.actions.length > 0
                  "
                  class="col-2"
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
            <form v-if="modal.modalTitle === 'Детали сообщения'">
              <div v-if="messageForm.form.id" class="row">
                <div class="col-10">
                  <Form
                    :form="messageForm.form.scheme"
                    :submission="messageForm"
                    language="ru"
                    :options="messageFormOptions"
                    ref="vueForm"
                  />
                </div>
                <div
                  v-if="
                    messageForm.form.actions &&
                    messageForm.form.actions.length > 0
                  "
                  class="col-2"
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
            <form v-if="modal.modalTitle === 'Детали экспертизы'">
              <div class="row">
                <div class="col-6">
                  <label for="exp-form-view-number" class="form-label"
                    >№ заявления</label
                  >
                  <input
                    type="text"
                    class="form-control"
                    id="exp-form-view-number"
                    disabled
                    value="0001"
                  />
                </div>
                <div class="col-6">
                  <label for="exp-form-view-fullName" class="form-label"
                    >ФИО педагогического работника</label
                  >
                  <input
                    type="text"
                    class="form-control"
                    id="exp-form-view-fullName"
                    disabled
                    value="Иванов А.П."
                  />
                </div>
                <div class="col-6">
                  <label for="exp-form-view-startDate" class="form-label"
                    >Дата начала экспертизы</label
                  >
                  <input
                    type="date"
                    class="form-control"
                    id="exp-form-view-startDate"
                    disabled
                    value="2022-08-01"
                  />
                </div>
                <div class="col-6">
                  <label for="exp-form-view-deadlineDate" class="form-label"
                    >Срок окончания экспертизы</label
                  >
                  <input
                    type="date"
                    class="form-control"
                    id="exp-form-view-deadlineDate"
                    disabled
                    value="2022-10-01"
                  />
                </div>
                <div class="col-6">
                  <label for="exp-form-view-finishDate" class="form-label"
                    >Дата окончания экспертизы</label
                  >
                  <input
                    type="text"
                    class="form-control"
                    id="exp-form-view-finishDate"
                    disabled
                    value="---"
                  />
                </div>
                <div class="col-6">
                  <label for="exp-form-view-result" class="form-label"
                    >Результат</label
                  >
                  <input
                    type="text"
                    class="form-control"
                    id="exp-form-view-result"
                    disabled
                    value="---"
                  />
                </div>
                <div class="col-6">
                  <label for="exp-form-view-status" class="form-label"
                    >Статус</label
                  >
                  <input
                    type="text"
                    class="form-control"
                    id="exp-form-view-status"
                    disabled
                    value="В работе"
                  />
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

      <!--      <FilterBootstrapCustom :filter-data="filterData" />-->
    </div>
  </div>
</template>

<script>
import { Modal } from "bootstrap";
import ModalBootstrapCustom46 from "../components/universal/ModalBootstrapCustomBS46";
import TableBootstrapCustom from "../components/universal/TableBootstrapCustom";
import { Form } from "vue-formio";
import LoaderBootstrapCustomBS46 from "../components/universal/LoaderBootstrapCustomBS46";
// import FilterBootstrapCustom from "../components/universal/FilterBootstrapCustom";

export default {
  name: "AccountView",
  components: {
    LoaderBootstrapCustomBS46,
    // FilterBootstrapCustom,
    TableBootstrapCustom,
    ModalBootstrapCustom46,
    Form,
  },
  props: [
    "user",
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
      teacherProfile: {
        basic: {
          lastName: this.user.fullInfo.userData.lastName,
          firstName: this.user.fullInfo.userData.firstName,
          middleName: this.user.fullInfo.userData.middleName,
          birthDate: this.user.fullInfo.userData.birthDate.substr(0, 10),
          snils: this.user.fullInfo.userData.snils,
          citizenship: "Российская Федерация",
          email: this.user.fullInfo.contacts[0].value,
          phone: "8-903-903-9000",
          gender: "Женский",
          region: "Новосибирская область",
          educationSubject: "Русский язык и литература",
        },
        address: {
          registrationAddress: "г.Новосибирск, ул.Советская, д.23, кв.45",
          livingAddress: "г.Новосибирск, ул.Ленина, д.12, кв.23",
        },
        document: {
          docType: "Паспорт гражданина РФ",
          docSeries: "5000",
          docNumber: "345678",
          docIssueDate: "2000-05-05",
          docFinishDate: "2050-05-04",
          docIssuer: "УВД Центрального района г.Новосибирска",
        },
        mailing: true,
      },
      expertProfile: {
        lastName: this.user.fullInfo.userData.lastName,
        firstName: this.user.fullInfo.userData.firstName,
        middleName: this.user.fullInfo.userData.middleName,
        snils: this.user.fullInfo.userData.snils,
        email: this.user.fullInfo.contacts[0].value,
        phone: "8-913-913-9999",
        region: "Иркутская область",
        workPosition: "Специалист отдела образования",
        organization: "Министерство образования",
        educationSubject: "Математика",
        rating: 5,
        activity: "Активен",
        mailing: false,
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
    };
  },

  methods: {
    editProfile() {
      this.editableProfile = true;
      this.$emit("edit-profile");
    },
    saveProfile() {
      this.editableProfile = false;
      this.$emit("save-profile");
    },
    openExistingApp(modalTitle, appId) {
      this.$emit("open-existing-app", appId);
      this.openModal(modalTitle);
    },
    createNewApp(modalTitle) {
      this.$emit("create-new-app");
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
      console.log("openExistingExpertise");
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
  },

  mounted() {
    this.modal.instance = new Modal(
      document.getElementById(this.modal.modalId)
    );
  },
};
</script>

<style lang="scss" scoped>
.from-closed-server {
  color: green;
}
</style>
