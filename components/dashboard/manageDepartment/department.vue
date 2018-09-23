<template>
  <div><br>



    <div class="modal" :class="{'is-active': this.isAddDepartmentModalActive}">
      <div class="modal-background" @click="this.toogleAddDepartmentModal" />
      <div class="modal-card">

        <header class="modal-card-head">
          <p class="modal-card-title">Add Deparment</p>
          <button class="delete" @click="this.toogleAddDepartmentModal"></button>
        </header>


        <section class="modal-card-body">
          <div class="columns">

            <div class="column">
              <input v-model="addDepartmentField" class="input is-capitalized is-fullwidth has-background-white-ter" type="text"
                placeholder="department">
            </div>


          </div>

        </section>


        <footer class="modal-card-foot column">
          <button @click="addDepartment()" class="button is-danger  is-pulled-right">Add</button>
        </footer>


      </div>
    </div>


    <div class="modal" :class="{'is-active': this.isUpdateDepartmentModalActive}">
      <div class="modal-background" @click="toogleUpdateDepartmentModal()" />
      <div class="modal-card">

        <header class="modal-card-head">
          <p class="modal-card-title">Update Department</p>
          <button class="delete" @click="toogleUpdateDepartmentModal()"></button>
        </header>


        <section class="modal-card-body">
          <div class="columns">

            <div class="column">
              <input v-model="updateDepartmentField" class="input  is-capitalized is-fullwidth has-background-white-ter" type="text"
                placeholder="department">
            </div>


          </div>

        </section>


        <footer class="modal-card-foot column">
          <button @click="updateDepartment()" class="button is-danger  is-pulled-right">Update</button>
        </footer>


      </div>
    </div>



    <div class="field has-addons has-addons-centered">
      <p class="control is-expanded">
        <input @keypress.enter="searchDepartment()" v-model="searchDepartmentField" class="input " type="text"
          placeholder="Search department...">
      </p>


      <p class="control">
        <!-- <button @click="searchPublicEfile" class="button is-danger is-outlined fa fa-search"></button> -->
        <button @click="searchDepartment()" class="button is-danger is-outlined fa fa-search"></button>
      </p>

      <p class="control">
        <button @click="toogleAddDepartmentModal()" class="button is-danger is-outlined fas fa-plus"></button>
      </p>

    </div>


    <!--table-->
    <div class="table-container">
      <table class="table is-bordered is-striped is-narrow is-hoverable is-fullwidth ">

        <thead>
          <tr class="has-text-centered">
            <th>Department</th>

            <th colspan="2" class="has-text-centered">Action</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="department in departmentList" :key="department._id">

            <td class="is-capitalized">{{department.department}}</td>

            <td class="has-text-centered">
              <a @click="getDepartmentInfoToUpdate(department)" class="button is-danger is-outlined">Update</a>
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
                <a class="button" @click="firstPage"> First </a>
              </p>

              <p class="control">
                <a class="button" @click="lastPage"> Last </a>
              </p>
            </div>
          </div>


          <div class="level-item">
            <div class="field has-addons">
              <p class="control">
                <a class="button is-static"> Page: {{deparmentPageNo}} </a>
              </p>

              <p class="control">
                <a class="button is-static"> Total: {{deparmentListTotalPageNo}} </a>
              </p>
            </div>
          </div>


          <div class="level-item">
            <div class="field has-addons">
              <p class="control">
                <a class="button" @click="prevPage"> Previous </a>
              </p>

              <p class="control">
                <a class="button" @click="nextPage"> Next </a>
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
      //   this.getListOfUsers()
      this.getDepartmentList()
    },
    data() {
      return {

        API_PROFILE: `${keys.BASE_URL}${keys.API_PROFILE}/`,

        //numeric
        deparmentListTotalPageNo: 0,
        deparmentListTotalNo: 0,
        deparmentPageNo: 1,


        isLoaderActive: false,
        isAddDepartmentModalActive: false,
        isUpdateDepartmentModalActive: false,
        addDepartmentField: null,
        updateDepartmentField: null,
        searchDepartmentField: ``,

        userList: [],
        departmentList: [],

        userInfoToView: {
          avatar: `null`,
          username: ``,
          name: ``,
          sex: ``,
          address: ``,
          department: ``,
          position: ``,
          regDate: ``
        },
        departmentInfoToUpdate: {}


      }
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

      getDepartmentInfoToUpdate(departmentInfo) {
        this.departmentInfoToUpdate = departmentInfo
        this.updateDepartmentField = this.departmentInfoToUpdate.department
        this.toogleUpdateDepartmentModal()
      },

      getUserInfoToView(userInfo) {
        console.log(userInfo.avatar);

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
        this.toogleAddDepartmentModal()
      },

      toggleLoader() {
        this.isLoaderActive = !this.isLoaderActive
      },

      toogleAddDepartmentModal() {
        this.isAddDepartmentModalActive = !this.isAddDepartmentModalActive
      },

      toogleUpdateDepartmentModal() {
        this.isUpdateDepartmentModalActive = !this.isUpdateDepartmentModalActive
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
            console.log(this.userList);
          })
          .catch(err => {
            this.toggleLoader()
            this.$notify(this.showNotif('error', 'Server Warning', 'fa-exclamation-triangle', err.response.data.errors))
          })
      },

      getDepartmentList() {
        this.toggleLoader()
        const config = {
          method: 'GET',
          url: `${keys.BASE_URL}/api/v1/departments?page=${this.deparmentPageNo}`,
          headers: {
            "Authorization": `Bearer ${this.$store.state.user_details.token}`
          }
        }


        axios(config)
          .then(res => {
            this.toggleLoader()
            this.departmentList = res.data.docs
            this.deparmentListTotalPageNo = res.data.total 
            this.deparmentListTotalNo = res.data.pages
          })
          .catch(err => {
            this.toggleLoader()
            this.$notify(this.showNotif('error', 'Server Warning', 'fa-exclamation-triangle', err.response.data.errors))
          })
      },


      firstPage() {

        this.toggleLoader()



        this.deparmentPageNo = 1

        const config = {
          method: 'GET',
          url: `${keys.BASE_URL}/api/v1/departments?page=${this.deparmentPageNo}`,
          headers: {
            "Authorization": `Bearer ${this.$store.state.user_details.token}`
          }
        }

        axios(config)
          .then(res => {
            this.departmentList = res.data.docs
            this.toggleLoader()
          })
          .catch(err => {
            this.toggleLoader()
            this.$notify(this.showNotif('warn', 'Warning', 'fa-exclamation-triangle', err))
          })
      },

      lastPage() {
        this.toggleLoader()




        this.deparmentPageNo = this.deparmentListTotalNo

        const config = {
          method: 'GET',
          url: `${keys.BASE_URL}/api/v1/departments?page=${this.deparmentPageNo}`,
          headers: {
            "Authorization": `Bearer ${this.$store.state.user_details.token}`
          }
        }

        axios(config)
          .then(res => {
            this.departmentList = res.data.docs
            this.toggleLoader()
          })
          .catch(err => {
            this.toggleLoader()
            this.$notify(this.showNotif('warn', 'Warning', 'fa-exclamation-triangle', err))
          })
      },

      nextPage() {
        this.toggleLoader()



        this.deparmentPageNo += 1

        const config = {
          method: 'GET',
          url: `${keys.BASE_URL}/api/v1/departments?page=${this.deparmentPageNo}`,
          headers: {
            "Authorization": `Bearer ${this.$store.state.user_details.token}`
          }
        }

        axios(config)
          .then(res => {

            if ((res.data.docs).length === 0) {
              this.deparmentPageNo -= 1
              this.$notify(this.showNotif('warn', 'Warning', 'fa-exclamation-triangle', 'no more data to display'))
              this.toggleLoader()
            } else {
              this.departmentList = res.data.docs

              this.toggleLoader()
            }

          })
          .catch(err => {
            this.toggleLoader()
            this.$notify(this.showNotif('warn', 'Warning', 'fa-exclamation-triangle', err))
          })
      },

      prevPage() {

        if (this.deparmentPageNo === 1) {

        } else {
          this.deparmentPageNo -= 1
          this.toggleLoader()

          const config = {
            method: 'GET',
            url: `${keys.BASE_URL}/api/v1/departments?page=${this.deparmentPageNo}&tag=${this.fileSearchInput}`,
            headers: {
              "Authorization": `Bearer ${this.$store.state.user_details.token}`
            }
          }

          axios(config)
            .then(res => {
              this.departmentList = res.data.docs

              this.toggleLoader()
            })
            .catch(err => {
              this.toggleLoader()
              this.$notify(this.showNotif('warn', 'Warning', 'fa-exclamation-triangle', err))
            })
        }
      },

      searchDepartment() {
        this.toggleLoader()
        const config = {
          method: 'GET',
          url: `${keys.BASE_URL}/api/v1/departments/search?department=${this.searchDepartmentField}`,
          headers: {
            "Authorization": `Bearer ${this.$store.state.user_details.token}`
          }
        }

        axios(config)
          .then(res => {
            this.departmentList = res.data.docs

            this.toggleLoader()
          })
          .catch(err => {
            this.toggleLoader()
            this.$notify(this.showNotif('warn', 'Warning', 'fa-exclamation-triangle', err))
          })
      },



      addDepartment() {

        if (_.isEmpty(this.addDepartmentField)) {

          this.$notify(this.showNotif('warn', 'Warning', 'fa-exclamation-triangle', "Fill out the remaining field"))

        } else {

          this.toggleLoader()
          const config = {
            method: 'POST',
            data: {
              "department": this.addDepartmentField
            },
            url: `${keys.BASE_URL}/api/v1/departments`,
            headers: {
              "Authorization": `Bearer ${this.$store.state.user_details.token}`
            }
          }

          axios(config)
            .then(res => {
              this.toggleLoader()
              this.$notify(this.showNotif('success', 'Success', 'fa-check-circle', 'Department successfully added..'))
              this.toogleAddDepartmentModal()
              this.getDepartmentList()
              this.addDepartmentField = ``
              // this.userList = res.data
              // console.log(this.userList);
            })
            .catch(err => {
              this.toggleLoader()
              this.toogleAddDepartmentModal()
              this.$notify(this.showNotif('error', 'Server Warning', 'fa-exclamation-triangle', err.response.data.errors))
            })

        }

      },

      updateDepartment() {
        if (_.isEmpty(this.updateDepartmentField)) {

          this.$notify(this.showNotif('warn', 'Warning', 'fa-exclamation-triangle', "Fill out the remaining field"))

        } else {

          this.toggleLoader()
          const config = {
            method: 'PUT',
            data: {
              "department": this.updateDepartmentField
            },
            url: `${keys.BASE_URL}/api/v1/departments/${this.departmentInfoToUpdate._id}`,
            headers: {
              "Authorization": `Bearer ${this.$store.state.user_details.token}`
            }
          }

          axios(config)
            .then(res => {
              this.toggleLoader()
              this.$notify(this.showNotif('success', 'Success', 'fa-check-circle', 'Department successfully added..'))
              this.toogleUpdateDepartmentModal()
              this.getDepartmentList()
              this.updateDepartmentField = ``
              // this.userList = res.data
              // console.log(this.userList);
            })
            .catch(err => {
              this.toggleLoader()
              this.toogleUpdateDepartmentModal()
              this.$notify(this.showNotif('error', 'Server Warning', 'fa-exclamation-triangle', err.response.data.errors))
            })

        }

      },




    }
  }

</script>

<style scoped>

</style>
