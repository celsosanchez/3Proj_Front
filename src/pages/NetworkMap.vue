<template>
  <div class>
    <div class="row">
      <div class="col-12">
        <h4 slot="header">Network Map</h4>
        <div class="row">
          <div class="col-md-3">
            <h4 class="mt-3">Saved data : {{savedData}} MB</h4>
          </div>
          <div class="col-md-1">
            <base-button v-show="!saving" @click="savedata()">
              <i style="font-size: 20px" class="tim-icons icon-cloud-upload-94"></i>
            </base-button>
            <pulse-loader class="mt-3" v-show="saving"></pulse-loader>
          </div>
          <div class="col-md-3 ml-4 mt-3">
            <h4 :style="loststyle">Vulnerable data: {{nonSaved}} MB</h4>
          </div>
        </div>
        <div class="row">
          <div class="col-md-10">
            <card class="network-card" :style="{'height':'600px'}">
              <Network @receives="receives" ref="child"></Network>
              <!-- <p>NetworkMap</p> -->
            </card>
          </div>

          <div class="col-md-2">
            <card :style="{'text-align':'center'}">
              <h3>Defense</h3>
              <base-button
                class="button"
                @click="ai()"
                :simple="aipressed"
                type="primary"
              >Defense AI</base-button>

              <h3 class="mt-5">Attacks</h3>
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
// import { PulseLoader } from 'vue-spinner'
import PulseLoader from "vue-spinner/src/PulseLoader.vue";
export default {
  components: {
    PulseLoader,
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

      aipressed: false,
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

      loststyle: "color : white",

      rdminterval: "",

      saving: false,
      saveDataInterval: null,

      savedData: 0,

      nonSaved: 0,
      rdminterval: "",

      timeouts: {
        mpressed: null,
        vpressed: null,
        rspressed: null,
        dpressed: null
      }
    };
  },
  methods: {
    smartphoneAttack(src, obj) {
      console.log(" before smartphone attack  " + src, obj);

      if (src == 100 || src == 101 || src == 102) {
        var possible = [100, 101, 102];
        possible.splice(possible.indexOf(src), 1);
        obj = possible[Math.floor(Math.random() * 2)];
      } else {
        var as = true;
        while (as) {
          console.log(obj);
          if (obj == 100 || obj == 101 || obj == 102) {
            var rndom = this.rndmOnDevices();
            obj = rndom.obj;
          } else {
            console.log(obj);
            as = false;
          }
        }
      }
      console.log(" after smartphone attack  " + src, obj);
      return { src: src, obj: obj };
    },
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
    lostdata: function() {
      this.loststyle = "color: red";
      setTimeout(() => {
        this.loststyle = "color: white";
      }, 300);
    },
    ai() {
      this.aipressed = !this.aipressed;
      if (this.aipressed && this.saveDataInterval == null) {
        this.saveDataInterval = setInterval(this.savedata, 10000);
      } else if (!this.aipressed && this.saveDataInterval != null) {
        clearInterval(this.saveDataInterval);
        this.saveDataInterval = null;
      }
      [
        { key: "mpressed", func: "mitm" },
        { key: "vpressed", func: "virus" },
        { key: "rspressed", func: "rsw" },
        { key: "dpressed", func: "ddos" }
      ].forEach(({ key, func }) => {
        if (this.aipressed) {
          if (this[key] && this.timeouts[key] === null) {
            setTimeout(this[func], 30000);
          }
        } else {
          if (this[key] && this.timeouts[key] !== null) {
            clearTimeout(this.timeouts[key]);
            this.timeouts[key] = null;
          }
        }
      });
    },
    mitm: async function() {
      this.mpressed = !this.mpressed;

      // var Affected_Area=[];
      //  Affected_area.push();
      if (this.mpressed) {
        this.mstart = Date(Date.now());
        var rndom = this.rndmOnDevices();
        this.msrc = rndom.src;

        if (this.aipressed && this.msrc == 1) {
          this.$refs.child.attack(1, 3);
          setTimeout(() => {
            this.$refs.child.disable(1, 3);
            this.mpressed = false;
          }, 1500);
        } else {
          this.mobj = rndom.obj;
          var smrtcheck = this.smartphoneAttack(this.msrc, this.mobj);
          this.msrc = smrtcheck.src;
          this.mobj = smrtcheck.obj;

          if (this.aipressed && (this.mobj == 9 || this.mobj == 10)) {
            this.$refs.child.attack(this.msrc, 5);
            setTimeout(() => {
              this.$refs.child.disable(this.msrc, 5);
              this.mpressed = false;
            }, 1500);
          } else {
            console.log(this.msrc, this.mobj);
            this.$refs.child.attack(this.msrc, this.mobj);
            if (this.aipressed)
              this.timeouts.mpressed = setTimeout(this.mitm, 30000);
          }
        }
      } else {
        if (this.timeouts.mpressed !== null) {
          clearTimeout(this.timeouts.mpressed);
          this.timeouts.mpressed = null;
        }
        console.log(this.msrc, this.mobj);
        this.$refs.child.disable(this.msrc, this.mobj);
        this.mend = Date(Date.now());
        var Attack_Type = "MitM";
        var Level_Affectation = Math.floor(Math.random() * 10 + 1);
        this.nonSaved -= Level_Affectation * 2;
        if (this.nonSaved < 1) {
          this.nonSaved = 0;
        }
        this.lostdata();
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
        if (this.aipressed && this.vsrc == 1) {
          this.$refs.child.attack(1, 3);
          setTimeout(() => {
            this.$refs.child.disable(1, 3);
            this.vpressed = false;
          }, 1500);
        } else {
          this.vobj = rndom.obj;
          var smrtcheck = this.smartphoneAttack(this.vsrc, this.vobj);
          this.vsrc = smrtcheck.src;
          this.vobj = smrtcheck.obj;
          if (this.aipressed && (this.vobj == 9 || this.vobj == 10)) {
            this.$refs.child.attack(this.vsrc, 5);
            setTimeout(() => {
              this.$refs.child.disable(this.vsrc, 5);
              this.vpressed = false;
            }, 1500);
          } else {
            console.log(this.vsrc, this.vobj);
            this.$refs.child.attack(this.vsrc, this.vobj);
            if (this.aipressed)
              this.timeouts.vpressed = setTimeout(this.virus, 30000);
          }
        }
      } else {
        if (this.timeouts.vpressed !== null) {
          clearTimeout(this.timeouts.vpressed);
          this.timeouts.vpressed = null;
        }
        console.log(this.vsrc, this.vobj);
        this.$refs.child.disable(this.vsrc, this.vobj);
        this.vend = Date(Date.now());
        var Attack_Type = "Virus";
        var Level_Affectation = Math.floor(Math.random() * 10 + 1);
        this.nonSaved -= Level_Affectation * 2;
        if (this.nonSaved < 1) {
          this.nonSaved = 0;
        }
        this.lostdata();
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
    },
    rsw: async function() {
      this.rspressed = !this.rspressed;

      // var Affected_Area=[];
      //  Affected_area.push();
      if (this.rspressed) {
        this.rsstart = Date(Date.now());
        var rndom = this.rndmOnDevices();
        this.rssrc = rndom.src;
        if (this.aipressed && this.rssrc == 1) {
          this.$refs.child.attack(1, 3);
          setTimeout(() => {
            this.$refs.child.disable(1, 3);
            this.rspressed = false;
          }, 1500);
        } else {
          this.rsobj = rndom.obj;

          var smrtcheck = this.smartphoneAttack(this.rssrc, this.rsobj);
          this.rssrc = smrtcheck.src;
          this.rsobj = smrtcheck.obj;

          if (this.aipressed && (this.rsobj == 9 || this.rsobj == 10)) {
            this.$refs.child.attack(this.rssrc, 5);
            setTimeout(() => {
              this.$refs.child.disable(this.rssrc, 5);
              this.rspressed = false;
            }, 1500);
          } else {
            console.log(this.rssrc, this.rsobj);
            this.$refs.child.attack(this.rssrc, this.rsobj);
            if (this.aipressed)
              this.timeouts.rspressed = setTimeout(this.rsw, 30000);
          }
        }
      } else {
        if (this.timeouts.rspressed !== null) {
          clearTimeout(this.timeouts.rspressed);
          this.timeouts.rspressed = null;
        }
        console.log(this.msrc, this.rsobj);
        this.$refs.child.disable(this.rssrc, this.rsobj);
        this.rsend = Date(Date.now());
        var Attack_Type = "Ransomware";
        var Level_Affectation = Math.floor(Math.random() * 10 + 1);
        this.nonSaved -= Level_Affectation * 2;
        if (this.nonSaved < 1) {
          this.nonSaved = 0;
        }
        this.lostdata();
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
    },
    ddos: async function() {
      this.dpressed = !this.dpressed;

      // var Affected_Area=[];
      //  Affected_area.push();
      if (this.dpressed) {
        this.dstart = Date(Date.now());
        var rndom = this.rndmOnDevices();
        this.dsrc = 1;
        if (this.aipressed && this.dsrc == 1) {
          this.$refs.child.attack(1, 3);
          setTimeout(() => {
            this.$refs.child.disable(1, 3);
            this.dpressed = false;
          }, 1500);
        } else {
          this.dobj = rndom.obj;

          console.log(this.dsrc, this.dobj);
          this.$refs.child.attack(this.dsrc, this.dobj);
          if (this.aipressed)
            this.timeouts.dpressed = setTimeout(this.ddos, 30000);
        }
      } else {
        if (this.timeouts.dpressed !== null) {
          clearTimeout(this.timeouts.dpressed);
          this.timeouts.dpressed = null;
        }
        console.log(this.dsrc, this.dobj);
        this.$refs.child.disable(this.dsrc, this.dobj);
        this.dend = Date(Date.now());
        var Attack_Type = "DDoS";
        var Level_Affectation = Math.floor(Math.random() * 10 + 1);
        this.nonSaved -= Level_Affectation * 2;
        if (this.nonSaved < 1) {
          this.nonSaved = 0;
        }
        this.lostdata();
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
        }, 10000);
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
    },
    savedata: function() {
      if (
        ["mpressed", "vpressed", "rspressed", "dpressed"].some(key => this[key])
      )
        return;
      this.saving = true;
      setTimeout(() => {
        this.savedData = this.savedData + this.nonSaved;
        this.nonSaved = 0;
        this.saving = false;
      }, 2000);
    }
  },
  mounted() {
    setInterval(() => {
      this.nonSaved += 10;
    }, 3000);

    this.nonSaved;

    // setTimeout(() => {

    // }, 3000);
  }
}; //:style="{'min-width': '9vw'}"
</script>
<style>
.flex {
  height: 50vh;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}
.network-card .card-body {
  height: 100%;
}
</style>
