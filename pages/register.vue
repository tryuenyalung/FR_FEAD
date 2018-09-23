<template>
  <div>

    <loader :isActive="this.registrationLoader" />

    <section class="hero has-background-white-ter is-fullheight">

      <div class="hero-body">

        <div class="container">

          <div class="columns">

            <div class="column is-half is-offset-one-quarter">

              <form name="registerForm">
                <!-- @submit.prevent="validateBeforeSubmit" -->

                <div class="box">

                  <div>
                    <nuxt-link class=" has-text-danger" to="/">
                      <span class="fa fa-chevron-circle-left fa-lg"></span>&nbsp;
                      <span class="has-text-danger subtitle is-5">Login</span>
                    </nuxt-link>
                    <br>
                    <br>
                  </div>

                  <div class="columns">

                    <div class="column">
                      <p for="label">Username</p>
                      <input name="username" v-model="username" class="input" data-vv-as="username" :class="{ 'is-danger': errors.has('username') }"
                        v-validate="'required|email'" type="text" placeholder="Username">
                      <small class="has-text-danger">{{ errors.first('username') }}</small>
                    </div>


                    <div class="column">
                      <p for="label">Password</p>
                      <input name="password" v-model="password" class="input" :class="{ 'is-danger': errors.has('password') }"
                        v-validate="{required:true, min:6, is:confirm_password}" type="password" placeholder="Password">
                      <small class="has-text-danger">{{ errors.first('password') }}</small>
                    </div>

                    <div class="column">
                      <p for="label">Confirm Password</p>
                      <input name="confirm_password" class="input" data-vv-as="confirm password" :class="{ 'is-danger': errors.has('password:is') }"
                        v-model="confirm_password" v-validate="'required'" type="password" placeholder="Confirm Password">
                      <small class="has-text-danger">{{ errors.first('confirm_password') }}</small>
                      <br>
                      <small class="has-text-danger">{{ errors.first('password:is') }}</small>
                    </div>

                  </div>
                  <!-- columns -->


                  <span>Name</span>
                  <div class="columns">

                    <div class="column">
                      <input name="name.firstName" v-model="firstName" data-vv-as="first name" class="input" :class="{ 'is-danger': errors.has('name.firstName') }"
                        v-validate="'required'" type="text" placeholder="First Name">
                      <small class="has-text-danger">{{ errors.first('name.firstName') }}</small>
                    </div>

                    <div class="column">
                      <input name="name.middleName" v-model="middleName" data-vv-as="middle name" class="input" :class="{ 'is-danger': errors.has('name.middleName') }"
                        v-validate="'required'" type="text" placeholder="Middle Name">
                      <small class="has-text-danger">{{ errors.first('name.middleName') }}</small>
                    </div>


                    <div class="column">
                      <input name="name.lastName" v-model="lastName" data-vv-as="last name" class="input" :class="{ 'is-danger': errors.has('name.lastName') }"
                        v-validate="'required'" type="text" placeholder="Last Name">
                      <small class="has-text-danger">{{ errors.first('name.lastName') }}</small>
                    </div>

                  </div>
                  <!-- columns -->

                  <div class="columns">

                    <div class="column">
                      <p for="label">Address</p>
                      <input name="address" v-model="address" class="input" :class="{ 'is-danger': errors.has('address') }"
                        v-validate="'required'" type="text" placeholder="Street, Barangay, Municipality, Province, City">
                      <small class="has-text-danger">{{ errors.first('address') }}</small>
                    </div>

                    <div class="column">
                      <p for="label">Date of Birth</p>
                      <input name="dateOfBirth" v-model="dateOfBirth" class="input" :class="{ 'is-danger': errors.has('dateOfBirth') }"
                        v-validate="'required'" type="date" placeholder="Street, Barangay, Municipality, Province, City">
                      <small class="has-text-danger">{{ errors.first('dateOfBirth') }}</small>
                    </div>

                  </div>
                  <!-- columns -->





                  <div class="columns">

                    <div class="column">
                      <p for="label">Sex</p>
                      <div class="select is-fullwidth" :class="{ 'is-danger': errors.has('gender') }">
                        <select name="gender" v-model="gender" v-validate="'required'">
                          <option value="">Please select</option>
                          <option class="is-uppercase" :key="gender" v-for="gender in genderOption" :value="gender">{{
                            gender }}</option>
                        </select>
                      </div>
                      <small class="has-text-danger">{{ errors.first('gender') }}</small>
                    </div>

                    <div class="column">

                      <p for="label">Position</p>
                      <div class="select is-fullwidth" :class="{ 'is-danger': errors.has('position') }">
                        <select name="position" v-model="position" v-validate="'required'">
                          <option value="">Select Position</option>
                          <option :key="position" v-for="position in positionOption" :value="position">{{ position }}</option>
                        </select>
                        <small class="has-text-danger">{{ errors.first('position') }}</small>
                      </div>

                    </div>

                    <div class="column">

                      <p for="label">Department</p>
                      <div class="select is-fullwidth" :class="{ 'is-danger': errors.has('department') }">
                        <select name="department" v-model="department" v-validate="'required'" required>
                          <option value="">Select Department</option>
                          <option :key="department" v-for="department in departmentOption" :value="department">{{
                            department }}</option>
                        </select>
                        <small class="has-text-danger">{{ errors.first('department') }}</small>
                      </div>

                    </div>

                  </div>
                  <!-- columns -->






                  <div>
                    <!-- <nuxt-link  type="submit" class="button is-fullwidth is-danger " to="/dashboard">Submit</nuxt-link> -->
                    <button @click="registerAdmin" type="button" class="button is-fullwidth is-danger">Submit</button>
                  </div>

                </div>
                <!--box-->

              </form>


            </div>
            <!--column-->
          </div>
          <!--columns-->

        </div>
      </div>
    </section>

  </div>
