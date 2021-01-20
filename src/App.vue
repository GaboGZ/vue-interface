<template>
  <div id="main-app" class="container">
    <h4>{{ title }}</h4>
    <add-appointment @add="addItem" />
    <search-appointments 
      @searchRecords="searchItems" 
      :myKey="filterKey" 
      :myDir="filterDir"/>
    <appointment-list
      v-bind:appointments="filteredApts"
      @remove="removeItem"
      @edit="editItem"
    />
  </div>
</template>

<script>
import axios from "axios";
import _ from "lodash";
import AppointmentList from "./components/AppointmentList";
import AddAppointment from "./components/AddAppointment";
import SearchAppointments from "./components/SearchAppointments.vue";

export default {
  name: "main-app",
  data: function () {
    return {
      title: "Appointment List",
      filterKey: "petName",
      filterDir: "asc",
      appointments: [],
      searchTerms: "",
    };
  },
  components: {
    AppointmentList,
    AddAppointment,
    SearchAppointments,
  },
  mounted() {
    axios.get("./data/appointments.json").then((response) => {
      this.appointments = response.data.map((item) => {
        item.aptId = this.aptIndex;
        this.aptIndex++;
        return item;
      });
    });
  },
  computed: {
    searchedApts: function () {
      return this.appointments.filter((item) => {
        return (
          item.petName.toLowerCase().match(this.searchTerms.toLowerCase()) ||
          item.petOwner.toLowerCase().match(this.searchTerms.toLowerCase()) ||
          item.aptNotes.toLowerCase().match(this.searchTerms.toLowerCase())
        );
      });
    },
    filteredApts: function () {
      return _.orderBy(
        this.searchedApts,
        (item) => {
          return item[this.filterKey].toLowerCase();
        },
        this.filterDir
      );
    },
  },
  methods: {
    addItem: function (apt) {
      apt.aptId = this.aptIndex;
      this.aptIndex++;
      this.appointments.push(apt);
    },
    editItem: function (id, field, text) {
      const aptIndex = _.findIndex(this.appointments, { aptId: id });
      this.appointments[aptIndex][field] = text;
    },
    removeItem: function (apt) {
      this.appointments = _.without(this.appointments, apt);
    },
    searchItems: function (terms) {
      this.searchTerms = terms;
    },
  },
};
</script>