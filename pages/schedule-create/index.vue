<template>
  <div>
    <h1>Novo contato</h1>

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
                  {{ item }}
                  <v-icon
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
            <v-btn small depressed color="primary" @click="createSchedule">
              Registar
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
  methods: {
    addNumber(number) {
      this.numbers.push(number)
      this.telefone = ''
    },
    removeNumber(item) {
      this.numbers = this.numbers.filter((el) => el !== item)
    },
    async createSchedule() {
      const payload = {
        name: this.name,
        numbers: [...this.numbers],
        email: this.email,
        cpf: this.cpf,
        date_born: this.date_born,
      }

      try {
        const data = await this.$axios.post('/schedule', payload)

        this.snackbar = data.data.status
        this.text = data.data.message

        setTimeout(() => {
          this.$router.push({ path: `/schedule` })
        }, 1000)
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