</template>

<script>
  import axios from 'axios'
  import keys from '~/components/keys.js'
  import loader from '~/components/loader'

  export default {

    components: {
      loader
    },

    created() {
      if (localStorage.getItem('token') !== null) {
        this.$router.push('/dashboard')
      }
    },

    data() {

      return {

        //for validation
        confirm_password: "",
        gender: "",

        username: "",
        password: "",
        firstName: "",
        middleName: "",
        lastName: "",
        address: "",
        department: "",
        position: "",
        dateOfBirth: "",

        registrationLoader: false,


        genderOption: [
          "male",
          "female"
        ],
        departmentOption: [
          "Department of Something",
          "Department of Another"
        ],
        positionOption: [
          "Admin Aid 1",
          "Admin Aid 2"
        ],
        //////////////////////////////////

        warningMsgs: {
          type: 'warn',
          group: 'warning',
          title: `<span ><span class='fas fa-exclamation-triangle fa-2x'></span><span class='title is-5'> Warning:</span></span>`,
          text: `<p class='subtitle is-5'>asdasd</p>`
        },

      } //return
    },

    computed: {


    },

    methods: {

      showNotif(type, title, icon, msg) {
        return {
          type: type,
          group: 'vnotif',
          title: `<span ><span class='fas ${icon} fa-2x'></span><span class='title is-5'> ${title}:</span></span>`,
          text: `<p class='subtitle is-5'>${msg}</p>`
        }
      },

      registerAdmin() {

        this.$validator.validateAll().then(isValidForm => {

          if (isValidForm) {
            let serializeForm = {
              username: this.username,
              password: this.password,
              name: {
                first_name: this.firstName,
                middle_name: this.middleName,
                last_name: this.lastName
              },
              gender: this.gender,
              address: this.address,
              department: this.department,
              position: this.position,
              dateOfBirth: this.dateOfBirth
            }

            const config = {
              method: 'POST',
              url: `${keys.BASE_URL}/api/v1/admins`,
              data: JSON.stringify(serializeForm),
              headers: {
                "Content-Type": "application/json"
              }
            }

            axios(config)
              .then(res => {
                this.registrationLoader = false
                this.$notify(this.showNotif('success', 'Success', 'fa-check-circle', 'Registration Successfull'))
                setTimeout(() => this.$router.push('/'), 1500)
              })
              .catch(err => {
                this.registrationLoader = false
                this.$notify(this.showNotif('error', 'Server Warning', 'fa-exclamation-triangle', err.response.data
                  .errors))
              })
          } 


        })



      }, //createUserWhenImageUpload

    } //methods

  }

</script>

<style scoped>

</style>
