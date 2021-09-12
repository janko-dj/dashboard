<template>
  <v-app id="inspire">
    <v-card dark>
      <v-tabs
        v-model="tab"
      >
        <v-tab  
          v-for="item in items"
          :key="item.serialNumber"
          :id="item"
          @click="getMachineData(item)">
          {{item}}
        </v-tab>
      </v-tabs>
      <v-divider>
        vertical
      </v-divider>
      <v-tabs>
        <v-tab 
          id="tableButton"
          @click="loadTable()"
        >
          Table
        </v-tab>
        <v-tab 
          @click="loadPath(result.serialNumber)"
        >
          Path
        </v-tab>
      </v-tabs>
      <div v-if = "responseOk == true">
        <v-card
        elevation="24"
        class="mx-auto"
        height="100%"
        >
          <div v-if = "openTable == true">
            <v-simple-table>
              <template v-slot:default>
                <thead>
                  <tr>
                    <th class="text-left">
                      Name
                    </th>
                    <th class="text-left">
                      Value
                    </th>
                  </tr>
                </thead>
                <tbody>
                  <tr>
                    <td>Serial number</td>
                    <td>{{ result.serialNumber }}</td>
                  </tr>
                  <tr>
                    <td>Minimum Fuel Consumtion</td>
                    <td>{{ result.minimumFuelConsumption }}</td>
                  </tr>
                  <tr>
                    <td>Average Fuel Consumtion</td>
                    <td>{{ result.averageFuelConsumption }}</td>
                  </tr>
                  <tr>
                    <td>Maxmimum Fuel Consumtion</td>
                    <td>{{ result.maximumFuelConsumption }}</td>
                  </tr>
                  <tr>
                    <td>Minimum Engine Load</td>
                    <td>{{ result.minimumEngineLoad }}</td>
                  </tr>
                  <tr>
                    <td>Average Engine Load</td>
                    <td>{{ result.averageEngineLoad }}</td>
                  </tr>
                  <tr>
                    <td>Maxmimum Engine Load</td>
                    <td>{{ result.maximumEngineLoad }}</td>
                  </tr>
                  <tr>
                    <td>Total Working Hours</td>
                    <td>{{ result.totalWorkingHours }}</td>
                  </tr>
                  <tr>
                    <td>Total Working Hours made that day</td>
                    <td>{{ result.totalWorkingHoursMadeThatDay }}</td>
                  </tr>
                </tbody>
              </template>
            </v-simple-table>
          </div>
          <div v-if = "openCanvas == true">
            <canvas>
            </canvas>
          </div>
        </v-card>
      </div>
    </v-card>
  </v-app>
</template>

<script>
  export default {
    props: {
      items: Array
    },
    data: () => ({ 
      drawer: null,
      responseOk: false,
      result: '',
      activeSerialNumber: '',
      openTable: true,
      openCanvas: false
    }),
    mounted() {
      console.log(this.items[0])
      document.getElementById(this.items[0]).click();
    },
    methods: {
      getMachineData(machineId) {
        console.log("called getMachineData")
        fetch("http://localhost:8999/datastore/claas/" + machineId + "/data", {
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
          this.result = response;
          this.activeSerialNumber = response.serialNumber;
          this.responseOk = true;
          this.openTable=true;
          document.getElementById("tableButton").click();
        })
        .catch(err => {
          console.log(err);
        });
      },
      loadPath(machineId) {
        console.log(machineId)
        this.openTable = false;
        this.openCanvas = true;
      },
      loadTable() {
        this.openTable = true;
        this.openCanvas = false;
      }
    }
  }
</script>