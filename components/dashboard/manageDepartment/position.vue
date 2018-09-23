<template>
  <div><br>



    <div class="modal" :class="{'is-active': this.isAddPositionModalActive}">
      <div class="modal-background" @click="toggleAddPositionModal()" />
      <div class="modal-card">

        <header class="modal-card-head">
          <p class="modal-card-title">Add Position</p>
          <button class="delete" @click="toggleAddPositionModal()"></button>
        </header>


        <section class="modal-card-body">
          <div class="columns">

            <div class="column">
              <div class="field">
                <label class="has-text-weight-semibold">Position:</label>
                <input v-model="addPositionField" class="input is-capitalized is-fullwidth has-background-white-ter"
                  type="text" placeholder="position">
              </div>
            </div>

            <div class="column">

              <div class="field">
                <label class="has-text-weight-semibold">Department:</label>

                <div class="control select is-fullwidth">
                  <select v-model="addDepartmentField">
                    <option disabled value="">Select File department</option>
                    <option :key="department._id" v-for="department in departmentList" :value="department._id">{{
                      department.department}}</option>
                  </select>
                </div>

              </div>

            </div>

          </div>

        </section>

        <footer class="modal-card-foot column">
          <button @click="addPosition()" class="button is-danger  is-pulled-right">Add</button>
        </footer>

      </div>
    </div>


    <div class="modal" :class="{'is-active': this.isUpdatePositionModalActive}">
      <div class="modal-background" @click="toggleUpdatePositionModal()" />
      <div class="modal-card">

        <header class="modal-card-head">
          <p class="modal-card-title">Update Department</p>
          <button class="delete" @click="toggleUpdatePositionModal()"></button>
        </header>


        <section class="modal-card-body">
          <div class="columns">

            <div class="column">
                <label class="has-text-weight-semibold">Position:</label>

              <input v-model="updatePositionField" class="input is-capitalized is-fullwidth has-background-white-ter" type="text"
                placeholder="position">
            </div>

