<template class="main-page">
  <button @click="isModalVisible=true" class="add-company-btn btn">Добавить компанию</button>
  <table class="company-list">
    <caption class="table-title">Список компаний</caption>
    <tr class="table-head">
      <th>Наименование</th>
      <th>Адрес</th>
      <th>ОГРН</th>
      <th>ИНН</th>
      <th>Дата регистрации</th>
    </tr>
    <tbody class="table-body">
    <tr v-for="company in companies">
      <td>{{company.name}}</td>
      <template v-if="!editing">
        <td @click="editing = true">{{company.address}}</td>
      </template>
      <template v-else>
        <input @input="editing = false" v-model="company.address" />
      </template>
      <td>{{company.ogrn}}</td>
      <td>{{company.inn}} <button @click="getCompanyByINN(company.inn, company)" class="inn-btn">загрузить</button></td>
      <td>{{company.regTime}}</td>
      <td class="delete-item"><button class="delete-row-btn btn" @click="deleteCompany(company)">Удалить компанию</button></td>
    </tr>


    </tbody>
  </table>

  <section v-bind:class="isModalVisible ? 'modal modal--show' : 'modal'" class="modal">

    <form class="modal-content">
      <span class="close-modal" @click="isModalVisible=false">
        <img src="@/assets/img/close.png" alt="close">
      </span>

        <label>Наименование компании <input v-model="companyData.name" type="text" name="companyName" class="modal-input"></label>
        <label>Адрес <input v-model="companyData.address" type="text" name="companyAddress" class="modal-input"></label>
        <label>ОГРН <input v-model="companyData.ogrn" type="text" name="companyOGRN" class="modal-input"></label>
        <label>ИНН <input v-model="companyData.inn" type="text" name="companyINN" class="modal-input"></label>
        <label>Дата регистрации <input v-model="companyData.regTime" type="text" name="companyRegistration" class="modal-input"></label>
      <button type="button" v-on:click="addCompany()" value="Добавить" class="add-data-btn">Добавить</button>
    </form>
  </section>
</template>

<script>

export default {
  data() {
    return {
      isModalVisible: false,
      tagEditingId: '2',
      companyData: {
        name: '',
        address: '',
        ogrn: '',
        inn: '',
        regTime: '',
      },
      companies: [],

    }
  },
  methods: {
    addCompany: function() {
      let company = {
        name: this.companyData.name,
        address: this.companyData.address,
        ogrn: this.companyData.ogrn,
        inn: this.companyData.inn,
        regTime: this.companyData.regTime,
      }
      this.companies.push(company);
      this.companyData = {
        name: '',
        address: '',
        ogrn: '',
        inn: '',
        regTime: '',
      }
      this.isModalVisible = false;
    },
    deleteCompany: function (company) {
       this.companies.splice(this.companies.findIndex(item => item === company), 1)
    },
    setToEditing: function (tag) {
      this.tagEditingId = tag.id
    },
    getCompanyByINN: function (inn, company) {
      const url = "https://suggestions.dadata.ru/suggestions/api/4_1/rs/findById/party";
      const token = "92b8d7b2e157690bac059055e32f9371514acb07";
      const options = {
        method: "POST",
        mode: "cors",
        headers: {
          "Content-Type": "application/json",
          "Accept": "application/json",
          "Authorization": "Token " + token
        },
        body: JSON.stringify({query: inn})
      }

      fetch(url, options)
          .then(response => response.json())
          .then(result => {
            company.name = result.suggestions[0].value;
                company.address = result.suggestions[0].data.address.unrestricted_value;
                company.ogrn = result.suggestions[0].data.ogrn;
                company.regTime = new Date(result.suggestions[0].data.state.registration_date);
          })
          .catch(error => console.log("error", error));
    }
  }
}

</script>

<style scoped>
  @import url("@/assets/style.css");
</style>
