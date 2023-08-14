<template>
  <q-page padding>
    <div class="q-pa-md">
      <q-form>
        <div class="row q-col-gutter-sm">
          <div
            class="col"
            style="max-width: 200px;"
          >
            <q-input
              v-model="cnpjNumber"
              buttom-slots
              maxlength="18"
              mask="##.###.###/####-##"
              unmasked-value
              label="CNPJ"
              :dense="dense"
              error-message="CNPJ inválido"
              :error="cnpjNumber.length >= 14 && !cnpjIsValid"
              @blur="() => getCompany(cnpjNumber)"
            />
          </div>
          <div class="col">
            <q-input
              v-model="companyName"
              label="Razão Social"
              :dense="dense"
            />
          </div>
        </div>
        <div class="row q-col-gutter-sm">
          <div class="col-5">
            <q-input
              v-model="companyAddress"
              label="Endereço"
              :dense="dense"
            />
          </div>
          <div class="col-1">
            <q-input
              v-model="companyAddressNumber"
              label="Número"
              :dense="dense"
            />
          </div>
          <div class="col-2">
            <q-input
              v-model="companyAddressComplement"
              label="Complemento"
              :dense="dense"
            />
          </div>
          <div class="col-3">
            <q-input
              v-model="companyAddressNeighborhood"
              label="Bairro"
              :dense="dense"
            />
          </div>
          <div class="col-1">
            <q-input
              v-model="companyAddressCep"
              label="CEP"
              :dense="dense"
            />
          </div>
        </div>
      </q-form>
    </div>
  </q-page>
</template>

<script>
import { ref, computed } from 'vue'
import { cnpj } from 'cpf-cnpj-validator'
import { api } from 'src/boot/axios'
import { useQuasar } from 'quasar'

export default {

  setup () {
    const cnpjNumber = ref('')
    const companyName = ref('')
    const companyAddress = ref('')
    const companyAddressNumber = ref('')
    const companyAddressNeighborhood = ref('')
    const companyAddressComplement = ref('')
    const companyAddressCep = ref('')
    const $q = useQuasar()
    return {
      cnpjNumber,
      companyName,
      companyAddress,
      companyAddressNumber,
      companyAddressNeighborhood,
      companyAddressComplement,
      companyAddressCep,
      $q,
      cnpjIsValid: computed(() => cnpj.isValid(cnpjNumber.value))
    }
  },
  // name: 'PageName',

  methods: {
    getCompany (data) {
      // console.log(data)
      api.get('https://minhareceita.org/' + data).then((response) => {
        this.companyName = response.data.razao_social
        this.companyAddress = response.data.descricao_tipo_de_logradouro + ' ' + response.data.logradouro
        this.companyAddressNumber = response.data.numero
        this.companyAddressNeighborhood = response.data.bairro
        this.companyAddressComplement = response.data.complemento
        this.companyAddressCep = response.data.cep
      }).catch(() => {
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
