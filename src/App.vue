<template>
  <v-app :dark="darkMode">
    <v-toolbar color="light-blue">
      <v-toolbar-title class="headline">
        Thunder
      </v-toolbar-title>

      <v-spacer></v-spacer>

       <v-btn icon @click="sheet=!sheet">
         <v-icon>settings</v-icon>
       </v-btn>
    </v-toolbar>
    <v-expand-transition>
     <div v-if="(distance < 1000) && state == 'finished'">
       <v-alert type="warning" value="true" color="light-blue darken-3" class="mt-0">
             You are less than 10km away from the lightning. Please make sure to take cover.
      </v-alert>
     </div>
   </v-expand-transition>
    <v-content>
      <v-container fluid>
        <v-layout align-center justify-center column>
          <v-flex>
              <v-progress-circular
              :size="250"
              :width="5"
              color="light-blue"
              :indeterminate="state == 'on'"
              @click="toggle"
              >
                <h1 class="display-2 font-weight-bold">{{ distance }}<span class="subheading">{{measurement}}</span></h1>
              </v-progress-circular>
                <h2 class="body-2 light-blue--text mt-5 text-xs-center">{{elapsed}}s</h2>
          </v-flex>
        </v-layout>
</v-container>
<!--<v-data-table
   :items="history"
   class="elevation-1 hidden"
   hide-actions
   header-text="['distance','time']"
 >
   <template slot="items" slot-scope="props">
     <td>{{ props.item.distance }}</td>
     <td class="text-xs-right">{{ props.item.timestamp }}</td>
   </template>
 </v-data-table>
-->
    </v-content>
    <v-bottom-sheet v-model="sheet" class="red">
      <v-card tile color="blue-grey darken-4">
        <v-container>
        <v-layout column>
          <v-flex>
            <h1>Settings</h1>
            <v-subheader class="pl-0">Measure distance in</v-subheader>

            <v-btn-toggle v-model="measurement" mandatory>
              <v-btn large flat value="m" class="light-blue">
                <span>Metres</span>
              </v-btn>
              <v-btn large flat value="mi" class="light-blue">
                <span>MILES</span>
              </v-btn>
            </v-btn-toggle>
          </v-flex>
          <v-flex>
            <v-subheader class="pl-0">
               Reaction time in miliseconds
            </v-subheader>
            <v-slider
               v-model="reactionSpeed"
               thumb-label
                min="0"
                max="400"
             ></v-slider>
          </v-flex>
          <v-flex>
            <v-subheader class="pl-0">
               Reaction time in miliseconds
            </v-subheader>
            <v-slider
               v-model="reactionSpeed"
               thumb-label
                min="0"
                max="400"
             ></v-slider>
          </v-flex>
        </v-layout>
</v-container>
      </v-card>
     </v-bottom-sheet>
  </v-app>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      sheet: false,
      state: 'off',
      startTime: 0,
      currentTime: 0,
      elapsed: 0.000,
      measurement: 'm',
      speedOfLight: 299792458, // in m/s
      speedOfSound: 343, // in m/s
      reactionSpeed: 100, // in ms
      darkMode: true,
      history: [],
    };
  },
  computed: {
    // a computed getter
    distance() {
      if (this.elapsed < 0.01) {
        return 0;
      }
      return ((parseFloat(this.elapsed) + (this.reactionSpeed / 1000)) * 340).toFixed(0);
    },
  },
  methods: {
    toggle() {
      if (this.state == 'off') {
        this.start();
      } else if (this.state == 'on') {
        this.stop();
      } else {
        this.reset();
      }
    },
    start() {
      if (this.state !== 'on') {
        this.state = 'on';
        this.startTime = new Date().getTime();
        this.interval = window.setInterval(this.countTime, 10);
      }
    },
    stop() {
      this.state = 'finished';
      const timestamp = new Date();
      this.history.push({ distance: this.distance + this.measurement, timestamp });

      clearInterval(this.interval);
    },
    countTime() {
      const time = new Date().getTime() - this.startTime;
      this.elapsed = parseFloat(Math.round(time) / 1000).toFixed(3);
    },
    reset() {
      this.state = 'off';
      this.elapsed = 0;
      this.startTime = 0;
    },

  },
};
</script>

<style>
html,body{
/*  overflow-x:hidden;
  overflow-y:auto;
  overscroll-behavior-y:contain;*/
  background: #37474F;
}
#app{
  background: #263238;
}
@media only screen and (min-width: 800px) {
  #app{
    max-width: 450px;
    margin: 0 auto;
  }
}
</style>
