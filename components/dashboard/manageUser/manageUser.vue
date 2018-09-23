<template>
  <div>
    <br>
    <div class="tab-padding tabs is-centered is-boxed is-marginless is-paddingless">
      <ul>

        <li @click="changeActiveTab('active users')" :class="{'is-active' : activeTab === 'active users' }">
          <a>
            <span>Active Users</span>
          </a>
        </li>

        <li @click="changeActiveTab('pending users')" :class="{'is-active' : activeTab === 'pending users' }">
          <a>
            <span>Pending Users</span>
          </a>
        </li>

        <li @click="changeActiveTab('update user info')" :class="{'is-active' : activeTab === 'update user info' }">
          <a>
            <span>Update User Info</span>
          </a>
        </li>

      </ul>
    </div>
    <!--tabs-->

    <!--tab's content-->
    <activeUser v-if="activeTab === 'active users' " />
    <pendingUser v-if="activeTab === 'pending users' " />
    <updateUserInfo v-if="activeTab === 'update user info' " />

  </div>
</template>

<script>
  import loader from '~/components/loader'
  import axios from 'axios'
  import keys from '~/components/keys.js'

  import activeUser from './activeUser'
  import pendingUser from './pendingUser'
  import updateUserInfo from './updateUserInfo'

  export default {

    components: {
      activeUser,
      pendingUser,
      updateUserInfo,
      loader
    }, //components


    data() {

      return {
        activeTab: 'active users',
      } //return

    }, //data

    methods: {
      changeActiveTab(tab) {
        this.activeTab = tab
        // this.getAllNotifs()
      },



    } //methods
  }

</script>

<style scoped>

</style>
