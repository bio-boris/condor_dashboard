<template>
  <div id="app">
    <h1>Jobs</h1>
    <!-- {{jobs_data}} -->
    <h1>Condor Queue Monitoring - Machines</h1>
    Records fetched: {{ record_count }} - Size of records fetched:
    {{ convert_bytes_to_kb }} kb - Current size in memory {{ Math.floor(calculate_record_size(machine_data) / 1024) }} kb
    <section v-if="errored">
      <p>
        We're sorry, we're not able to retrieve this information at the moment,
        please try back later
      </p>
    </section>

    <section v-else>
      <div v-if="loading">Loading...</div>

      <div v-else v-for="machine in machine_data" v-bind:key="machine.name">
        <br />
        <Machine v-bind:machine="machine"></Machine>
      </div>
    </section>
  </div>
</template>

<script>
var DAEMONMASTER = "DaemonMaster";
var MACHINE = "Machine";

function captureDaemonMasters(data) {
  // console.log("GROUP BY MACHINE");
  const dms = {};
  data.forEach(classad => {
    if (classad.type === DAEMONMASTER) {
      let name = classad["name"];
      let machine = classad["classad"]["machine"];
      dms[machine] = {
        classad: classad.classad,
        slots: [],
        name: name
      };
    }
  });
  return dms;
}

function addSlotsToDaemonMasters(dms, data) {
  // console.log(Object.keys(dms));
  data.forEach(classad => {
    // let name = classad["name"];
    if (classad.type === MACHINE) {
      classad["uniq"] = classad.name;
      let machine = classad["classad"]["machine"];
      dms[machine]["slots"].push(classad);
    }
  });
  data = null; //Why unset it here?
  return dms;
}

function group_by_machine(data) {
  let dms = captureDaemonMasters(data);
  addSlotsToDaemonMasters(dms, data);
  return dms;
}

// function group_by_partionable_slot(data){
//   return data;
// }

import axios from "axios";
import { sizeof } from "./sizeof.js";
import Machine from "./Machine.vue";

export default {
  name: "CondorMachineDashboard",
  components: {
    Machine
  },
  props: {
    msg: String
  },
  data() {
    return {
      machine_data: {},
      jobs_data: {},
      loading: true,
      jobs_loading: true,
      errored: false,
      jobs_errored: false,
      record_count: 0,
      record_size: 0
    };
  },
  computed: {
    convert_bytes_to_kb: function() {
      return Math.floor(this.record_size / 1024);
    },
    convert_bytes_to_kb2: function(data) {
      return Math.floor(data / 1024);
    }
  },
  methods: {
    getCondorStatusData: function() {
      axios
        .get("http://localhost:9680/v1/status")
        .then(response => {
          this.machine_data = group_by_machine(response.data);
          this.errored = false;
        })
        .catch(error => {
          console.log(error);
          this.errored = true;
        })
        .finally(() => (this.loading = false));
    },
    getCondorJobsData: function() {
      axios
        .get(
          "http://localhost:9680/v1/grouped_jobs/kbase@ci-dock/lastremotehost"
        )
        .then(response => {
          this.jobs_data = response.data;
          this.jobs_errored = false;
        })
        .catch(error => {
          console.log(error);
          this.jobs_errored = true;
        })
        .finally(() => (this.jobs_loading = false));
    },
    calculate_record_size: function(data) {
      return sizeof(data);
    },
    updateMachineJobsData: function() {
      // let machines = Object.keys();
      console.log("Update Machine");
      console.log(this.jobs_data);
      for (let key in this.jobs_data) {
        let job_machine_name = key.split("@")[1];
        let jobs = this.jobs_data[key]; //Array of jobs
        console.log(this.machine_data);
        // Should probably redo the data structure to get it by keys
        for (let machine_name in this.machine_data) {
          console.log(job_machine_name);
          console.log(machine_name);
        }
        this.machine_data[job_machine_name]["jobs"] = jobs;
      }
    },
    intervalFetchData: function() {
      setInterval(() => {
        this.getAllData();
        this.record_count += Object.keys(this.machine_data).length;
        this.record_size += this.calculate_record_size(this.machine_data);
      }, 10000);
    },
    getAllData() {
      this.getCondorStatusData();
      // this.getCondorJobsData();
      // this.updateMachineJobsData();
    }
  },

  mounted() {
    this.getAllData();
    this.intervalFetchData();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
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
