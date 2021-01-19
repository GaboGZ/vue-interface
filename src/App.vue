<template>
  <div id="main-app" class="container">
    <h4>{{ title }}</h4>
    <span class="btn bg-primary text-white my-2">&plus; Add Appointment</span>
    <appointment-list v-bind:appointments="appointments" @remove="removeItem"></appointment-list>
  </div>
</template>

<script>
import axios from "axios";
import _ from "lodash";
import AppointmentList from "./components/AppointmentList";

export default {
  name: "main-app",
  data: function () {
    return {
      title: "Appointment List",
      appointments: [],
    };
  },
  components: {
    AppointmentList
  },
  mounted(){
    axios.get("./data/appointments.json")
    .then(response => {
      this.appointments = response.data;
    })
  },
  methods: {
    removeItem: function(apt){
      this.appointments = _.without(this.appointments, apt);
    }
  }
}
</script>