<div class="column">

              <div class="field">
                <label class="has-text-weight-semibold">Department:</label>

                <div class="control select is-fullwidth">
                  <select v-model="updateDepartmentField">
                    <option disabled value="">Select File department</option>
                    <option class="is-capitalized" :key="department._id" v-for="department in departmentList" :value="department._id">{{
                      department.department}}</option>
                  </select>
                </div>

              </div> </div>


          </div>

        </section>


        <footer class="modal-card-foot column">
          <button @click="updatePosition()" class="button is-danger  is-pulled-right">Update</button>
        </footer>


      </div>
    </div>



    <div class="field has-addons has-addons-centered">
      <p class="control is-expanded">
        <input @keypress.enter="searchPosition()" v-model="searchPositionField" class="input " type="text"
          placeholder="Search department...">
      </p>


      <p class="control">
        <!-- <button @click="searchPublicEfile" class="button is-danger is-outlined fa fa-search"></button> -->
        <button @click="searchPosition()" class="button is-danger is-outlined fa fa-search"></button>
      </p>

      <p class="control">
        <button @click="toggleAddPositionModal()" class="button is-danger is-outlined fas fa-plus"></button>
      </p>

    </div>


    <!--table-->
    <div class="table-container">
      <table class="table is-bordered is-striped is-narrow is-hoverable is-fullwidth ">

        <thead>
          <tr class="has-text-centered">
            <th>Positon</th>
            <th>Department</th>

            <th colspan="2" class="has-text-centered">Action</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="position in positionList" :key="position._id">

            <td class="is-capitalized">{{position.position}}</td>
            <td v-if="isDepartmentLoaded" class="is-capitalized">{{ getDepartmentById(position.department) }}</td>

            <td class="has-text-centered">
              <a @click="getPositionInfoToUpdate(position)" class="button is-danger is-outlined">Update</a>
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
                <a class="button is-static"> Page: {{positionPageNo}} </a>
              </p>

              <p class="control">
                <a class="button is-static"> Total: {{positionListTotalPageNo}} </a>
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
      this.getPositionList()
    },
    data() {
      return {

        API_PROFILE: `${keys.BASE_URL}${keys.API_PROFILE}/`,

        //numeric
        positionListTotalPageNo: 0,
        positionListTotalNo: 0,
        positionPageNo: 1,


        isLoaderActive: false,
        isAddPositionModalActive: false,
        isUpdatePositionModalActive: false,

        addPositionField: null,
        addDepartmentField: null,

        updateDepartmentField: null,
        updatePositionField: null,
        searchPositionField: ``,

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
        },
        positionInfoToUpdate: {}


      }
    },

    computed: {

      isDepartmentLoaded() {
        return this.departmentList.length;
      },

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

      getDepartmentById(id) {

        let department = this.departmentList.find(x => x._id === id)

        if (_.isNil(department)) {
          return "NA"
        }
        return `${department.department}`
      },

      getPositionInfoToUpdate(positionInfo) {
        this.positionInfoToUpdate = positionInfo
        this.updateDepartmentField = this.positionInfoToUpdate.department
        this.updatePositionField = this.positionInfoToUpdate.position
        this.toggleUpdatePositionModal()
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
        this.toggleAddPositionModal()
      },

      toggleLoader() {
        this.isLoaderActive = !this.isLoaderActive
      },

      toggleAddPositionModal() {
        this.isAddPositionModalActive = !this.isAddPositionModalActive
      },

      toggleUpdatePositionModal() {
        this.isUpdatePositionModalActive = !this.isUpdatePositionModalActive
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
          url: `${keys.BASE_URL}/api/v1/departments?page=${this.positionPageNo}`,
          headers: {
            "Authorization": `Bearer ${this.$store.state.user_details.token}`
          }
        }


        axios(config)
          .then(res => {
            this.toggleLoader()
            this.departmentList = res.data.docs
          })
          .catch(err => {
            this.toggleLoader()
            this.$notify(this.showNotif('error', 'Server Warning', 'fa-exclamation-triangle', err.response.data.errors))
          })
      },

      getPositionList() {

        this.toggleLoader()
        const config = {
          method: 'GET',
          url: `${keys.BASE_URL}/api/v1/positions?page=${this.positionPageNo}`,
          headers: {
            "Authorization": `Bearer ${this.$store.state.user_details.token}`
          }
        }


        axios(config)
          .then(res => {
            this.toggleLoader()
            this.positionList = res.data.docs
            this.positionListTotalPageNo = res.data.total
            this.positionListTotalNo = res.data.pages
          })
          .catch(err => {
            this.toggleLoader()
            this.$notify(this.showNotif('error', 'Server Warning', 'fa-exclamation-triangle', err.response.data.errors))
          })
      },



      firstPage() {

        this.toggleLoader()



        this.positionPageNo = 1

        const config = {
          method: 'GET',
          url: `${keys.BASE_URL}/api/v1/positions?page=${this.positionPageNo}`,
          headers: {
            "Authorization": `Bearer ${this.$store.state.user_details.token}`
          }
        }

        axios(config)
          .then(res => {
            this.positionList = res.data.docs
            this.toggleLoader()
          })
          .catch(err => {
            this.toggleLoader()
            this.$notify(this.showNotif('warn', 'Warning', 'fa-exclamation-triangle', err))
          })
      },

      lastPage() {
        this.toggleLoader()




        this.positionPageNo = this.positionListTotalNo

        const config = {
          method: 'GET',
          url: `${keys.BASE_URL}/api/v1/positions?page=${this.positionPageNo}`,
          headers: {
            "Authorization": `Bearer ${this.$store.state.user_details.token}`
          }
        }

        axios(config)
          .then(res => {
            this.positionList = res.data.docs
            this.toggleLoader()
          })
          .catch(err => {
            this.toggleLoader()
            this.$notify(this.showNotif('warn', 'Warning', 'fa-exclamation-triangle', err))
          })
      },

      nextPage() {
        this.toggleLoader()



        this.positionPageNo += 1

        const config = {
          method: 'GET',
          url: `${keys.BASE_URL}/api/v1/positions?page=${this.positionPageNo}`,
          headers: {
            "Authorization": `Bearer ${this.$store.state.user_details.token}`
          }
        }

        axios(config)
          .then(res => {

            if ((res.data.docs).length === 0) {
              this.positionPageNo -= 1
              this.$notify(this.showNotif('warn', 'Warning', 'fa-exclamation-triangle', 'no more data to display'))
              this.toggleLoader()
            } else {
            this.positionList = res.data.docs
              this.toggleLoader()
            }

          })
          .catch(err => {
            this.toggleLoader()
            this.$notify(this.showNotif('warn', 'Warning', 'fa-exclamation-triangle', err))
          })
      },

      prevPage() {

        if (this.positionPageNo === 1) {

        } else {
          this.positionPageNo -= 1
          this.toggleLoader()

          const config = {
            method: 'GET',
            url: `${keys.BASE_URL}/api/v1/positions?page=${this.positionPageNo}`,
            headers: {
              "Authorization": `Bearer ${this.$store.state.user_details.token}`
            }
          }

          axios(config)
            .then(res => {
            this.positionList = res.data.docs
              this.toggleLoader()
            })
            .catch(err => {
              this.toggleLoader()
              this.$notify(this.showNotif('warn', 'Warning', 'fa-exclamation-triangle', err))
            })
        }
      },

      searchPosition() {

        this.toggleLoader()
        const config = {
          method: 'GET',
          url: `${keys.BASE_URL}/api/v1/positions/search?position=${this.searchPositionField}`,
          headers: {
            "Authorization": `Bearer ${this.$store.state.user_details.token}`
          }
        }

        axios(config)
          .then(res => {
            this.positionList = res.data.docs
            this.toggleLoader()
          })
          .catch(err => {
            this.toggleLoader()
            this.$notify(this.showNotif('warn', 'Warning', 'fa-exclamation-triangle', err))
          })
      },



      addPosition() {

        if (_.isEmpty(this.addPositionField) || _.isEmpty(this.addDepartmentField)) {

          this.$notify(this.showNotif('warn', 'Warning', 'fa-exclamation-triangle', "Fill out the remaining field"))

        } else {

          this.toggleLoader()
          const config = {
            method: 'POST',
            data: {
              "department": this.addDepartmentField,
              "position": this.addPositionField,
            },
            url: `${keys.BASE_URL}/api/v1/positions`,
            headers: {
              "Authorization": `Bearer ${this.$store.state.user_details.token}`
            }
          }

          axios(config)
            .then(res => {
              this.toggleLoader()
              this.$notify(this.showNotif('success', 'Success', 'fa-check-circle', 'Position successfully added..'))
              this.toggleAddPositionModal()
              this.getPositionList()
              this.addDepartmentField = null
              this.addPositionField = null
              // this.userList = res.data
              // console.log(this.userList);
            })
            .catch(err => {
              this.toggleLoader()
              this.toggleAddPositionModal()
              this.$notify(this.showNotif('error', 'Server Warning', 'fa-exclamation-triangle', err.response.data.errors))
            })

        }

      },

      updatePosition() {
        if (_.isEmpty(this.updatePositionField) && _.isEmpty(this.updateDepartmentField) ) {

          this.$notify(this.showNotif('warn', 'Warning', 'fa-exclamation-triangle', "Fill out the remaining field"))

        } else {

          this.toggleLoader()
          const config = {
            method: 'PUT',
            data: {
              "department": this.updateDepartmentField,
              "position": this.updatePositionField,
              
            },
            url: `${keys.BASE_URL}/api/v1/positions/${this.positionInfoToUpdate._id}`,
            headers: {
              "Authorization": `Bearer ${this.$store.state.user_details.token}`
            }
          }

          axios(config)
            .then(res => {
              this.toggleLoader()
              this.$notify(this.showNotif('success', 'Success', 'fa-check-circle', 'Position successfully added..'))
              this.toggleUpdatePositionModal()
              this.getPositionList()
              this.updateDepartmentField = null
              this.updatePositionField = null
              // this.userList = res.data
              // console.log(this.userList);
            })
            .catch(err => {
              this.toggleLoader()
              this.toggleUpdatePositionModal()
              this.$notify(this.showNotif('error', 'Server Warning', 'fa-exclamation-triangle', err.response.data.errors))
            })

        }

      },




    }
  }

</script>

<style scoped>

</style>
