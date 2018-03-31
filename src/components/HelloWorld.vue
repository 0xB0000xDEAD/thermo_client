<template>
<div class="hello">
  <h1>{{ msg }}</h1>
  <input v-model="filterArg" placeholder="filter by node name">
  <button v-on:click="resetData" type="button" name="button">Delete Data</button>
  <!-- <ul>
    <li v-for="item of nodes">
      {{ item }}
    </li>
  </ul> -->
  <trend :data="nodes" :gradient="['#6fa8dc', '#42b983', '#2c3e50']" auto-draw smooth>
  </trend>
  <br>
  <br>
  <b-dropdown>
            <button class="button is-primary" slot="trigger">
                <span>Click me!</span>
                <b-icon icon="menu-down"></b-icon>
            </button>

            <b-dropdown-item>Action</b-dropdown-item>
            <b-dropdown-item>Another action</b-dropdown-item>
            <b-dropdown-item>Something else</b-dropdown-item>
        </b-dropdown>
        <!-- <gmap></gmap> -->
        
</div>
</template>

<script>

// import * as VueGoogleMaps from 'vue2-google-maps'
// Vue.use(VueGoogleMaps, {
//   load: {
//     key: 'AIzaSyBJoqpzXmAlOuZ2e-eKP4wDe9YfLpnWhiY',
//     libraries: 'places', // This is required if you use the Autocomplete plugin
//     // OR: libraries: 'places,drawing'
//     // OR: libraries: 'places,drawing,visualization'
//     // (as you require)
//   }
// })


// import buefy
// import Vue from 'vue'
import Buefy from "buefy";
import "buefy/lib/buefy.css";
import _ from "lodash";

Vue.use(Buefy);
import Vue from "vue";
import Trend from "vuetrend";
Vue.use(Trend);
// import gmap from  './Map'
// Vue.use(gmap)

export default {
  name: "HelloWorld",
  data: function() {
    return {
      nodes: [],
      filterArg: ""
    };
  },
  components: {
    trend: Trend
  },
  props: {
    msg: String
  },
  created: function() {
    this.load();
  },
  watch: {
    // whenever question changes, this function will run
    filterArg: function () {
      this.courtesy = 'Waiting for you to stop typing...'
      this.load(this.filterArg)
    }
  },
  methods: {
     load: _.debounce(function(nodeId){
       this.idToName(nodeId);
      let myscope = this;
    // console.log(myscope);
    fetch("http://localhost:9000/api/thermonodes") // use get by id
      .then(function(response) {      
        return response.json();
      }).then(function(data) {        
        // console.log(typeof data);
        console.log("this is the data structure: \n");
        console.log(data);

        myscope.nodes = data[0].temp;
        


        // for (let values of Object.values(data)) {
        //   myscope.nodes.push(parseFloat(values.temp));
        // }
      })
      .catch(function(err) {
        console.log('there is a problem fetching the data', err);
      })
  }, 500),
  idToName: function (nodeId) {
    fetch("http://localhost:9000/api/thermonodes").then(function(response){
      return(response.json());
    }).then(function(data){
      console.log(data);
      
    })
    
  },
  resetData: function() {
    fetch("delete Api", option).then(function(response) {
      console.log(response.json());
    })
  }
  /* getAnswer: _.debounce( // use debounce....plaese import lodash
      function () {
        if (this.question.indexOf('?') === -1) {
          this.answer = 'Questions usually contain a question mark. ;-)'
          return
        }
        this.answer = 'Thinking...'
        var vm = this
        axios.get('https://yesno.wtf/api')
          .then(function (response) {
            vm.answer = _.capitalize(response.data.answer)
          })
          .catch(function (error) {
            vm.answer = 'Error! Could not reach the API. ' + error
          })
      },
      // This is the number of milliseconds we wait for the
      // user to stop typing.
      500
    ) */
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
