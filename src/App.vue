<template>
  <v-app>
    <div v-if = "responseOk == true">
      <dashboard-workspace :items=items></dashboard-workspace>
    </div>
    <v-main>
      <!--  -->
    </v-main>
  </v-app>
</template>

<script>
import DashboardWorkspace from './components/DashboardWorkspace.vue';

export default {
  components: { DashboardWorkspace },
    data: () => ({ 
      items: [],
      responseOk: false,
      drawer: null }),
    created() {
      this.responseOk=false
      fetch("http://localhost:8999/datastore/all-machine-ids", {
        "method": "GET",
      })
      .then(response => {
        if(response.ok){
            return response.json()
        } else{
            alert("Server returned " + response.status + " : " + response.statusText);
        }
      })
      .then(response => {
        this.items = response.machineIdsList; 
        this.responseOk = true;
      })
      .catch(err => {
        console.log(err);
      });
    }
  }
</script>