<template>
  <div class>
    <div class="row">
      <div class="col-12">
        <h4 slot="header">Network Map</h4>
        <div class="row">
          <div class="col-md-10">
            <card class="network-card" :style="{'height':'600px'}">
              <Network @receives="receives" ref="child"></Network>
              <!-- <p>NetworkMap</p> -->
            </card>
          </div>

          <div class="col-md-2">
            <card :style="{'text-align':'center'}">
              <h3>Attacks</h3>
              <div class="flex">
                <base-button
                  class="button"
                  @click="random()"
                  :simple="rpressed"
                  type="danger"
                >Random</base-button>

                <base-button class="button" @click="mitm()" :simple="mpressed" type="primary">MitM</base-button>

                <base-button class="button" @click="virus()" :simple="vpressed" type="primary">Virus</base-button>

                <base-button class="button" @click="rsw()" :simple="rspressed" type="primary">RSW</base-button>

                <base-button class="button" @click="ddos()" :simple="dpressed" type="primary">DDoS</base-button>
              </div>
            </card>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import Network from "@/components/Network";
import { NetworkMap } from "@/components";
import axios from "axios";
export default {
  components: {
    Network,
    NetworkMap
  },
  data() {
    return {
      dsrc: Number,
      dobj: Number,

      msrc: Number,
      mobj: Number,

      vsrc: Number,
      vobj: Number,

      rssrc: Number,
      rsobj: Number,

      rpressed: false,
      mpressed: false,
      vpressed: false,
      rspressed: false,
      dpressed: false,
      startDevices: [],
      endDevices: [],
      mstart: Date,
      mend: Date,

      vstart: Date,
      vend: Date,

      rsstart: Date,
      rsend: Date,

      dstart: Date,
      dend: Date,

      rdminterval: ""
    };
  },
  methods: {
    receives(devices) {
      console.log("activated");
      devices.start.forEach(element => {
        console.log("adding start device");
        console.log(element);
        this.startDevices.push(element);
      });
      devices.end.forEach(element => {
        console.log("adding end device");
        console.log(element);
        this.endDevices.push(element);
      });
    },
    APIcall: async function(obj) {
      console.log(obj);

      return await axios.put("http://localhost:8800/logs", {
        Attack_Type: obj.Attack_Type,
        Level_Affectation: obj.Level_Affectation,
        Affected_Area: obj.Affected_Area,
        Start_Time: obj.Start_Time,
        End_Time: obj.End_Time
      });
    },
    rndmOnDevices() {
      var src = this.startDevices[
        Math.floor(Math.random() * this.startDevices.length)
      ].id;
      var obj = this.endDevices[
        Math.floor(Math.random() * this.endDevices.length)
      ].id;
      if (src == obj) {
        return this.rndmOnDevices();
      }
      console.log(src, obj);
      return {
        src: src,
        obj: obj
      };
    },
    mitm: async function() {
      this.mpressed = !this.mpressed;

      // var Affected_Area=[];
      //  Affected_area.push();
      if (this.mpressed) {
        this.mstart = Date(Date.now());
        var rndom = this.rndmOnDevices();
        this.msrc = rndom.src;

        this.mobj = rndom.obj;

        console.log(this.msrc, this.mobj);
        this.$refs.child.attack(this.msrc, this.mobj);
      } else {
        console.log(this.msrc, this.mobj);
        this.$refs.child.disable(this.msrc, this.mobj);
        this.mend = Date(Date.now());
        var Attack_Type = "MitM";
        var Level_Affectation = Math.floor(Math.random() * 10 + 1);
        var Affected_Area = [];
        var obj = {
          Attack_Type: Attack_Type,
          Level_Affectation: Level_Affectation,
          Affected_Area: Affected_Area,
          Start_Time: this.mstart,
          End_Time: this.mend
        };

        await this.APIcall(obj);

        await axios.put("http://localhost:8800/add", {
          Affected_Areas: this.mobj
        });
      }
    },

    virus: async function() {
      this.vpressed = !this.vpressed;

      if (this.vpressed) {
        this.vstart = Date(Date.now());
        var rndom = this.rndmOnDevices();
        this.vsrc = rndom.src;

        this.vobj = rndom.obj;

        console.log(this.vsrc, this.vobj);
        this.$refs.child.attack(this.vsrc, this.vobj);
      } else {
        console.log(this.vsrc, this.vobj);
        this.$refs.child.disable(this.vsrc, this.vobj);
        this.vend = Date(Date.now());
        var Attack_Type = "Virus";
        var Level_Affectation = Math.floor(Math.random() * 10 + 1);
        var Affected_Area = [];
        var obj = {
          Attack_Type: Attack_Type,
          Level_Affectation: Level_Affectation,
          Affected_Area: Affected_Area,
          Start_Time: this.vstart,
          End_Time: this.vend
        };

        await this.APIcall(obj);

        await axios.put("http://localhost:8800/add", {
          Affected_Areas: this.vobj
        });
      }
      
    },rsw: async function() {
      this.rspressed = !this.rspressed;

      // var Affected_Area=[];
      //  Affected_area.push();
      if (this.rspressed) {
        this.rsstart = Date(Date.now());
        var rndom = this.rndmOnDevices();
        this.rssrc = rndom.src;

        this.rsobj = rndom.obj;

        console.log(this.rssrc, this.rsobj);
        this.$refs.child.attack(this.rssrc, this.rsobj);
      } else {
        console.log(this.msrc, this.rsobj);
        this.$refs.child.disable(this.rssrc, this.rsobj);
        this.rsend = Date(Date.now());
        var Attack_Type = "Ransomware";
        var Level_Affectation = Math.floor(Math.random() * 10 + 1);
        var Affected_Area = [];
        var obj = {
          Attack_Type: Attack_Type,
          Level_Affectation: Level_Affectation,
          Affected_Area: Affected_Area,
          Start_Time: this.rsstart,
          End_Time: this.rsend
        };

        await this.APIcall(obj);

        await axios.put("http://localhost:8800/add", {
          Affected_Areas: this.rsobj
        });
      }
    },ddos: async function() {
      this.dpressed = !this.dpressed;

      // var Affected_Area=[];
      //  Affected_area.push();
      if (this.dpressed) {
        this.dstart = Date(Date.now());
        var rndom = this.rndmOnDevices();
        this.dsrc = rndom.src;

        this.dobj = rndom.obj;

        console.log(this.dsrc, this.dobj);
        this.$refs.child.attack(this.dsrc, this.dobj);
      } else {
        console.log(this.dsrc, this.dobj);
        this.$refs.child.disable(this.dsrc, this.dobj);
        this.dend = Date(Date.now());
        var Attack_Type = "DDoS";
        var Level_Affectation = Math.floor(Math.random() * 10 + 1);
        var Affected_Area = [];
        var obj = {
          Attack_Type: Attack_Type,
          Level_Affectation: Level_Affectation,
          Affected_Area: Affected_Area,
          Start_Time: this.dstart,
          End_Time: this.dend
        };

        await this.APIcall(obj);

        await axios.put("http://localhost:8800/add", {
          Affected_Areas: this.dobj
        });
      }
    },
    random: async function() {
      this.rpressed = !this.rpressed;

      if (this.rpressed) {
        this.rdminterval = setInterval(() => {
          this.rdm();
        }, 2000);
      } else {
        clearInterval(this.rdminterval);
      }
    },
    rdm: function() {
      var fun = Math.floor(Math.random() * 4) + 1;
      switch (fun) {
        case 1:
          this.mitm();
          break;
        case 2:
          this.virus();
          break;
        case 3:
          this.rsw();
          break;
        case 4:
          this.ddos();
          break;
        default:
          console.log("error");
          break;
      }
    }
  },
  mounted() {
    // this.$refs.child.attack(2, 9);
    // this.$refs.child.disable(2, 9);
    // this.$refs.child.attack(1, 101);
    // this.$refs.child.disable(1, 101);
    // this.$refs.child.attack(1, 13);
    // this.$refs.child.disable(1, 13);
    // this.$refs.child.attack(7, 3);
    // this.$refs.child.disable(7, 3);
  }
}; //:style="{'min-width': '9vw'}"
</script>
<style>
.flex {
  height: 70vh;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}
.network-card .card-body {
  height: 100%;
}
</style>
