<template>
  <div>
    <h1>Editar contato</h1>

    <v-snackbar v-model="snackbar" color="success">
      {{ text }}
      <template v-slot:actions>
        <v-btn color="pink" variant="text" @click="snackbar = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>

    <v-form v-model="valid">
      <v-container>
        <v-row>
          <v-col cols="12" md="2">
            <v-text-field
              v-model="name"
              :rules="nameRules"
              label="Nome"
              required
            ></v-text-field>
          </v-col>

          <v-col cols="12" md="2">
            <v-text-field
              v-model="email"
              :rules="emailRules"
              label="E-mail"
              required
            ></v-text-field>
          </v-col>

          <v-col cols="12" md="2">
            <v-text-field
              v-model="telefone"
              label="Telefones"
              append-icon="mdi-plus-box"
              required
              @click:append="addNumber(telefone)"
            >
            </v-text-field>
            <div>
              <ul v-for="(item, i) in numbers" :key="i">
                <li class="font-weight-light">
                  {{ item
                  }}<v-icon
                    small
                    color="red darken-2"
                    @click.stop="removeNumber(item)"
                  >
                    mdi-trash-can-outline
                  </v-icon>
                </li>
              </ul>
            </div>
          </v-col>
          <v-col cols="12" md="2">
            <v-text-field v-model="cpf" label="CPF" required></v-text-field>
          </v-col>

          <v-col cols="12" md="2">
            <v-menu
              ref="menu"
              v-model="menu"
              :close-on-content-click="false"
              :return-value.sync="date"
              transition="scale-transition"
              offset-y
              min-width="auto"
            >
              <template #activator="{ on, attrs }">
                <v-text-field
                  v-model="date_born"
                  label="Data Nasc."
                  append-icon="mdi-calendar"
                  readonly
                  v-bind="attrs"
                  v-on="on"
                />
              </template>
              <v-date-picker v-model="date_born" no-title locale="pt-BR">
                <v-spacer />
                <v-btn text color="primary" @click="cancelarSelectedDate">
                  cancelar
                </v-btn>
                <v-btn text color="primary" @click="$refs.menu.save(date_born)">
                  ok
                </v-btn>
              </v-date-picker>
            </v-menu>
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-btn small depressed color="primary" @click="editSchedule">
              Editar
            </v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-form>
  </div>
</template>

<script>
export default {
  data: () => ({
    valid: false,
    menu: false,
    name: '',
    cpf: '',
    date: '',
    date_born: '',
    telefone: '',
    snackbar: false,
    text: '',
    numbers: [],
    nameRules: [
      (v) => !!v || 'Name is required',
      (v) => v.length >= 6 || 'No minimu 6 caracteres',
    ],
    email: '',
    emailRules: [
      (v) => !!v || 'E-mail is required',
      (v) => /.+@.+/.test(v) || 'E-mail must be valid',
    ],
  }),
  created() {
    this.getScheduleById()
  },
  methods: {
    async getScheduleById() {
      try {
        const data = await this.$axios.get(`/schedule/${this.$route.query.id}`)

        this.name = data.data.data[0].name
        this.email = data.data.data[0].email
        this.cpf = data.data.data[0].cpf
        this.date_born = data.data.data[0].date_born
        this.numbers = data.data.data[0].numbers.map((el) =>
          el.number.toString()
        )
      } catch (error) {
        console.log('🚀 ~ getScheduleById ~ error:', error)
      }
    },
    removeNumber(item) {
      this.numbers = this.numbers.filter((el) => el !== item)
    },
    addNumber(number) {
      this.numbers.push(number)
      this.telefone = ''
    },
    async editSchedule() {
      const payload = {
        name: this.name,
        numbers: [...this.numbers],
        email: this.email,
        cpf: this.cpf,
        date_born: this.date_born,
      }

      try {
        const data = await this.$axios.put(
          `/schedule/${this.$route.query.id}`,
          payload
        )

        this.snackbar = data.data.status
        this.text = data.data.message

        setTimeout(() => {
          this.$router.push({ path: `/schedule` })
        }, 3000)
      } catch (error) {
        console.log('🚀 ~ createSchedule ~ error:', error)
      }
    },
    cancelarSelectedDate() {
      this.date = ''
      this.menu = false
    },
  },
}
</script>

<style></style>
