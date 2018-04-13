<template lang='pug'>
  div
    PrivateHeader.header
    div.body.container
      h4 Welcome to the Universal Touring Machine
      hr
      div.col-md-6
        router-link(:to="{name: 'FindUTM'}")
          button
            h3 Find a Tour
      div.col-md-6
        router-link(:to="{name: 'DefineUTM'}")
          button
            h3 Define a Tour
    PublicFooter.footer
</template>

<script>
import DataGrid from './../Standard/DataGrid.vue'

import Modal from './../Standard/Modal.vue'
import Messaging from './../Standard/Messaging.vue'
import RecursiveList from './../Standard/RecursiveList.vue'

import PrivateHeader from './PrivateHeader.vue'
import PublicFooter from './PublicFooter.vue'

import axios from 'axios'
// import config from '@/config.js'

import 'vue-awesome/icons/check-circle'
import 'vue-awesome/icons/warning'
import 'vue-awesome/icons/home'
import 'vue-awesome/icons/edit'
import 'vue-awesome/icons/close'
import 'vue-awesome/icons/question-circle'

export default {
  name: 'ovid',
  components: {
    DataGrid,
    Messaging,
    Modal,
    RecursiveList,
    PrivateHeader,
    PublicFooter
  },
  data () {
    return {
    }
  },
  props: {
  },
  events: {
  },
  created: function () {
    axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded'

    var _this = this
    axios.get(this.tourURL)
    .then(function (result, err) {
      if (err) {
        console.log('set error...')
        _this.$store.commit('setError', {context: 'Searching For ' + _this.scope, err: err})
        console.log('axios error: ' + err)
      } else {
        _this.tours = result.data

        console.log('axios returned value(s): ' + JSON.stringify(_this.tours))
        _this.$store.commit('setHash', {key: 'tours', value: _this.tours})
      }
    })
  },
  computed: {
    storedTours: function () {
      var C = this.$store.getters.getHash('tours') || []
      console.log('load Tours: ' + JSON.stringify(C))
      return C
    }
  },
  methods: {
    tourModal: function (record) {
      console.log('retrieve more info from record: ' + JSON.stringify(record))

      this.$store.dispatch('setModalData', record)
      this.$store.getters.toggleModal('info-modal')
    }
  }
}
</script>

<style>
.page {
  /*margin-top: -20px;*/
  height: 100%;
  width: 100%;
}
.body {
  min-height: calc(100vh - 150px);
}
.header {
  height: 80px;
  background-color: #3a3;
}
.footer {
  height: 70px;
  /*padding: 10px;*/
  background-color: #ccc;
}

  .body {
    height: auto;
  }
  
  .pageWrapper {
    margin: 0px;
  }

  .flexWrapper {
    display: flex;
    flex-flow: row no-wrap;
  }

  .flexChild {
    flex: 1;
    justify-content: space-around;
  }

  .mainBlock {
    padding: 20px;
    align-content: top;
  }

  .user-section, .scheduled-section, .coverage-section {
    width: 80%;
    margin-left: 10%;
    margin-right: 10%;
    margin-top: 40px;
    border: 1px solid black;
    padding: 10px;
  }

  .block {
    padding: 0px;
    /*border: 1px solid black;*/
    min-height: 200px;
  }
  .block-header {
    padding: 10px;
    background-color: #ccc;
  }
  .block-subheader {
    padding: 10px;
    background-color: #ddd;
  }
  .block-body {
    padding: 20px;
    background-color: #eee;
  }
  .block-footer {
    padding: 20px;
    background-color: '#eee';
  }
  .coverageBlock {
    border: solid black 3px;
    padding: 20px;
    min-width: 200px;
    max-width: 400px;
  }
  .mainBlock {
    border-top: solid black 3px;
    border-right: solid black 3px;
    padding: 20px;
    transition: opacity 1s ease;
  }
  .mainSection {
    transition: visibility 0s linear 0s, opacity 1000ms;
  }

  .footer {
    padding-left: 40px;
    padding-right: 40px;
    padding-top: 15px;
    padding-bottom: 15px;
  }
  .logo-s {
    max-height: 70px;
    margin-top: -15px;
    padding-right: 50px;
  }

</style>
