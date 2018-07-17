<template lang='pug'>
  div.info-header
    div.col-md-2.info-logo
      a(href='/')
        img(src='/static/images/utm.png' height='40px')
    div.col-md-4.info-left
      b.input-lg UTM - &nbsp; &nbsp;
      b Universal Touring Machine

      <!-- Demo(:demo="demo" name="patient") -->
      <!-- User(role='patient' :user="patient" title='Patient' :globalSearch="userSearch" :fields="userFields") -->
    div.col-md-6
      <!-- navbar-right items listed from right to left ! -->
      span.navbar-right
        span &nbsp; &nbsp;
        LoginPopup(icon='bars' :payload='payload') &nbsp; &nbsp; 

        <!-- Demo(:demo="demo" name='staff') -->
        <!-- User(role='staff' :user="staff" title='staff' include='staff' :globalSearch="staffSearch" :search="addStaff") -->
</template>

<script>
  import User from '@/components/User'
  import Modal from '@/components/Standard/Modal'
  import LoginPopup from '@/components/Standard/LoginPopup'

  import 'vue-awesome/icons/home'
  import 'vue-awesome/icons/bars'

  export default {
    data () {
      return {
        userFields: ['email', 'name']
      }
    },
    components: {
      User,
      Modal,
      LoginPopup
    },
    props: {
      searchOpen: {
        type: Boolean,
        default: false
      },
      patient: {
        type: Object,
        default () { return {} }
      },
      staff: {
        type: Object,
        default () { return {} }
      },
      demo: {
        type: Boolean
      },
      payload: {
        type: Object
      }
    },
    mounted: function () {
      // this.payload = this.$store.getters.payload
      console.log('mounted in private header: ' + JSON.stringify(this.payload))
    },
    computed: {
    },
    methods: {
      setPatient (data) {
        console.log('set Patient')
        console.log(JSON.stringify(data))

        var keys = Object.keys(data[0])
        for (var i = 0; i < keys.length; i++) {
          this.$set(this.patient, keys[i], data[0][keys[i]])
        }

        console.log(JSON.stringify(this.patient))
      },
      setStaff (data) {
        console.log('set Staff')
        console.log(JSON.stringify(data))

        var keys = Object.keys(data[0])
        for (var i = 0; i < keys.length; i++) {
          this.$set(this.staff, keys[i], data[0][keys[i]])
        }
        console.log(JSON.stringify(this.staff))
      },
      clearPatient () {
        if (this.patient) {
          var keys = Object.keys(this.patient)
          for (var j = 0; j < keys.length; j++) {
            this.$delete(this.patient, keys[j])
          }
        } else { console.log('patient already empty') }
      },
      clearStaff () {
        if (this.staff) {
          var keys = Object.keys(this.staff)
          for (var j = 0; j < keys.length; j++) {
            this.$delete(this.staff, keys[j])
          }
        } else { console.log('staff already empty') }
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.info-header {
  padding: 10px;
  height: 100px;
  color: black;
  background-color: #9f9;
  display: flex;
  justify-content: center;
}
.info-logo {
  text-align: left;
  align-self: center;
}

.info-left {
  text-align: left;
  align-self: center;
}

.info-right {
  text-align: right;
  align-self: center;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

</style>
