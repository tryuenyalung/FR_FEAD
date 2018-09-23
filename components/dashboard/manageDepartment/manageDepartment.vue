<template>
  <div>
    <br>
    <div class="tab-padding tabs is-centered is-boxed is-marginless is-paddingless">
      <ul>

        <li @click="changeActiveTab('department')" :class="{'is-active' : activeTab === 'department' }">
          <a>
            <span>Department</span>
          </a>
        </li>

        <li @click="changeActiveTab('position')" :class="{'is-active' : activeTab === 'position' }">
          <a>
            <span>Position</span>
          </a>
        </li>

      </ul>
    </div>
    <!--tabs-->

    <!--tab's content-->
    <department v-if="activeTab === 'department' " />
    <position v-if="activeTab === 'position' " />

  </div>
</template>

<script>
  import loader from '~/components/loader'
  import axios from 'axios'
  import keys from '~/components/keys.js'

  import department from './department'
  import position from './position'

  export default {

    components: {
      department,
      position,
      loader
    }, //components


    data() {

      return {
        activeTab: 'department',
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
