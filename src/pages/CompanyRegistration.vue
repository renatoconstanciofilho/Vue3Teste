<template>
  <q-page padding>
    <div class="q-pa-md">
      <q-form>
        <div class="row q-col-gutter-sm">
          <div
            class="col"
            style="max-width: 200px"
          >
            <q-input
              v-model="company.cnpj"
              buttom-slots
              maxlength="18"
              mask="##.###.###/####-##"
              unmasked-value
              label="CNPJ"
              :dense="dense"
              error-message="CNPJ inválido"
              :error="company.cnpj.length >= 14 && !cnpjIsValid"
              @blur="() => getCompany()"
            />
          </div>
          <div class="col">
            <q-input
              v-model="company.name"
              label="Razão Social"
              :dense="dense"
            />
          </div>
        </div>
        <div class="row q-col-gutter-sm">
          <div class="col-5">
            <q-input
              v-model="company.address.name"
              label="Endereço"
              :dense="dense"
            />
          </div>
          <div class="col-1">
            <q-input
              v-model="company.address.number"
              label="Número"
              :dense="dense"
            />
          </div>
          <div class="col-2">
            <q-input
              v-model="company.address.complement"
              label="Complemento"
              :dense="dense"
            />
          </div>
          <div class="col-3">
            <q-input
              v-model="company.address.neighborhood"
              label="Bairro"
              :dense="dense"
            />
          </div>
          <div class="col-1">
            <q-input
              v-model="company.address.cep"
              label="CEP"
              :dense="dense"
            />
          </div>
        </div>
        <!-- <q-separator />

        <div class="row q-col-gutter-sm">
          <div class="col-3">
            <q-input
              v-model="newContact.nome"
              label="nome"
              :dense="dense"
            />
          </div>
          <div class="col-1">
            <q-input
              v-model="newContact.telefone"
              label="Telefone"
              :dense="dense"
            />
          </div>
        </div>

        <div class="q-pa-md">
          <q-table
            title="Contatos"
            :rows="company.contacts"
            :columns="columns"
            row-key="name"
          />
        </div>

        <q-btn
          color="white"
          text-color="black"
          label="Adicionar"
          @click="adicionarContato"
        /> -->
      </q-form>
    </div>
    <div clas="q-pa-md -q-gutter-sm">
      <q-btn
        label="Adicionar Contato"
        color="primary"
        @click="addContact = true"
      />
      <q-dialog
        v-model="addContact"
        persistent
      >
        <q-card style="min-width: 500px">
          <q-card-section>
            <div class="text-h6">
              Contato
            </div>
          </q-card-section>

          <q-card-section class="q-pt-none">
            <q-form>
              <div class="row q-col-gutter-sm">
                <div class="col-1">
                  <q-input
                    v-model="newContact.title"
                    label="Título"
                    maxlength="3"
                    :dense="dense"
                  />
                </div>
                <div class="col-4">
                  <q-input
                    v-model="newContact.name"
                    label="Nome"
                    :dense="dense"
                  />
                </div>
                <div class="col-7">
                  <q-input
                    v-model="newContact.surname"
                    label="Sobrenome"
                    :dense="dense"
                  />
                </div>
              </div>
              <div class="row q-col-gutter-sm">
                <div class="col-6">
                  <q-input
                    v-model="newContact.phone"
                    label="Telefone"
                    maxlength="14"
                    mask="(##) ####-####"
                    :dense="dense"
                  />
                </div>
                <div class="col-6">
                  <q-input
                    v-model="newContact.mobile"
                    label="Celular"
                    maxlength="15"
                    mask="(##) #####-####"
                    :dense="dense"
                  />
                </div>
              </div>
              <div class="row q-col-gutter-sm">
                <div class="col-12">
                  <q-input
                    v-model="newContact.email"
                    label="E-Mail"
                    :dense="dense"
                  />
                </div>
              </div>
              <div class="row q-col-gutter-sm">
                <div class="col-12">
                  <q-input
                    v-model="newContact.department"
                    label="Departamento"
                    :dense="dense"
                  />
                </div>
              </div>
            </q-form>
          </q-card-section>

          <q-card-actions
            align="right"
            class="text-primary"
          >
            <q-btn
              v-close-popup
              flat
              icon="mdi-close"
            />
            <q-btn
              v-close-popup
              flat
              icon="mdi-content-save"
              @click="newContact"
            />
          </q-card-actions>
        </q-card>
      </q-dialog>
    </div>
    <div clas="q-pa-md -q-gutter-sm">
      <q-table
        title="Contatos"
        :rows="company.contacts"
        :columns="columns"
        row-key="name"
      />
    </div>
  </q-page>
