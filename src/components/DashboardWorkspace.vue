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
                    <td>{{ result.minimumFuelConsumption }} l/h</td>
                  </tr>
                  <tr>
                    <td>Average Fuel Consumtion</td>
                    <td>{{ result.averageFuelConsumption }} l/h</td>
                  </tr>
                  <tr>
                    <td>Maxmimum Fuel Consumtion</td>
                    <td>{{ result.maximumFuelConsumption }} l/h</td>
                  </tr>
                  <tr>
                    <td>Minimum Engine Load</td>
                    <td>{{ result.minimumEngineLoad }} %</td>
                  </tr>
                  <tr>
                    <td>Average Engine Load</td>
                    <td>{{ result.averageEngineLoad }} %</td>
                  </tr>
                  <tr>
                    <td>Maxmimum Engine Load</td>
                    <td>{{ result.maximumEngineLoad }} %</td>
                  </tr>
                  <tr>
                    <td>Minimum Engine Speed</td>
                    <td>{{ result.minimumEngineSpeed }} rpm</td>
                  </tr>
                  <tr>
                    <td>Average Engine Speed</td>
                    <td>{{ result.averageEngineSpeed }} rpm</td>
                  </tr>
                  <tr>
                    <td>Maxmimum Engine Speed</td>
                    <td>{{ result.maximumEngineSpeed }} rpm</td>
                  </tr>
                  <tr>
                    <td>Minimum Front PTO Speed</td>
                    <td>{{ result.minimumFrontPTOSpeed }} rpm</td>
                  </tr>
                  <tr>
                    <td>Average Front PTO Speed</td>
                    <td>{{ result.averageFrontPTOSpeed }} rpm</td>
                  </tr>
                  <tr>
                    <td>Maxmimum Front PTO Speed</td>
                    <td>{{ result.maximumFrontPTOSpeed }} rpm</td>
                  </tr>
                  <tr>
                    <td>Minimum Rear PTO Speed</td>
                    <td>{{ result.minimumRearPTOSpeed }} rpm</td>
                  </tr>
                  <tr>
                    <td>Average Rear PTO Speed</td>
                    <td>{{ result.averageRearPTOSpeed }} rpm</td>
                  </tr>
                  <tr>
                    <td>Maxmimum Rear PTO Speed</td>
                    <td>{{ result.maximumRearPTOSpeed }} rpm</td>
                  </tr>
                  <tr>
                    <td>Total Working Hours</td>
                    <td>{{ result.totalWorkingHours }} h</td>
                  </tr>
                  <tr>
                    <td>Total Working Hours made that day</td>
                    <td>{{ result.totalWorkingHoursMadeThatDay }} h</td>
                  </tr>
                </tbody>
              </template>
            </v-simple-table>
          </div>
          <div v-if = "openImgWithPath == true">
            <gmap-map
              :center="center"
              :zoom="13"
              map-type-id="satellite"
              style="width: 100%; height:655px"
            >
              <gmap-polyline v-bind:path.sync="path" v-bind:options="{ strokeColor:'#800080'}">
              </gmap-polyline>
            </gmap-map>
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
      openImgWithPath: false,
      center: {lat: 0, lng: 0},
      path: []
    }),
    mounted() {
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
              return response.json();
          } else {
              alert("Server returned " + response.status + " : " + response.statusText);
          }
        })
        .then(response => {
          this.result = response;
          this.center.lat = response.centerLatitude;
          this.center.lng = response.centerLongitude;
          this.activeSerialNumber = response.serialNumber;
          this.responseOk = true;
          this.openTable=true;
          document.getElementById("tableButton").click();
        })
        .catch(err => {
          console.log(err);
        });
      },
      async loadPath(machineId) {
        fetch("http://localhost:8999/datastore/" + machineId + "/coordinates", {
            "method": "GET"
          }).then(response => {
            if(response.ok){
              return response.json();
            } else {
              alert("Server returned " + response.status + " : " + response.statusText);
            }
          }).then(response => {
            this.path = response.gpsCoordinatesList;
          }).catch(err => {
            console.log(err);
          })
        console.log(machineId);
        this.openTable = false;
        this.openImgWithPath = true;
      },
      loadTable() {
        this.openTable = true;
        this.openImgWithPath = false;
      }
    }
  }
</script>