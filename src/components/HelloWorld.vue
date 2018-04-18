<template>
<div class="hello">
  <h1>{{ msg }}</h1>
  <h2>
    this are the registered nodes
  </h2>
  <ul>
    <li v-for="node of nodes">
      {{ node }}
    </li>
  </ul>

  <input v-model="filterArg" placeholder="filter by node name">
  <button v-on:click="resetData" type="button" name="button">reset data</button>
  
  <h2>{{courtesy}}</h2>
  <trend :data="tPoints" :gradient="['#6fa8dc', '#42b983', '#2c3e50']" auto-draw smooth>
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
// import config from "../config"; // testing

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
const dbURI = "http://localhost:9000/api/thermonodes"; //api endpoint
export default {
  name: "HelloWorld",
  data: function() {
    return {
      nodes: [],
      // tPoints: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0], //12 elements
      tPoints: [],
      filterArg: "",
      courtesy: ""
    };
  },
  components: {
    trend: Trend
  },
  props: {
    msg: String
  },
  created: function() {
    this.getNodesName(); // preload node names
    // this.load(idArray[0]); // load data from first node by default
    // this.load();
  },
  watch: {
    // called every change in the input field
    filterArg: function() {
      // this.courtesy = "Waiting for you to stop typing...";
      this.search(this.filterArg);
    }
  },
  methods: {
    getNodesName: function() { // riscrivere con init: {"fields":"id"}
      let nodes = this.nodes;
      fetch(dbURI) // use get by id
        .then(function(response) {
          return response.json();
        })
        .then(function(data) {
          if (data.length > 0) {
            for (const node of data) {
              nodes.push(node.name);
            }
            // console.log(`node in db are: ${nodes}`);
          }
          console.log(`node in db are: ${nodes}`);
        })
        .catch(function(err) {
          console.log("there is a problem fetching the data", err);
        });
    },
    search: _.debounce(function(string) {
      // console.log(`nameToId executed with ${string} as argument`);
      let resultArray = [];
      let refer = this.tPoints;
      fetch(dbURI)
        .then(function(response) {
          return response.json();
        })
        .then(function(nodes) {
          for (const node of nodes) {
            if (string == node.name) {
              resultArray.push(node.id);
            }
          }
          // console.log(`resultArray in .then() is: ${resultArray}`);

          //call a function expression
          (function(node) {
            let refer2 = refer; // array
            // refer2[0]++;

            if (node != undefined) {
              // console.log(`load() launched with ${node} as parameter`);

              // console.log(`filter executed on filterArg change to ${this.filterArg}`);
              // let apiRequest = `${dbURI}/:${this.nameToId(this.filterArg)}`;
              let apiRequest = `${dbURI}/${node}`;
              console.log(apiRequest);
              fetch(apiRequest) // use get by id
                .then(function(response) {
                  return response.json();
                })
                .then(function(data) {
                  /* refer2[0]++; // work
                  refer2 = data.temp; //dont work cause  object pass-by-reference ???
                  console.log(refer2); */

                  /*  let index = -1;
                  for (let el of data.temp) {
                    console.log(el);
                    refer2[index++] = parseInt(el);
                  }
                  console.log(refer2);
                  console.log(index); */

                  let pippo = data.temp.map(el => {
                    return parseInt(el);
                  });
                  console.log(pippo);

                  pippo[3] = 33; // visual improve the detection that data are changing
                  refer2.splice(0, pippo.length, ...pippo);

                  // append the temp data data

                  /* for (let values of Object.values(data.temp)) {
                    refer2.push(parseInt(values));
                    // this.courtesy = "";
                  }
 */

                  return refer2;
                })
                .catch(function(err) {
                  console.log("there is a problem fetching the data", err);
                });
            } else {
              console.log(`load() launched without parameter`);
              refer2.splice(0,12, ...[]);
            }
          })(resultArray[0]);
        });
      // console.log(`resultArray outside is: ${resultArray}`);
    }, 500),
    resetData: function() {
      fetch(dbURI, { fields: "id" }) // return [ id ]
        .then(function(response) {
          return response.json();
        })
        .then(function(data) {
          if (data.length > 0) {
            for (const node of data) {
              fetch(`${dbURI}/${node.id}`, { method: "DELETE" });
            }
            console.log(`tada!`);
          }
        })
        .catch(function(err) {
          console.log("there is a problem fetching the data", err);
        });
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
h1 {
  color:#42b983
}
h2 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  /* display: inline-block; */
  color: #42b983;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
