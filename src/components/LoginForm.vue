<template>
<div class="border">
   <h1>Вход</h1>
    <div class="form-login">

        <form class="form" @submit.prevent="onSubmit">

            <div class="group-field" :class="{ 'field--error wobble-error': $v.email.$error}">
                <div class="input-block">
                    <input v-model="email" :disabled="disabled" required>
                    <label>Адрес электронной почты</label>
                </div>
                <div class="error_block">
                    <div class="error" v-if="($v.email.$dirty && !$v.email.required)">Введите e-mail</div>
                    <div class="error" v-if="($v.email.$dirty && !$v.email.email)">Введите корректный e-mail</div>
                </div>
            </div>

            <div class="group-field" :class="{ 'field--error wobble-error': $v.password.$error }">
                <div class="input-block">
                    <input type="password" v-model="password" :disabled="disabled" v-show="!showPassword" required>
                    <input type="text" v-model="password" :disabled="disabled" v-show="showPassword" required>
                   <div class="buttonShowPassword" @click="showPassword=!showPassword" >
                      <div class="iconPassowordShow" v-show="showPassword"></div>
                      <div class="iconPassowordHide" v-show="!showPassword"></div>
                    </div>
                    <label>Пароль</label>
                </div>
                <div class="error_block">
                    <div class="error" v-if="!$v.password.required">Введите пароль</div>
                    <div class="error" v-if="!$v.password.betweenLength">{{ $v.password.between }}Длина пароля должна
                        быть от {{ $v.password.$params.betweenLength.min }} до {{ $v.password.$params.betweenLength.max
                        }} символов
                    </div>
                </div>
            </div>

            <button :disabled="disabled" class="button" :class="{ 'button-valid': $v.email.required && $v.password.required}" type="submit">Войти</button>



            <a class="link_recovery" @click="()=>{toggleRecoveryPassword()}">Я не помню пароль</a>
            <p class="link_noacc"> Нет аккаунта? <a class="link_reg" @click="()=>{toggleRegister()}"> Создать </a></p>
            <!-- test link  -->
            <!-- <a class="link_succ" @click="()=>{toRegistrationSuccessModal()}">Registration Success</a> -->
            <!-- test link -->


<Loader  v-if="disabled"/>
        </form>


    </div>
    </div>
</template>

<script>
  import { required, email, minLength, maxLength, and, helpers } from 'vuelidate/lib/validators'
  import Loader from './Loader'


  const betweenLength = (min, max) => helpers.withParams(
    {
      min,
      max
    },
    and(minLength(min), maxLength(max))
  )

  export default {
    name: 'LoginForm',
    components: { Loader },
    props: ['onSuccess', 'toggleLoginModal', 'toggleRegisterModal', 'toggleRegistrationSuccessModal', 'toggleRecoveryPasswordModal'],
    data () {
      return {
        email: '',
        password: '',
        messages: [],
        disabled: false,
        showPassword: false,
      }
    },
    validations: {
      email: {
        required,
        email,
      },
      password: {
        required,
        betweenLength: betweenLength(4, 18),
      }
    },
    methods: {
      async onSubmit () {
        this.$v.$touch()
        if (this.$v.$invalid) {
          return
        }
        if (!this.$v.$invalid) {
          const { email, password } = this

          this.disabled = true
          const rez = await this.$store.dispatch('loginUser', {
            email,
            password
          })
          if (rez) {
            this.toggleLoginModal()
            this.$router.push('/profile')
          }
          this.disabled = false
        }
      },
      toggleRegister () {
        this.toggleLoginModal()
        this.toggleRegisterModal()
      },
      toggleRecoveryPassword(){
        this.toggleLoginModal()
        this.toggleRecoveryPasswordModal()
      },
      toRegistrationSuccessModal(){
        this.toggleLoginModal()
        this.toggleRegistrationSuccessModal()
      },
    }
  }
</script>

<style lang="scss" scoped>
@import "../assets/variables.scss";
@import "../assets/styles/_forms.scss";



</style>
