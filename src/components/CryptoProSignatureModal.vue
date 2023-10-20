<template>
  <vb-modal :modal-id="modalId" size="xl" header>
    <template v-slot:modal-header>
      <h5 class="modal-title">Подпись файла</h5>
      <button
        type="button"
        class="close"
        data-dismiss="modal"
        aria-label="Close"
      >
        <span aria-hidden="true">&times;</span>
      </button>
    </template>
    <template v-slot:modal-body>
      <label for="CertListBox">Выберите сертификат</label>
      <select name="CertListBox" id="CertListBox" class="form-control"></select>
      <div class="row mt-3">
        <div class="col-6" id="cryptoProStatusDiv">
          <h4>Информация о программном обеспечении</h4>
          <div>
            <p class="mb-0">
              <span class="dot red" id="ExtensionEnabledImg"></span>
              <span id="ExtensionEnabledTxt">Расширение не загружено</span>
              <span id="ExtensionSolution"></span>
            </p>
            <p class="mb-0">
              <span class="dot" id="PluginEnabledImg"></span>
              <span id="PluginEnabledTxt"
                >Плагин: ожидание загрузки расширения</span
              >
              <span id="PluginSolution"></span>
            </p>
            <p class="mb-0">
              <span class="dot" id="CspEnabledImg"></span>
              <span id="CspEnabledTxt"
                >КриптоПро CSP: ожидание загрузки плагина</span
              >
              <span id="CspSolution"></span>
            </p>
            <p class="mb-0">
              <span class="dot" id="ObjectsLoadedImg"></span>
              <span id="ObjectsLoadedTxt"
                >Объекты плагина: ожидание загрузки провайдера</span
              >
              <span id="ObjectsSolution"></span>
            </p>
            <ul>
              <li class="descr" id="ExtVersionTxt" lang="ru"></li>
              <li class="descr" id="ExtStoreTxt" lang="ru"></li>
              <li class="descr" id="PlugInVersionTxt" lang="ru"></li>
              <li class="descr" id="CSPVersionTxt" lang="ru"></li>
              <li class="descr" id="CSPNameTxt" lang="ru"></li>
              <li class="descr" id="Platform"></li>
              <li class="descr" id="UserAgent"></li>
            </ul>
          </div>

          <div id="CertListBoxToHide" name="CertListBoxToHide">
            <div id="boxdiv" style="display: none">
              <span id="errorarea">У вас отсутствуют личные сертификаты.</span>
            </div>
          </div>
        </div>
        <div class="col-6" id="cert_info" name="CertInfo" style="display: none">
          <h4>Информация о сертификате</h4>
          <p class="info_field mb-0" id="subject"></p>
          <p class="info_field mb-0" id="issuer"></p>
          <p class="info_field mb-0" id="from"></p>
          <p class="info_field mb-0" id="till"></p>
          <p class="info_field mb-0" id="provname"></p>
          <p class="info_field mb-0" id="privateKeyLink"></p>
          <p class="info_field mb-0" id="algorithm"></p>
          <p class="info_field mb-0" id="status"></p>
          <p class="info_field mb-0" id="location"></p>
          <!--        Добавлено поле для отпечатка сертификата-->
          <p class="info_field mb-0 d-none" id="thumbprint"></p>
        </div>
        <div class="d-none">
          <!--    Поле для подписи-->
          <textarea id="DataToSignTxtBox" name="DataToSignTxtBox"></textarea>
          <!--    Поле с подписью-->
          <p
            class="info_msg"
            id="SignatureTitle"
            name="SignatureTitle"
            style="display: none"
          >
            Подпись:
          </p>
          <textarea id="SignatureTxtBox"></textarea>
        </div>
      </div>
      <div class="d-flex justify-content-end">
        <button
          id="SignBtn"
          type="button"
          class="btn btn-primary"
          name="SignData"
          @click="$emit('get-hash-to-sign')"
        >
          Подписать
        </button>
        <button
          type="button"
          class="btn btn-outline-secondary ml-3"
          data-dismiss="modal"
        >
          Отмена
        </button>
      </div>
    </template>
  </vb-modal>
</template>

<script>
import VbModal from "./universal/BS46Modal";
export default {
  name: "CryptoProSignatureModal",
  components: { VbModal },
  props: {
    modalId: String,
    cryptoProSignature: Object,
  },
};
</script>

<style scoped>
.error {
  color: red;
  font-weight: bold;
}

.warning {
  color: orange;
  font-weight: bold;
}

li.descr:empty {
  display: none;
}

.dot {
  height: 10px;
  width: 10px;
  background-color: #d8d8d8;
  border-radius: 50%;
  display: inline-block;
  margin-right: 6px;
}

.red {
  background-color: #e83127;
}

.yellow {
  background-color: #face48;
}

.green {
  background-color: #59bd2a;
}
</style>
