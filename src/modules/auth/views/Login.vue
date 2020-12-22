<template>
  <v-container fluid>
    <v-row justify="center">
      <v-col
        cols="12"
        sm="6"
        md="4"
        lg="3"
        xl="3"
      >
        <v-card
          width="400"
          elevation="12"
        >
          <v-toolbar
            color="primary"
            dark
          >
            <v-toolbar-title>{{ texts.toolbar }}</v-toolbar-title>
          </v-toolbar>
          <v-card-text>
            <v-form>
              <v-text-field
                v-if="!isLogin"
                prepend-icon="person"
                name="name"
                label="Nome"
                type="text"
                :success="!$v.user.name.$invalid"
                :error-messages="nameErrors"
                v-model.trim="$v.user.name.$model"
              >

              </v-text-field>
              <v-text-field
                prepend-icon="email"
                name="email"
                label="Email"
                type="email"
                :success="!$v.user.email.$invalid"
                :error-messages="emailErrors"
                v-model.trim="$v.user.email.$model"
              >

              </v-text-field>
              <v-text-field
                prepend-icon="lock"
                name="password"
                label="Password"
                type="password"
                :success="!$v.user.password.$invalid"
                :error-messages="passwordErrors"
                v-model.trim="$v.user.password.$model"
              >

              </v-text-field>
            </v-form>
            <v-btn
              block
              depressed
              color="secondary"
              @click="isLogin=!isLogin"
            >
              {{ texts.button }}
            </v-btn>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
              :disabled="$v.$invalid"
              color="primary"
              large
              @click="submit"
            >{{ texts.toolbar }}</v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>
<script>
import { required, email, minLength } from 'vuelidate/lib/validators'
import AuthService from './../services/auth-service'
export default {
  name: 'Login',
  data: () => ({
    isLogin: true,
    user: {
      name: '',
      email: '',
      password: ''
    }
  }),

  validations () {
    const validations = {
      user: {
        email: {
          required,
          email
        },
        password: {
          required,
          minLength: minLength(6)
        }
      }
    }
    if (this.isLogin) { return validations }
    return {
      user: {
        ...validations.user,
        name: {
          required,
          minLength: minLength(3)
        }
      }

    }
  },
  computed: {
    texts () {
      return this.isLogin
        ? { toolbar: 'Entrar', button: 'Criar conta' }
        : { toolbar: 'Criar conta', button: 'Já tenho conta' }
    },

    nameErrors () {
      const errors = []
      const name = this.$v.user.name
      if (!name.$dirty) {
        return errors
      }
      !name.required && errors.push('O Nome é obrigatório')
      !name.minLength && errors.push(`O nome deve conter pelo menos ${name.$params.minLength.min} caracteres`)

      return errors
    },
    emailErrors () {
      const errors = []
      const email = this.$v.user.email
      if (!email.$dirty) { return errors }
      !email.required && errors.push('O email é obrigatório')
      !email.email && errors.push('O email é inválido')
      return errors
    },
    passwordErrors () {
      const errors = []
      const password = this.$v.user.password
      if (!password.$dirty) { return errors }
      !password.required && errors.push('A password é obrigatória')
      !password.minLength && errors.push(`A password deve conter pelo menos ${password.$params.minLength.min} caracteres `)
      return errors
    }
  },
  methods: {
    log () {
      console.log('vuelidate', this.$v)
    },
    async submit () {
      try {
        console.log('User: ', this.user)
        const authData = await AuthService.login(this.user)
        console.log('AuthData: ', authData)
      } catch (err) {
        console.log('Erro: ', err)
      }
    }
  }
}
</script>
