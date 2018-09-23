<template>
  <div><br>


    <div class="modal" :class="{'is-active': this.isUserProfileModalActive}">
      <div class="modal-background" @click="this.toggleUserProfileModalActive" />
      <div class="modal-card">

        <header class="modal-card-head">
          <p class="modal-card-title">User Profile</p>
          <button class="delete" @click="this.toggleUserProfileModalActive"></button>
        </header>


        <section class="modal-card-body">
          <div class="columns">

            <div class="column is-4">

              <figure :class="'avatar has-text-centered'">
                <img v-if="this.userInfoToView.avatar === null" :src="`https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQF1zJt89f-BE-BEriCrVlTfXhOKdTnVzo-CdfbL4S9aC4gzA03`">
                <img v-else :src="`${API_PROFILE}${this.userInfoToView.avatar}`">
              </figure>



            </div>

            <div class="column is-8">
              <div class="columns">
                <div class="column">
                  Username: {{this.userInfoToView.username}}
                </div>

                <div class="column">
                  Name: {{this.userInfoToView.name}}
                </div>
              </div>

              <div class="columns">
                <div class="column">
                  Sex: {{this.userInfoToView.sex}}
                </div>

                <div class="column">
                  Address: {{this.userInfoToView.address}}
                </div>
              </div>

              <div class="columns">
                <div class="column">

                  Department : <p class="is-capitalized" v-if="isDepartmentLoaded">{{
                    getDepartmentById(this.userInfoToView.department) }}</p>
                </div>

                <div class="column">

                  Position: <p class="is-capitalized" v-if="isPositionLoaded">{{getPositionById(this.userInfoToView.position)}}</p>
                </div>
              </div>

              <div class="columns">
                <div class="column">
                  Registration Date : {{this.userInfoToView.regDate | dateTimeFormatter}}
                </div>


              </div>
            </div>

          </div>


        </section>


        <footer class="modal-card-foot">

        </footer>


      </div>
    </div>







    <div class="field has-addons has-addons-centered">
      <p class="control is-expanded">
        <!-- <input @keypress.enter="searchPublicEfile" v-model="privateEfileSearchInput" class="input " type="text"
          placeholder="Search efile..."> -->

        <input class="input " type="text" placeholder="Search efile...">
      </p>

      <p class="control">
        <span class="select">

          <!-- <select v-model="privateEfileSearchBy">
            <option>Name</option>
            <option>Sender</option>
            <option>Recipients</option>
            <option>Date Created</option>
          </select> -->

          <select>
            <option>Username</option>
            <option>Name</option>
            <option>Department</option>
            <option>Position</option>
            <option>Registration Date</option>
          </select>
        </span>
      </p>

      <p class="control">
        <!-- <button @click="searchPublicEfile" class="button is-danger is-outlined fa fa-search"></button> -->
        <button class="button is-danger is-outlined fa fa-search"></button>
      </p>
    </div>

    <!--table-->
    <div class="table-container">
      <table class="table is-bordered is-striped is-narrow is-hoverable is-fullwidth ">

        <thead>
          <tr class="has-text-centered">
            <th>Name</th>
            <th>Department</th>
            <th>Position</th>
            <th>Registration Date</th>
            <th colspan="2" class="has-text-centered">Action</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="user in userList" :key="user._id">

            <td>{{user.name.first_name}} {{user.name.middle_name}} {{user.name.last_name}}</td>
            <td class="is-capitalized" v-if="isDepartmentLoaded">{{ getDepartmentById(user.department) }}</td>
            <td class="is-capitalized" v-if="isPositionLoaded">{{ getPositionById(user.position) }}</td>
            <td>{{user.created_at | dateTimeFormatter}}</td>

            <td class="has-text-centered">
              <a @click="getUserInfoToView(user)" class="button is-danger is-outlined">View</a>
            </td>
            <td class="has-text-centered">
              <a class="button is-danger is-outlined">Deactivate User</a>
            </td>
          </tr>
        </tbody>

        <!-- {{this.privateEfileList}} -->
      </table>


      <div class="box">

        <div class="level">

          <div class="level-item">
            <div class="field has-addons">
              <p class="control">
                <!-- <a class="button" @click="firstPage"> First </a> -->
                <a class="button"> First </a>
              </p>

              <p class="control">
                <!-- <a class="button" @click="lastPage"> Last </a> -->
                <a class="button"> Last </a>
              </p>
            </div>
          </div>


          <div class="level-item">
            <div class="field has-addons">
              <p class="control">
                <!-- <a class="button is-static"> Page: {{privateEfileList.page}} </a> -->
                <a class="button is-static"> Page: 1 </a>
              </p>

              <p class="control">
                <!-- <a class="button is-static"> Total: {{privateEfileList.total}} </a> -->
                <a class="button is-static"> Total: 1 </a>
              </p>
            </div>
          </div>


          <div class="level-item">
            <div class="field has-addons">
              <p class="control">
                <!-- <a class="button" @click="prevPage"> Previous </a> -->
                <a class="button"> Previous </a>
              </p>

              <p class="control">
                <!-- <a class="button" @click="nextPage"> Next </a> -->
                <a class="button"> Next </a>
              </p>
            </div>
          </div>

        </div>

      </div>

    </div>
    <!--table-container-->

    <loader :isActive="this.isLoaderActive" />













  </div>
