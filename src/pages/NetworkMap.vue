<template>
  <div class>
    <div class="row">
      <div class="col-12">
        <h4 slot="header">Network Map</h4>
        <div class="row">
          <div class="col-md-10">
            <card class="network-card" :style="{'height':'600px'}">
              <Network></Network>
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
import Network from "@/components/Network"
import { NetworkMap } from "@/components";
import axios from "axios";
export default {
  components: {
    Network,
    NetworkMap
  },
  data() {
    return {
      rpressed: false,
      mpressed: false,
      vpressed: false,
      rspressed: false,
      dpressed: false,

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
    mitm: async function() {
      this.mpressed = !this.mpressed;

      if (this.mpressed) {
        this.mstart = Date(Date.now());
      } else {
        this.mend = Date(Date.now());
        var Attack_Type = "MitM";
        var Level_Affectation = Math.floor(Math.random() * 10 + 1);
        var Affected_Area = Math.floor(Math.random() * 5 + 1);
        await this.APIcall({
          Attack_Type: Attack_Type,
          Level_Affectation: Level_Affectation,
          Affected_Area: Affected_Area,
          Start_Time: this.mstart,
          End_Time: this.mend
        });
      }
    },

    virus: async function() {
      this.vpressed = !this.vpressed;

      if (this.vpressed) {
        this.vstart = Date(Date.now());
      } else {
        this.vend = Date(Date.now());
        var Attack_Type = "Virus";
        var Level_Affectation = Math.floor(Math.random() * 10 + 1);
        var Affected_Area = Math.floor((Math.random() * 10 + 1) / 2);
        await this.APIcall({
          Attack_Type: Attack_Type,
          Level_Affectation: Level_Affectation,
          Affected_Area: Affected_Area,
          Start_Time: this.vstart,
          End_Time: this.vend
        });
      }
    },
    rsw: async function() {
      this.rspressed = !this.rspressed;

      if (this.rspressed) {
        this.rsstart = Date(Date.now());
      } else {
        this.rsend = Date(Date.now());
        var Attack_Type = "Ransomware";
        var Level_Affectation = Math.floor(Math.random() * 10 + 1);
        var Affected_Area = Math.floor((Math.random() * 10 + 1) / 2);
        await this.APIcall({
          Attack_Type: Attack_Type,
          Level_Affectation: Level_Affectation,
          Affected_Area: Affected_Area,
          Start_Time: this.rstart,
          End_Time: this.rsend
        });
      }
    },
    ddos: async function() {
      this.dpressed = !this.dpressed;

      if (this.dpressed) {
        this.dstart = Date(Date.now());
      } else {
        this.dend = Date(Date.now());
        var Attack_Type = "DDoS";
        var Level_Affectation = Math.floor(Math.random() * 10 + 1);
        var Affected_Area = Math.floor((Math.random() * 10 + 1) / 2);
        await this.APIcall({
          Attack_Type: Attack_Type,
          Level_Affectation: Level_Affectation,
          Affected_Area: Affected_Area,
          Start_Time: this.dstart,
          End_Time: this.dend
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
          this.ddos()
          break;
        default:
          console.log("error");
          break;
      }
    }
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
