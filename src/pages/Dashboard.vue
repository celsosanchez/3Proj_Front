<template>
  <div v-show="!loading">
    <div class="row">
      <div class="col-12">
        <card type="chart">
          <template slot="header">
            <div class="row">
              <div class="col-sm-6" :class="isRTL ? 'text-right' : 'text-left'">
                <h3 class="card-category">{{$t('dashboard.totalShipments')+" (" +logs.length+")"}}</h3>
                <h2 class="card-title">
                  <i class="tim-icons icon-calendar-60 text-primary"></i>
                  {{$t('dashboard.performance')}}
                </h2>
              </div>
              <div class="col-sm-6">
                <div
                  class="btn-group btn-group-toggle"
                  :class="isRTL ? 'float-left' : 'float-right'"
                  data-toggle="buttons"
                >
                  <label
                    v-for="(option, index) in bigLineChartCategories"
                    :key="option"
                    class="btn btn-sm btn-primary btn-simple"
                    :class="{active: bigLineChart.activeIndex === index}"
                    :id="index"
                  >
                    <input
                      type="radio"
                      @click="initBigChart(index)"
                      name="options"
                      autocomplete="off"
                      :checked="bigLineChart.activeIndex === index"
                    />
                    {{option}}
                  </label>
                </div>
              </div>
            </div>
          </template>
          <div class="chart-area">
            <line-chart
              style="height: 100%"
              ref="bigChart"
              chart-id="big-line-chart"
              :chart-data="bigLineChart.chartData"
              :gradient-colors="bigLineChart.gradientColors"
              :gradient-stops="bigLineChart.gradientStops"
              :extra-options="bigLineChart.extraOptions"
            ></line-chart>
          </div>
        </card>
      </div>
    </div>
    <div class="row">
      <div class="col-lg-4" :class="{'text-right': isRTL}">
        <card type="chart">
          <template slot="header">
            <h5 class="card-category">Time under attack</h5>
            <h3 class="card-title">
              <i class="tim-icons icon-watch-time text-primary"></i>
              <span>{{totalTime}} minutes total</span>
            </h3>
          </template>
          <div class="chart-area">
            <line-chart
              style="height: 100%"
              chart-id="purple-line-chart"
              :chart-data="purpleLineChart.chartData"
              :gradient-colors="purpleLineChart.gradientColors"
              :gradient-stops="purpleLineChart.gradientStops"
              :extra-options="purpleLineChart.extraOptions"
            ></line-chart>
          </div>
        </card>
      </div>
      <div class="col-lg-4" :class="{'text-right': isRTL}">
        <card type="chart">
          <template slot="header">
            <h5 class="card-category">Attacks types</h5>
            <h3 class="card-title">
              <i class="tim-icons icon-tag text-info"></i>
              <span>
                {{this.getByType(
                this.logs).typelabels.length}} Types registered
              </span>
            </h3>
          </template>
          <div v-if="!loading" class="chart-area">
            <bar-chart
              style="height: 100%"
              chart-id="blue-bar-chart"
              :chart-data="blueBarChart.chartData"
              :gradient-stops="blueBarChart.gradientStops"
              :extra-options="blueBarChart.extraOptions"
            ></bar-chart>
          </div>
        </card>
      </div>
      <div class="col-lg-4" :class="{'text-right': isRTL}">
        <card type="chart">
          <template slot="header">
            <h5 class="card-category">Average affectation level by month</h5>
            <h3 class="card-title">
              <i class="tim-icons icon-sound-wave text-success"></i> <span>{{this.getByAffect(this.logs).totalAffectationMedia}}</span>
            </h3>
          </template>
          <div class="chart-area">
            <line-chart
              style="height: 100%"
              chart-id="green-line-chart"
              :chart-data="greenLineChart.chartData"
              :gradient-stops="greenLineChart.gradientStops"
              :extra-options="greenLineChart.extraOptions"
            ></line-chart>
          </div>
        </card>
      </div>
    </div>
    <div class="row">
      <div class="col-lg-6 col-md-12">
        <card type="tasks" :header-classes="{'text-right': isRTL}">
          <template slot="header">
            <h6 class="title d-inline">{{$t('dashboard.tasks', {count: 5})}}</h6>
            <p class="card-category d-inline">{{$t('dashboard.today')}}</p>
            <base-dropdown
              menu-on-right
              tag="div"
              title-classes="btn btn-link btn-icon"
              aria-label="Settings menu"
              :class="{'float-left': isRTL}"
            >
              <i slot="title" class="tim-icons icon-settings-gear-63"></i>

              <a class="dropdown-item" href="#pablo">{{$t('dashboard.dropdown.action')}}</a>
              <a class="dropdown-item" href="#pablo">{{$t('dashboard.dropdown.anotherAction')}}</a>
              <a class="dropdown-item" href="#pablo">{{$t('dashboard.dropdown.somethingElse')}}</a>
            </base-dropdown>
          </template>
          <div class="table-full-width table-responsive">
            <task-list></task-list>
          </div>
        </card>
      </div>
      <div class="col-lg-6 col-md-12">
        <card class="card" :header-classes="{'text-right': isRTL}">
          <h4 slot="header" class="card-title">
            <i slot="title" class="tim-icons icon-paper"></i> <span>Logs {{" (" +logs.length+")"}}</span>
          </h4>
          <div class="table-responsive">
            <user-table
              :columns="['Affected_Areas','Attack_Type','Level_Affectation','Start_Time','End_Time','Duration']"
              :data="logs"
            ></user-table>
          </div>
        </card>
      </div>
    </div>
  </div>