</template>

<script>
  import _ from "lodash"
  import loader from '~/components/loader'
  import axios from 'axios'
  import keys from '~/components/keys.js'
  import moment from 'moment'

  export default {

    components: {
      loader
    },

    filters: {

      dateTimeFormatter: function (value) {
        if (value)
          return moment(String(value)).format('MMM Do YY')
      }

    },


    mounted() {
      this.getListOfUsers()
      this.getPositionList()
      this.getDepartmentList()
    },

    computed: {
      isDepartmentLoaded() {
        return this.departmentList.length;
      },

      isPositionLoaded() {
        return this.positionList.length;
      },
    },



    data() {
      return {

        API_PROFILE: `${keys.BASE_URL}${keys.API_PROFILE}/`,

        isLoaderActive: false,
        isUserProfileModalActive: false,

        userList: [],
        departmentList: [],
        positionList: [],

        userInfoToView: {
          avatar: `null`,
          username: ``,
          name: ``,
          sex: ``,
          address: ``,
          department: ``,
          position: ``,
          regDate: ``
        }


      }
    },

    methods: {

      getDepartmentById(id) {

        let department = this.departmentList.find(x => x._id === id)

        if (_.isNil(department)) {
          return "NA"
        }
        return `${department.department}`
      },

      getPositionById(id) {

        let position = this.positionList.find(x => x._id === id)

        if (_.isNil(position)) {
          return "NA"
        }
        return `${position.position}`
      },


      getPositionList() {

        const config = {
          method: 'GET',
          url: `${keys.BASE_URL}/api/v1/positions`
        }


        axios(config)
          .then(res => {
            this.positionList = res.data
          })
          .catch(err => {
            this.toggleLoader()
            this.$notify(this.showNotif('error', 'Server Warning', 'fa-exclamation-triangle', err.response.data.errors))
          })
      },

      getDepartmentList() {
        const config = {
          method: 'GET',
          url: `${keys.BASE_URL}/api/v1/departments`
        }

        axios(config)
          .then(res => {
            this.departmentList = res.data
          })
          .catch(err => {
            this.toggleLoader()
            this.$notify(this.showNotif('error', 'Server Warning', 'fa-exclamation-triangle', err.response.data.errors))
          })
      },

      getUserInfoToView(userInfo) {

        if (userInfo.avatar !== null) {
          userInfo.avatar = `${userInfo.avatar}?${Date.now()}`
        }

        this.userInfoToView = {
          avatar: userInfo.avatar,
          username: userInfo.username,
          name: `${userInfo.name.first_name} ${userInfo.name.middle_name} ${userInfo.name.last_name}`,
          sex: userInfo.gender,
          address: userInfo.address,
          department: userInfo.department,
          position: userInfo.position,
          regDate: userInfo.created_at
        }
        this.toggleUserProfileModalActive()
      },

      toggleLoader() {
        this.isLoaderActive = !this.isLoaderActive
      },

      toggleUserProfileModalActive() {
        this.isUserProfileModalActive = !this.isUserProfileModalActive
      },


      getListOfUsers() {
        this.toggleLoader()
        const config = {
          method: 'GET',
          url: `${keys.BASE_URL}/api/v1/users/search?status=y`,
          headers: {
            "Authorization": `Bearer ${this.$store.state.user_details.token}`
          }
        }

        axios(config)
          .then(res => {
            this.toggleLoader()
            this.userList = res.data
          })
          .catch(err => {
            this.toggleLoader()
            this.$notify(this.showNotif('error', 'Server Warning', 'fa-exclamation-triangle', err.response.data.errors))
          })
      },

    }
  }

</script>

<style scoped>

</style>
