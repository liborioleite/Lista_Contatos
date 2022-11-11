<template>
  <div>
    <b-container
      v-if="!has_login"
      class="d-flex justify-content-center container text-center"
    >
      <b-card class="b-card">
        <b-card-title>Login</b-card-title>
        <b-form-input
          v-model="user"
          placeholder="Usuário"
          maxlength="50"
        ></b-form-input>
        <b-form-input
          v-model="password"
          type="password"
          placeholder="Senha"
          maxlength="8"
          class="mt-2"
        ></b-form-input>
        <b-button class="mt-3" @click="login()">Sing in</b-button>
      </b-card>
    </b-container>
    <b-container v-if="has_login">
      <b-card class="text-center">
        <b-card-title> Lista de Contatos </b-card-title>
        <b-card-text>
          <b-button variant="primary" @click="showModal()"
            >Novo Contato</b-button
          >

          <b-modal ref="my-modal" hide-footer title="Criar Contato">
            <div class="d-block text-center">
              <b-form-input
                v-model="contact.name"
                placeholder="Nome"
              ></b-form-input>
              <b-form-input
                v-model="contact.age"
                placeholder="Idade"
                class="mt-2"
              ></b-form-input>
              <b-form-input
                v-model="contact.phone"
                placeholder="Telefone"
                class="mt-2"
              ></b-form-input>
            </div>

            <b-button
              v-if="operation == 'insert'"
              class="mt-2"
              variant="primary"
              block
              @click="saveContact()"
              >Salvar</b-button
            >

            <b-button
              v-if="operation == 'edit'"
              class="mt-2 ms-2"
              variant="warning"
              block
              @click="editContact()"
              >Editar</b-button
            >
          </b-modal>

          <div class="mt-2">
            <b-list-group
              class="mt-2"
              v-for="(contato, index) in contacts"
              :key="index"
            >
              <b-list-group-item href="#" class="flex-column align-items-start">
                <div class="d-flex w-100 justify-content-center">
                  <h5 class="mb-1">Contato</h5>
                </div>

                <div class="d-flex">
                  <p class="mb-1">ID: {{ index }}</p>
                </div>

                <div class="d-flex">
                  <p class="mb-1">Nome: {{ contato.name }}</p>
                </div>
                <div class="d-flex">
                  <p class="mb-1">Idade: {{ contato.age }}</p>
                </div>

                <div class="d-flex">
                  <p class="mb-1">Telefone: {{ contato.phone }}</p>
                </div>
                <b-button variant="danger" @click="removeContact(index)"
                  >Excluir Contato</b-button
                >
                <b-button variant="warning" @click="showEditModal(index)"
                  >Editar Contato</b-button
                >
              </b-list-group-item>
            </b-list-group>
          </div>
          <!-- <ol>
            <li v-for="(contato, index) in contacts" :key="index">
              <p>Nome: {{ contato.name }}</p>
              <p>Idae: {{ contato.age }}</p>
              <p>Telefone: {{ contato.phone }}</p>
            </li>
          </ol> -->
        </b-card-text>
      </b-card>
      <!-- <b-form-input placeholder="Search"></b-form-input> -->
    </b-container>
  </div>
</template>

<script>
export default {
  data() {
    return {
      user: "",
      password: "",
      has_login: false,
      operation: "insert",
      contact_ref: null,
      contacts: [],
      contact: { name: "", age: "", phone: "" },
    };
  },

  created() {
    this.isLoged();
  },

  mounted() {
    //PERSISTINDO OS DADOS NO LOCAL STORAGE
    const data = localStorage.getItem("lista");
    const lista = JSON.parse(data);

    if (lista) {
      this.contacts = lista;
    } else {
      this.contacts = [];
    }
  },
  methods: {
    login() {
      localStorage.setItem("user", this.user);
      localStorage.setItem("password", this.password);
      this.has_login = true;
    },

    isLoged() {
      const has_user = localStorage.getItem("user");
      const has_password = localStorage.getItem("password");
      if (has_user != null && has_password != null) {
        this.has_login = true;
      }
    },
    showModal() {
      this.operation = "insert";
      this.$refs["my-modal"].show();
    },
    showEditModal(index) {
      this.operation = "edit";
      let contato = this.contacts.find((item, i) => i == index);
      this.contact_ref = index;
      this.contact = contato;
      this.$refs["my-modal"].show();
    },
    saveContact() {
      this.contacts.push({ ...this.contact });

      localStorage.setItem("lista", JSON.stringify(this.contacts));
      this.$refs["my-modal"].toggle("#toggle-btn");
    },

    removeContact(index) {
      this.contacts.splice(index, 1);
      const lista = JSON.stringify(this.contacts);
      const data = localStorage.setItem("lista", lista);
    },

    editContact() {
      this.contacts[this.contact_ref] = this.contact;
      localStorage.setItem("lista", JSON.stringify(this.contacts));
      this.$refs["my-modal"].toggle("#toggle-btn");
    },
  },
};
</script>

<style>
.container {
  height: 100vh;
  align-items: center;
}
.b-card {
  width: 30%;
  height: 300px;
  padding-top: 4rem;
}
</style>