</template>
<script>
import LineChart from "@/components/Charts/LineChart";
import BarChart from "@/components/Charts/BarChart";
import * as chartConfigs from "@/components/Charts/config";
import TaskList from "./Dashboard/TaskList";
import UserTable from "./Dashboard/UserTable";
import config from "@/config";
import axios from "axios";

export default {
  components: {
    LineChart,
    BarChart,
    TaskList,
    UserTable
  },
  data() {
    return {
      logs: [],
      talabels: [],
      timeattack: [],
      totalTime: "",
      timelabels: [],
      timevalues: [],
      loading: true,
      bigLineChart: {
        allData: [
          [80, 120, 105, 110, 95, 105, 90, 100, 80, 95, 70, 120],
          [80, 120, 105, 110, 95, 105, 90, 100, 80, 95, 70, 120],
          [60, 80, 65, 130, 80, 105, 90, 130, 70, 115, 60, 130]
        ],
        activeIndex: 0,
        chartData: null,
        extraOptions: chartConfigs.purpleChartOptions,
        gradientColors: config.colors.primaryGradient,
        gradientStops: [1, 0.4, 0],
        categories: []
      },
      purpleLineChart: {
        extraOptions: chartConfigs.purpleChartOptions,
        chartData: {
          labels: ["labels", "labels", "labels"],
          datasets: [
            {
              label: "Data",
              fill: true,
              borderColor: config.colors.primary,
              borderWidth: 2,
              borderDash: [],
              borderDashOffset: 0.0,
              pointBackgroundColor: config.colors.primary,
              pointBorderColor: "rgba(255,255,255,0)",
              pointHoverBackgroundColor: config.colors.primary,
              pointBorderWidth: 20,
              pointHoverRadius: 4,
              pointHoverBorderWidth: 15,
              pointRadius: 4,
              data: [1, 2, 3]
            }
          ]
        },
        gradientColors: config.colors.primaryGradient,
        gradientStops: [1, 0.2, 0]
      },
      greenLineChart: {
        extraOptions: chartConfigs.greenChartOptions,
        chartData: {
          labels: ["JUL", "AUG", "SEP", "OCT", "NOV"],
          datasets: [
            {
              label: "Average",
              fill: true,
              borderColor: config.colors.danger,
              borderWidth: 2,
              borderDash: [],
              borderDashOffset: 0.0,
              pointBackgroundColor: config.colors.danger,
              pointBorderColor: "rgba(255,255,255,0)",
              pointHoverBackgroundColor: config.colors.danger,
              pointBorderWidth: 20,
              pointHoverRadius: 4,
              pointHoverBorderWidth: 15,
              pointRadius: 4,
              data: [90, 27, 60, 12, 80]
            }
          ]
        },
        gradientColors: [
          "rgba(66,134,121,0.15)",
          "rgba(66,134,121,0.0)",
          "rgba(66,134,121,0)"
        ],
        gradientStops: [1, 0.4, 0]
      },
      blueBarChart: {
        extraOptions: chartConfigs.barChartOptions,
        chartData: {
          labels: ["USA", "GER", "AUS", "UK", "RO", "BR"],
          datasets: [
            {
              label: "Ocurrences",
              fill: true,
              borderColor: config.colors.info,
              borderWidth: 2,
              borderDash: [],
              borderDashOffset: 0.0,
              data: []
            }
          ]
        },
        gradientColors: config.colors.primaryGradient,
        gradientStops: [1, 0.4, 0]
      }
    };
  },
  computed: {
    enableRTL() {
      return this.$route.query.enableRTL;
    },
    isRTL() {
      return this.$rtl.isRTL;
    },
    bigLineChartCategories() {
      return this.$t("dashboard.chartCategories");
    }
  },
  methods: {
    initBigChart(index) {
      let chartData = {
        datasets: [
          {
            fill: true,
            borderColor: config.colors.primary,
            borderWidth: 2,
            borderDash: [],
            borderDashOffset: 0.0,
            pointBackgroundColor: config.colors.primary,
            pointBorderColor: "rgba(255,255,255,0)",
            pointHoverBackgroundColor: config.colors.primary,
            pointBorderWidth: 20,
            pointHoverRadius: 4,
            pointHoverBorderWidth: 15,
            pointRadius: 4,
            data: this.timeattack[index]
          }
        ],
        labels: this.talabels[index]
      };
      this.$refs.bigChart.updateGradients(chartData);
      this.bigLineChart.chartData = chartData;
      this.bigLineChart.activeIndex = index;
    },
    sortStart: function(data) {
      // var neosHavingData = data.filter(function (neo) {
      //     return neo.close_approach_data.length > 0;
      // });
      var start = data.map(function(item) {
        return { start: new Date(item.Start_Time) };
      });
      var sortedStarts = start.sort(function(a, b) {
        return a.start.getTime() - b.start.getTime();
      });
      var values = [];
      var labels = [];
      sortedStarts.forEach(element => {
        let year = new Date(element.start).getYear() + 1900;
        let month =new Date(element.start).getMonth()+1;
        let formatted =
          new Date(element.start).getDate() +
          "/" +
          month+
          "/" +
          year;
        
        if (!labels.includes(formatted)) {
          labels.push(formatted);
          values[labels.lastIndexOf(formatted)] = 1;
        } else {
          values[labels.lastIndexOf(formatted)] += 1;
        }
      });

      var alabels = [];
      var avalues = [];
      sortedStarts.forEach(element => {
        
        let year = new Date(element.start).getYear() + 1900;
        let month =new Date(element.start).getMonth()+1;
        let formatted =
          new Date(element.start).getDate() +
          "/" +
          month+
          "/" +
          year;
        if (labels[labels.length - 1] == formatted) {
          let year = new Date(element.start).getYear() + 1900;
          let month =new Date(element.start).getMonth()+1;
          let aformatted =
            new Date(element.start).getHours() +
            "h of day " +
            new Date(element.start).getDate() +
            "/" +
            month +
            "/" +
            year;
          
          if (!alabels.includes(aformatted)) {
            alabels.push(aformatted);
            avalues[alabels.lastIndexOf(aformatted)] = 1;
          } else {
            avalues[alabels.lastIndexOf(aformatted)] += 1;
          }
        }
      });

      var blabels = [];
      var bvalues = [];
      sortedStarts.forEach(element => {
        let year = new Date(element.start).getYear() + 1900;
        let month =new Date(element.start).getMonth()+1;
        let aformatted =
          new Date(element.start).getHours() +
          "h of day " +
          new Date(element.start).getDate() +
          "/" +
          month +
          "/" +
          year;
  
        if (alabels[alabels.length - 1] == aformatted) {
          let year = new Date(element.start).getYear() + 1900;
          let month =new Date(element.start).getMonth()+1;
          let bformatted =
            new Date(element.start).getHours() +
            ":" +
            new Date(element.start).getMinutes() +
            " - " +
            new Date(element.start).getDate() +
            "/" +
            month +
            "/" +
            year;
          if (!blabels.includes(bformatted)) {
            blabels.push(bformatted);
            bvalues[blabels.lastIndexOf(bformatted)] = 1;
          } else {
            bvalues[blabels.lastIndexOf(bformatted)] += 1;
          }
        }
      });
        
      return {
        //labels= days alabels=hours blavels= min
        labels: [labels, alabels, blabels],
        values: [values, avalues, bvalues]
      };
    },

    getByTime: function(data) {
      var totalTime = 0;
      data.forEach(element => {
        totalTime = totalTime + element.Duration;
      });

      var start = data.map(function(item) {
        return { start: new Date(item.Start_Time), duration: item.Duration };
      });
      var sortedStartD = start.sort(function(a, b) {
        return a.start.getTime() - b.start.getTime();
      });

      var tvalues = [];
      var tlabels = [];
      sortedStartD.forEach(element => {
        let year = new Date(element.start).getYear() + 1900;
        let month =new Date(element.start).getMonth()+1;
        let formatted =
          new Date(element.start).getDate() +
          "/" +
          month+
          "/" +
          year;
        
        if (!tlabels.includes(formatted)) {
          tlabels.push(formatted);
          tvalues[tlabels.lastIndexOf(formatted)] = element.duration / 60;
        } else {
          tvalues[tlabels.lastIndexOf(formatted)] += element.duration / 60;
        }
      });

      this.totalTime = totalTime / 60;

      return {
        tvalues: tvalues,
        tlabels: tlabels
      };
    },
    getByType: function(data) {
      var typelabels = [];
      var typevalues = [];
      data.forEach(element => {
        if (!typelabels.includes(element.Attack_Type)) {
          typelabels.push(element.Attack_Type);
          typevalues[typelabels.lastIndexOf(element.Attack_Type)] = 1;
        } else {
          typevalues[typelabels.lastIndexOf(element.Attack_Type)] =
            typevalues[typelabels.lastIndexOf(element.Attack_Type)] + 1;
        }
      });

      return {
        typelabels: typelabels,
        typevalues: typevalues
      };
    },
    getByAffect: function(data) {
      var totalAffectationMedia = 0;
      data.forEach(element => {
        totalAffectationMedia =
          totalAffectationMedia + element.Level_Affectation;
      });

      var start = data.map(function(item) {
        return {
          start: new Date(item.Start_Time),
          Affectation: item.Level_Affectation
        };
      });
      var sortedStartD = start.sort(function(a, b) {
        return a.start.getTime() - b.start.getTime();
      });

      var afvalues = [];
      var aflabels = [];
      var afvaluesAmount = [];
      sortedStartD.forEach(element => {
        let year = new Date(element.start).getYear() + 1900;
        let month =new Date(element.start).getMonth()+1;
        let formatted = month + "/" + year;
        
        if (!aflabels.includes(formatted)) {
          aflabels.push(formatted);
          afvalues[aflabels.lastIndexOf(formatted)] = element.Affectation;
          afvaluesAmount[aflabels.lastIndexOf(formatted)] = 1;
        } else {
          afvalues[aflabels.lastIndexOf(formatted)] =
            afvalues[aflabels.lastIndexOf(formatted)] + element.Affectation;
          afvaluesAmount[aflabels.lastIndexOf(formatted)] += 1;
        }
      });
      var media = [];
      for (var i = 0; i < afvalues.length; i++) {
        media[i] = Math.round(afvalues[i] / afvaluesAmount[i]);
      }

      totalAffectationMedia = (totalAffectationMedia / data.length).toFixed(2);
      return {
        aflabels: aflabels,
        media: media,
        totalAffectationMedia: totalAffectationMedia
      };
      
    },
    getData: function() {
      var url = "http://localhost:8800/logs";

      axios
        .get(url)
        .then(res => {
          this.logs = res.data.found;
          
        })
        .then(() => {
          if (this.logs.length > 0) {
            this.talabels = this.sortStart(this.logs).labels;
            this.timeattack = this.sortStart(this.logs).values;

            this.purpleLineChart.chartData.labels = this.getByTime(
              this.logs
            ).tlabels;
            this.purpleLineChart.chartData.datasets[0].data = this.getByTime(
              this.logs
            ).tvalues;

            this.blueBarChart.chartData.labels = this.getByType(
              this.logs
            ).typelabels;
            this.blueBarChart.chartData.datasets[0].data = this.getByType(
              this.logs
            ).typevalues;

            this.greenLineChart.chartData.labels = this.getByAffect(this.logs).aflabels;
             this.greenLineChart.chartData.datasets[0].data = this.getByAffect(this.logs).media;
            
            this.initBigChart(0);
            this.loading = false;
          }
        });
    }
  },
  created() {
    this.getData();
  },
  mounted() {
    this.i18n = this.$i18n;
    if (this.enableRTL) {
      this.i18n.locale = "ar";
      this.$rtl.enableRTL();
    }
  },
  updated() {},
  beforeDestroy() {
    if (this.$rtl.isRTL) {
      this.i18n.locale = "en";
      this.$rtl.disableRTL();
    }
  }
};
</script>
<style>
</style>
