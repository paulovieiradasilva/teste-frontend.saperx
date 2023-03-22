<template>
  <div>
    <h1>Lista de Contatos</h1>

    <v-snackbar v-model="snackbar" color="success">
      {{ text }}
      <template v-slot:actions>
        <v-btn color="pink" variant="text" @click="snackbar = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>

    <div class="d-flex justify-end">
      <v-btn
        small
        depressed
        color="primary"
        @click="redirectTo({ type: 'create' })"
      >
        Novo contato
      </v-btn>
    </div>
    <v-simple-table>
      <template v-slot:default>
        <thead>
          <tr>
            <th class="text-left">#</th>
            <th class="text-left">Name</th>
            <th class="text-left">E-mail</th>
            <th class="text-left">Numeros</th>
            <th class="text-left">CPF</th>
            <th class="text-left">Data Nac.</th>
            <th>Ações</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in desserts" :key="item.id">
            <td>{{ item.id }}</td>
            <td>{{ item.name }}</td>
            <td>{{ item.email }}</td>
            <td>
              {{
                item.numbers
                  .map((numbers) => numbers)
                  .map((number) => number)
                  .map((item) => item)
                  .reduce((acc, el) => (acc += `${el.number} `), ' ')
              }}
            </td>
            <td>{{ item.cpf }}</td>
            <td>{{ item.date_born }}</td>
            <td>
              <v-icon
                small
                color="green darken-2"
                @click="redirectTo({ type: 'edit', id: item.id })"
              >
                mdi-circle-edit-outline
              </v-icon>
              <v-icon small color="red darken-2" @click="removeItem(item)">
                mdi-trash-can-outline
              </v-icon>
            </td>
          </tr>
        </tbody>
      </template>
    </v-simple-table>
  </div>
</template>

<script>
export default {
  computed: {},
  data() {
    return {
      desserts: [],
      snackbar: false,
      text: '',
    }
  },
  created() {
    this.getSchedule()
  },
  methods: {
    async getSchedule() {
      try {
        const data = await this.$axios.get('/schedule')
        this.desserts = data.data.data
      } catch (error) {
        console.log('🚀 ~ getSchedule ~ error:', error)
      }
    },
    redirectTo({ type, id }) {
      const options = {
        create: () => {
          this.$router.push({ path: `/schedule-create` })
        },
        edit: () => {
          this.$router.push({ path: `/schedule-edit?id=${id}` })
        },
      }
      options[type]()
    },
    async removeItem({ id }) {
      const data = await this.$axios.delete(`/schedule/${id}`)

      this.snackbar = data.data.status
      this.text = data.data.message
      this.getSchedule()
    },
    editItem({ id }) {
      console.log('🚀 ~ editItem ~ editItem:', id)
    },
  },
}
</script>
<style></style>