</template>

<script>
import { ref, computed } from 'vue'
import { cnpj } from 'cpf-cnpj-validator'
import { api } from 'src/boot/axios'
import { useQuasar } from 'quasar'

// const columns = [
//   {
//     name: 'nome',
//     align: 'center',
//     label: 'nome',
//     field: 'nome',
//     sortable: true
//   },
//   { name: 'telefone', label: 'telefone', field: 'telefone', sortable: true }
// ]

export default {
  setup () {
    const $q = useQuasar()
    const company = ref({
      cnpj,
      number: ref(''),
      name: ref(''),
      address: ref({
        name: ref(''),
        number: ref(''),
        neighborhood: ref(''),
        complement: ref(''),
        cep: ref('')
      }),
      contacts: ref([])
    })
    const columns = [
      {
        name: 'title',
        required: true,
        label: 'Título',
        align: 'left',
        field: row => company.value.contacts.title,
        formmat: val => `${val}`,
        sortable: true
      },
      {
        name: 'name',
        required: true,
        label: 'Nome',
        align: 'left',
        field: row => company.value.contacts.name,
        formmat: val => `${val}`,
        sortable: true
      },
      {
        name: 'surname',
        required: true,
        label: 'Sobrenome',
        align: 'left',
        field: row => company.value.contacts.surname,
        formmat: val => `${val}`,
        sortable: true
      },
      {
        name: 'phone',
        required: true,
        label: 'Telefone',
        align: 'left',
        field: row => company.value.contacts.phone,
        formmat: val => `${val}`,
        sortable: true
      },
      {
        name: 'mobile',
        required: true,
        label: 'Celular',
        align: 'left',
        field: row => company.value.contacts.mobile,
        formmat: val => `${val}`,
        sortable: true
      },
      {
        name: 'email',
        required: true,
        label: 'E-Mail',
        align: 'left',
        field: row => company.value.contacts.email,
        formmat: val => `${val}`,
        sortable: true
      },
      {
        name: 'department',
        required: true,
        label: 'Departamento',
        align: 'left',
        field: row => company.value.contacts.department,
        formmat: val => `${val}`,
        sortable: true
      }
    ]
    return {
      addContact: ref(false),
      company,
      columns,
      newContact: ref({}),
      $q,
      cnpjIsValid: computed(() => {
        return cnpj.isValid(company.value.cnpj)
      })
    }
  },
  // name: 'PageName',

  methods: {
    adicionarContato () {
      this.company.contacts.push({
        title: this.newContact.title,
        name: this.newContact.name,
        surname: this.newContact.surname,
        department: this.newContact.department,
        phone: this.newContact.phone,
        mobile: this.newContact.mobile,
        email: this.newContact.email
      })
      console.log(this.company.contacts[0])
    },
    getCompany () {
      // console.log(data)
      api
        .get('https://minhareceita.org/' + this.company.cnpj)
        .then((response) => {
          this.company.name = response.data.razao_social
          this.company.address.name =
            response.data.descricao_tipo_de_logradouro +
            ' ' +
            response.data.logradouro
          this.company.address.number = response.data.numero
          this.company.address.neighborhood = response.data.bairro
          this.company.address.complement = response.data.complemento
          this.company.address.cep = response.data.cep
        })
        .catch(() => {
          this.$q.notify({
            color: 'negative',
            position: 'top',
            message: 'Erro ao consultar CNPJ',
            icon: 'mdi-alert'
          })
        })
    }
  }
}
</script>
