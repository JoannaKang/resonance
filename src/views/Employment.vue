<template>
  <main id="employment">
    <section id="page-title">
      <row>
        <column :lg="8" class="page-title">
          <div align="left" class="back">
            <router-link to="/">
              <img src="../../src/assets/BackArrow.svg" alt="back-arrow"/> Back
            </router-link>
          </div>
          <h1 class="title">Positive progress - Employment status</h1>
        </column>
        <column :lg="4" class="progress-summary">
          <div class="doughnut-1"><aim-doughnut-chart :doughnutChartData="doughnutChartData1"></aim-doughnut-chart></div>
          <div class="doughnut-2"><time-doughnut-chart  :doughnutChartData="doughnutChartData2"></time-doughnut-chart></div>
        </column>
      </row>
    </section>
    <section class="employment-select-area">
      <row :gutter="12" >
        <column :lg="1.5"><h3 class="employment-select-country">Select status</h3></column>
        <column :lg="2.5" class="employment-select-box">
          <v-select :options="employmentStatus" v-model="selectedStatus" placeholder="Show all" :searchable="false">
          </v-select>
        </column>
        <column :lg="1.5"><h3 class="employment-select-camp">Select fund</h3></column>
        <column :lg="2.5" class="employment-select-box">
          <v-select :options="funds" v-model="selectedFund" class="select-camp" :placeholder="fundPlaceholder" :searchable="false" :disabled="fundSelectboxDisabled">
            <span slot="no-options">
              <h3>No more available options</h3>
            </span>
          </v-select>
        </column>
        <column :lg="1.5"><h3 class="employment-select-school">Select  demographic</h3></column>
        <column :lg="2.5" class="employment-select-box">
          <v-select :options="demographics" v-model="selectedDemo" class="select-school" placeholder="Show none" :searchable="false" :disabled="demoSelectboxDisabled">
            <span slot="no-options">
              <h3 style="text-align:left; padding-left: 1.8rem; color:#686868; font-family: Helvetica; font-size:1.4rem;">No more available options</h3> 
            </span>            
          </v-select>
        </column>
      </row>
    </section>
    <section :gutter="12" class="chart-title-area">
      <row class="chart-title">
        <column :lg="8" :xs="6"><h2 class="employment-sub-title">Employment status {{status}} {{fund}} {{demo}}</h2></column>
        <column class="chart-summary" :lg="4" :xs="6">
          <div class="total-tenants"> <span><h1>{{ totalTenants }}</h1> <h2>tenants</h2></span> <h3 style="font-family:'Source Sans Pro';">employment status - all</h3></div>
          <div class="growth-rate"> <h1>{{growthRate}}</h1> <h3 style="font-family:'Source Sans Pro';">last 12 months</h3></div>
        </column>
      </row>
    </section>
    <section id="chart-area">
      <row :gutter="12" class="chart-main">
        <column :lg="8.16" class="line-chart-area">
          <h3>No of Tenants</h3>
          <line-chart :chart-data="chartData" :options="options" v-if="linechartShow "></line-chart>
          <stacked-bar-chart id="stacked-bar-chart" :chart-data="stackedBarChartData" :options="stackedBarchartOption" v-if="stackedChartShow === true"></stacked-bar-chart>
          <column :lg="4" :xs="12" class="year-select-box" ><v-select :options="yearOptions" v-model="selectedYear" class="select-year" placeholder="Show all" :searchable="false"></v-select></column>
        </column>
        <!-- Status summary start-->
        <column :lg="3.84" class="summary-area" v-if="viewMode ==='All' || selectedStatus === null && stackedChartShow === false || linechartShow === true && viewMode ==='Fund'">
          <div class="summary-wrapper" v-for="status in summaryBoxData" v-bind:key="status.vForId" :value="status.vForId">
            <div class="text-container">
              <input type="checkbox" v-bind:class="status.cssId" v-bind:id="status.cssId" v-bind:key="status.vForId" :value="status.vForId" v-model="checkedItems" style="display:none">
                <label v-bind:class="status.cssId" v-bind:for="status.cssId">
                  <div v-bind:class="status.cssId" v-bind:for="status.cssId" style="justify-content:center; align-item:center;">
                    <span v-bind:class="status.cssId" v-bind:for="status.cssId" style="color:#ffffff; margin:2px 2px 2px 5px; width:10px; height:10px;">V</span>
                  </div>
                </label>
                <div class="summary-text" v-bind:class="status.cssId" v-bind:for="status.cssId">
                  <div v-bind:class="status.cssId" v-bind:for="status.cssId" style="border:none; color:'#D8D8D8' !important; display:flex; width:27.7rem; justify-content:space-between; align-items:flex-end;">
                    <div>
                      <h1 style="display: inline; color:'#D8D8D8'; font-family: Helvetica; font-size:3rem; font-weight:500;" v-bind:class="status.cssId" v-bind:for="status.cssId">
                        {{ status.totalTenants }}
                      </h1>
                      <h2 style="display: inline; color:'#D8D8D8'; font-family:'Source Sans Pro'; font-size:2.47rem;" v-bind:class="status.cssId" v-bind:for="status.cssId">employed </h2>
                    </div>
                    <div class="summary-bar-chart-container" v-if="linechartShow" style="align-self:flex-end;">
                      <bar-chart id="bar-chart" class="barChart" :chart-data="barChartData[status.name]" :options="barchartOption"></bar-chart>
                    </div>
                  </div>
                  <div v-bind:class="status.cssId" v-bind:for="status.cssId" style="text-align:left; border:none; color:'#D8D8D8';">
                    <h2 style="font-family:'Source Sans Pro'; font-size:1.4rem; font-weight:300;">in {{status.name}}</h2>
                  </div>
                </div>
            </div>
          </div>
        </column>
        <!-- Status summary end-->
        <!-- Demo summary -->
        <column :lg="3.84" class="summary-area" v-if="stackedChartShow && viewMode === 'Demo'">
          <div class="summary-wrapper" v-for="status in summaryBoxData" v-bind:key="status.vForId" :value="status.vForId">
            <div class="text-container">
              <input type="checkbox" v-bind:class="status.cssId" v-bind:id="status.cssId" v-bind:key="status.vForId" :value="status.vForId" v-model="checkedItems" style="display:none">
                <label v-bind:class="status.cssId" v-bind:for="status.cssId">
                  <div v-bind:class="status.cssId" v-bind:for="status.cssId" style="justify-content:center; align-item:center;">
                    <span v-bind:class="status.cssId" v-bind:for="status.cssId" style="color:#ffffff; margin:2px 2px 2px 5px; width:10px; height:10px;">V</span>
                  </div>
                </label>        
                <div class="summary-text" v-bind:class="status.cssId" v-bind:for="status.cssId" >
                  <div v-bind:class="status.cssId" v-bind:for="status.cssId" style="border:none; color:'#D8D8D8' !important; padding: 0;">
                      <h2 v-bind:class="status.cssId" v-bind:for="status.cssId" style="font-size: 2rem;">
                        {{ status.vForId }}
                      </h2>
                  </div>
                </div>
            </div>
          </div>
        </column>
        <!-- Demo summary end -->
        <column :lg="3.44" class="summary-grid-area" v-if="stackedChartShow && viewMode === 'Status'">
          <row v-for="i in setNoOfRows" v-bind:key="i">
            <column v-for="j in [0, 1]" v-bind:key="i*2+j" :lg="6" :xs="6">
              <div v-if="i*2+j < summaryBoxData.length" class="summary-grid">
                <input type="checkbox" style="display:none" v-bind:id="summaryBoxData[i*2+j].cssId" v-bind:class="summaryBoxData[i*2+j].cssId" v-bind:value="summaryBoxData[i*2+j].name" v-model="checkedItems">
                <label v-bind:class="summaryBoxData[i*2+j].cssId" v-bind:for="summaryBoxData[i*2+j].cssId" id="checkbox-label">
                  <div v-bind:class="summaryBoxData[i*2+j].cssId" style="justify-content:center; align-item:center;">
                    <span v-bind:class="summaryBoxData[i*2+j].cssId" style="color: #ffffff; margin:2px 2px 2px 2.5px; width:10px; height:10px;">V</span>
                  </div>
                </label>
                <div v-bind:class="summaryBoxData[i*2+j].cssId" id="checkbox-text-area">
                  <h1>{{summaryBoxData[i*2+j].monthlyData.tenants[13]}}</h1>
                  <h3>{{summaryBoxData[i*2+j].name}}</h3>
                </div>
                </div>
            </column>
          </row>
          </column>                
      </row>
      <Table :tableData="tableData" v-if="linechartShow === true && viewMode !=='Fund'"></Table>
      <Table :tableData="tableDataByStatus" v-if="stackedChartShow === true && viewMode === 'Status' || linechartShow === true && viewMode ==='Fund'"></Table>
      <table-for-topic :TopicTableData="TopicTableData" v-if="viewMode === 'Demo'"></table-for-topic>
    </section>
  </main>
</template>

<script>
import LineChart from '../components/Chart/LineChart.js'
import BarChart from '../components/Chart/BarChart.js'
import StackedBarChart from '../components/Chart/StackedBarChart.js'
import AimDoughnutChart from '../components/Chart/AimDoughnutChart.vue'
import TimeDoughnutChart from '../components/Chart/TimeDoughnutChart.vue'
import Table from '../components/Table'
import TableForTopic from '../components/TableforTopic'
import { getValueByDemo, setYearSelectBox, getFundNames, getTenantsNumber, getTenantsByTopics, getTotalTenantsByStatus } from '../data/data-provider.js'
import { getAllPurpleColor, getLineChartColorScheme, getSkillsGroupBarChartColorSheme, getTopicColorSchme } from '../data/colour-scheme.js'
import { getRandomInt, getMonthlyColumn, calcSum, compareDataByYear, getLineChartData, getTableData, getBarChartData, getStackedBarChartData } from '../data/data-handler'

export default {
  components: {
    LineChart,
    BarChart,
    Table,
    TableForTopic,
    StackedBarChart,
    AimDoughnutChart,
    TimeDoughnutChart
  },
  data () {
    return {
      viewMode: 'All',
      selectedStatus: null,
      selectedFund: null,
      selectedDemo: null,
      selectedYear: 2020,
      chartData: {},
      linechartShow: true,
      groupBarchartShow: false,
      stackedChartShow: false,
      fundSelectboxDisabled: true,
      demoSelectboxDisabled: true,
      barChartData: [],
      stackedBarChartData: {},
      doughnutChartData1: {
        cssId: 'aim-chart',
        title: 'Aim',
        subtitle1: '1200',
        subtitle2: 'employed',
        subtitle2Position: [29, 36.2, 60],
        subtitle3: 'full time',
        percentage: '80',
        insideText: 'complete',
        color: '#8954BA'
      },
      doughnutChartData2: {
        box: 'box2',
        title: 'Time',
        subtitle1: '2 years',
        subtitle2: '',
        percentage: '1',
        insideText: 'more year',
        color: '#0091FF',
        totalYear: 3
      },
      tableData: [],
      tableDataByStatus: [],
      TopicTableData: [],
      summaryBoxData: [],
      yearOptions: [],
      employmentStatus: [
        'Full time - 35+ hours/week',
        '24+ hours/week',
        '16+ hours/week',
        '16- hours/week',
        'Zero hours contract',
        'Unemployed',
        'Unemployed - not eligible'],
      funds: [
        "RLPF1 - St Mungo’s",
        "RLPF2 - St Mungo’s",
        "NHPF 1 - Oxford",
        "NHPF 1 - Bristol",
        "NHPF 1 - Lancaster",
        "NHPF 1 - Manchester"],
      demographics: [
      'Age at survey date',
      'Benefits',
      'Households with children',
      'Tenants with disabilities',
      'Ethnic origin',
      'Gender'],
      status: '',
      fund: '',
      demo: '',
      checkedItems: [],
      totalTenants: '',
      growthRate: '',
      fundPlaceholder: 'Select status to activate',
      colorIndex: [],
      options: {
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          xAxes: [{
            gridLines: {
              color: '#ffffff'
            },
            ticks: {
              callback: function (value, index) {
                const months = getMonthlyColumn()
                value = months[index]
                return value
              }
            }
          }],
          yAxes: [{
            ticks: {
              beginAtZero: true
            }
          }]
        },
        legend: {
          labels: {
            boxWidth: 10,
            fontSize: 14,
            padding: 20,
            usePointStyle: true
          }
        },
        tooltips: {
          callbacks: {
            title: function (tooltipItem, data) {
              const months = getMonthlyColumn()        
              return data.datasets[0].label + ' | ' + months[tooltipItem[0].index]
            },
            label: function (tooltipItem) {
              return tooltipItem.value + ' Tenants'
            }
          }
        }
      },
      barchartOption: {
        animation: {
          duration: 0
        },
        scales: {
          xAxes: [{
            display: false
          }],
          yAxes: [{
            display: false
          }]
        },
        legend: {
          display: false
        },
        tooltips: {
          callbacks: {
            title: function (tooltipItem) {
              const months = getMonthlyColumn()          
              return months[tooltipItem[0].index]
            }
          }
        }
      },
      stackedBarchartOption: {
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          xAxes: [{
            stacked: true,
            gridLines: {
              color: '#ffffff'
            },
            ticks: {
              callback: function (value, index) {
                if (value.length < 3) {
                  value = getMonthlyColumn()[index]
                  return value
                } else {
                  return value
                }
              }
            }
          }],
          yAxes: [{
            stacked: true,
            suggestedMin: true,
            ticks: {
              beginAtZero: true
            }
          }]
        },
        legend: {
          labels: {
            boxWidth: 10
          },
          display: true
        },
        tooltips: {
          callbacks: {
            title: function (tooltipItem) {
              const month = getMonthlyColumn()
              return month[tooltipItem[0].index]
            }
          }
        }        
      },
      doughnutChartOption: {
        animation: {
          duration: 0
        },
        scales: {
          xAxes: [{
            display: false
          }],
          yAxes: [{
            display: false
          }]
        },
        legend: {
          display: false
        },
        tooltips: {
          callbacks: {
            title: function (tooltipItem) {
              const months = getMonthlyColumn()         
              return months[tooltipItem[0].index]
            }
          }
        }
      }
    }
  },
  mounted () {
    this.showNavBar()
    this.yearOptions = setYearSelectBox() // Set initial Year select box options        
    this.updateData()
  },
  methods: {
    showNavBar () {
      const navbar = document.getElementById('nav')
      navbar.style.display = 'inline'
    },
    updateData () {
      this.updateConditionalRendering()
      let tenants = {}
      let prevYearTenants = {}
      let totalCurrTenants = []
      let totalPrevTenants = []
      let tableTenants = {}
      let prevtableTenants = {}
      let summaryboxTenants = {}
      let prevsummaryboxTenants = {}
      const dummyBarchartData = {
        datasets: [
          {backgroundColor: [
            "rgb(232, 79, 137)",
            "rgb(47, 185, 239)",
            "rgb(103, 182, 117)",
            "rgb(247, 101, 17)",
            "rgb(28, 184, 196)",
            "rgb(247, 181, 0)"
            ],
          barThickness: 15,
          data: [getRandomInt(0 ,250), getRandomInt(0 ,250), getRandomInt(0 ,250), getRandomInt(0 ,250), getRandomInt(0 ,250), getRandomInt(0 ,250)]},          
        ],
        labels: [
          "RLPF1 - St Mungo’s",
          "RLPF2 - St Mungo’s",
          "NHPF 1 - Oxford",
          "NHPF 1 - Bristol",
          "NHPF 1 - Lancaster",
          "NHPF 1 - Manchester"
        ]
      }

      switch (this.viewMode) {
        case 'All':
          tableTenants = getTenantsNumber(this.employmentStatus, [], [], this.selectedYear, 'All')
          prevtableTenants = getTenantsNumber(this.employmentStatus, [], [], this.selectedYear - 1, 'All')
          if (this.checkedItems.length === 0) {
            tenants = getTenantsNumber([], [], [], this.selectedYear, 'All')
            prevYearTenants = getTenantsNumber([], [], [], this.selectedYear - 1, 'All')
            this.totalTenants = calcSum(Object.values(tenants.tenants[0]))
            this.growthRate = compareDataByYear(Object.values(prevYearTenants.tenants[0]), Object.values(tenants.tenants[0]))
            this.chartData = getLineChartData(tenants, getAllPurpleColor)
          } else {
            tenants = tableTenants
            this.chartData = this.filterChartData(getLineChartData(tenants, getLineChartColorScheme), this.checkedItems)
          }
          this.barChartData = getBarChartData(getTableData('Status', tableTenants, prevtableTenants))
          this.tableData = getTableData('Status', tableTenants, prevtableTenants)
          this.summaryBoxData = this.filterTopics(getTableData('Status', tableTenants, prevtableTenants))
          this.updateColors(this.viewMode, getLineChartColorScheme)
          break

        case 'Status':
          tableTenants = getTenantsNumber([this.selectedStatus], [], [], this.selectedYear, 'Status')
          prevtableTenants = getTenantsNumber([this.selectedStatus], [], [], this.selectedYear - 1, 'Status')
          if (this.checkedItems.length === 0) {
            tenants = getTotalTenantsByStatus(this.selectedStatus, this.selectedYear, 'Status')
            prevYearTenants = getTotalTenantsByStatus(this.selectedStatus, this.selectedYear - 1, 'Status')
            this.stackedBarChartData = dummyBarchartData
            this.tableDataByStatus = getTableData('Status', tableTenants, prevtableTenants)
          } else {
            tenants = getTenantsNumber([this.selectedStatus], getFundNames(this.selectedStatus), [], this.selectedYear, 'Status')
            prevYearTenants = getTenantsNumber([this.selectedStatus], getFundNames(this.selectedStatus), [], this.selectedYear - 1, 'Status')
            this.stackedBarChartData = this.filterBarChartData(dummyBarchartData)
          }
          summaryboxTenants = getTenantsNumber([this.selectedStatus], getFundNames(this.selectedStatus), [], this.selectedYear, 'Status')
          prevsummaryboxTenants = getTenantsNumber([this.selectedStatus], getFundNames(this.selectedStatus), [], this.selectedYear - 1, 'Status')
          this.summaryBoxData =  this.filterTopics(getTableData('Fund', summaryboxTenants, prevsummaryboxTenants))          
          totalCurrTenants = tenants.tenants.flatMap(el => Object.values(el))
          totalPrevTenants = prevYearTenants.tenants.flatMap(el => Object.values(el))
          this.totalTenants = calcSum(totalCurrTenants)
          this.growthRate = compareDataByYear(totalPrevTenants, totalCurrTenants)
          this.updateBarchartColor(getSkillsGroupBarChartColorSheme, this.colorIndex)
          break

        case 'Fund':
          this.linechartShow = true
            // status x / fund o : all employment status line graph + summary Area & Table by status
            if (this.selectedStatus === null && this.selectedFund !== null) {
              tableTenants = getTenantsNumber([this.selectedFund], this.employmentStatus, [], this.selectedYear, 'Fund')
              prevtableTenants = getTenantsNumber([this.selectedFund], this.employmentStatus, [], this.selectedYear -1, 'Fund')
              tenants = getTenantsNumber([this.selectedFund], [], [], this.selectedYear, 'Fund')
              prevYearTenants = getTenantsNumber([], [], [], this.selectedYear - 1, 'Fund')
              this.totalTenants = calcSum(Object.values(tenants.tenants[0]))
              this.growthRate = compareDataByYear(Object.values(prevYearTenants.tenants[0]), Object.values(tenants.tenants[0]))
              this.chartData = getLineChartData(tenants, getAllPurpleColor)         
              this.barChartData = getBarChartData(getTableData('Status', tableTenants, prevtableTenants))
              this.tableDataByStatus = getTableData('Status', tableTenants, prevtableTenants)
              this.summaryBoxData = this.filterTopics(getTableData('Status', tableTenants, prevtableTenants))
          } else if (this.selectedStatus && this.selectedFund) {
            // status o / fund o : selected employment status line graph + summary Area & Table by selected status
            tenants = getTenantsNumber([this.selectedFund], [this.selectedStatus], [], this.selectedYear, 'Fund')
            prevYearTenants = getTenantsNumber([this.selectedFund], [this.selectedStatus], [], this.selectedYear - 1, 'Fund')   
            totalCurrTenants = tenants.tenants.flatMap(el => Object.values(el))
            totalPrevTenants = prevYearTenants.tenants.flatMap(el => Object.values(el))
            this.totalTenants = calcSum(totalCurrTenants)
            this.growthRate = compareDataByYear(totalPrevTenants, totalCurrTenants)
            this.barChartData = getBarChartData(getTableData('Status', tenants, prevYearTenants))
            this.tableDataByStatus = getTableData('Status', tenants, prevYearTenants)
            this.chartData = this.filterChartData(getLineChartData(tenants, getLineChartColorScheme), this.checkedItems)
            this.summaryBoxData = this.filterTopics(getTableData('Status', tenants, prevYearTenants))
          }
          this.updateColors(this.viewMode, getLineChartColorScheme)
          break

        case 'Demo':
          // if status & fund & demographic options selected -> year data render
          // if status & demographic options selected -> fund data render
          if (this.selectedStatus && this.selectedFund && this.selectedYear) {
            tenants = getTenantsByTopics([this.selectedStatus], [this.selectedFund], [this.selectedDemo], this.selectedYear)
            prevYearTenants = getTenantsByTopics([this.selectedStatus], [this.selectedFund], [this.selectedDemo], this.selectedYear - 1)
          } else {
            tenants = getValueByDemo(this.selectedStatus, this.selectedDemo, this.selectedYear)
            prevYearTenants = getValueByDemo(this.selectedStatus, this.selectedDemo, this.selectedYear - 1)
          }
          totalCurrTenants = tenants.tenants.flatMap(el => Object.values(el))
          totalPrevTenants = prevYearTenants.tenants.flatMap(el => Object.values(el))
          this.totalTenants = calcSum(totalCurrTenants)
          this.growthRate = compareDataByYear(totalPrevTenants, totalCurrTenants)
          this.stackedBarChartData = this.filterChartData(getStackedBarChartData(tenants, getTopicColorSchme), this.checkedItems)
          this.TopicTableData = getTableData('Topics', tenants, prevYearTenants)
          this.summaryBoxData = this.filterTopics(getTableData('Topics', tenants, prevYearTenants)) // for checkbox rendering
          this.updateColors(this.viewMode, getTopicColorSchme)
          break
      }
    },
    updateConditionalRendering () {
      switch (this.viewMode) {
        case 'All':
          this.linechartShow = true
          this.stackedChartShow = false
          this.fundPlaceholder = 'Show all'
          this.fundSelectboxDisabled = false
          this.demoSelectboxDisabled = true
          this.selectedFund = null
          this.selectedDemo = null
          this.status = '- all, across funds'
          this.fund = ''
          this.demo = ''
          break
        case 'Status':
          this.linechartShow = (this.selectedStatus === null) ? true : false
          this.stackedChartShow = (this.selectedStatus !== null) ? true : false
          this.fundPlaceholder = 'Show All'
          this.fundSelectboxDisabled = false
          this.demoSelectboxDisabled = false
          this.stackedBarchartOption.legend.display = false          
          this.selectedFund = null
          this.selectedDemo = null
          this.status = '- ' + this.selectedStatus
          this.fund = ''
          this.demo = ''
          break
        case 'Fund':
          this.linechartShow = true
          this.stackedChartShow = false
          this.fundSelectboxDisabled = false
          this.demoSelectboxDisabled = false
          this.selectedStatus = (this.selectedFund === 'null') ? null : this.selectedStatus
          this.selectedDemo = null
          this.fund = ', ' + this.selectedFund
          this.demo = ''
          break
        case 'Demo':
          this.linechartShow = false
          this.stackedChartShow = true
          this.fundSelectboxDisabled = false
          this.demoSelectboxDisabled = false
          this.stackedBarchartOption.legend.display = true
          this.demo = ', ' + this.selectedDemo
          break
      }
    },
    filterTopics (tableData) {
      const filtered = tableData.filter(el => el.totalTenants !== 0)
      return filtered
    },
    uncheckAllCheckboxes () {
      for (let i = 0; i < this.checkedItems.length; i++) {
        const checkedItem = this.checkedItems[i]
        const cssId = checkedItem.toLowerCase().replaceAll(' ', '-')
        const dom = document.getElementsByClassName(`${cssId}`)
        dom[0].checked = false
      }
      this.checkedItems = []
    },
    filterChartData (chartData, filter) {
      if (filter.length !== 0) {
        const filtered = chartData.datasets.filter(el => filter.indexOf(el.label) !== -1)
        chartData.datasets = filtered
      }
      return chartData
    },
    filterBarChartData (chartData) {
      if (this.checkedItems.length === 0) {
        return chartData
      } else {
        const checked = this.checkedItems
        const colorIndex = []
        checked.forEach(el => {
          colorIndex.push(chartData.labels.indexOf(el))
        })
        this.colorIndex = colorIndex
      
        const dataIndex = [...Array(chartData.labels.length).keys()].filter(el => colorIndex.includes(el) === false)
        dataIndex.forEach(el => {
          chartData.datasets[0].data.splice(el, 1, '-')
          chartData.labels.splice(el, 1, '-')
        })
        return chartData
      }
    },
    updateBarchartColor (colorScheme, colorIndex) {
      let haveSet = 0
      colorIndex.sort((a, b) => a - b)
      for (let i = 0; i < this.summaryBoxData.length; i++) {
        const school = this.summaryBoxData[i].cssId
        const dom = document.getElementsByClassName(`${school}`)
        if (dom.length !== 0 && dom[0].checked === true) {
          const setColor = colorScheme().normal[colorIndex[haveSet]]
          dom[1].style.color = setColor
          dom[2].style.border = `1px solid ${setColor}`
          dom[3].style.color = setColor
          dom[4].style.color = setColor
          haveSet += 1
        } else if (dom.length !== 0) {
          dom[1].style.color = '#D8D8D8'
          dom[2].style.border = `1px solid ${'#D8D8D8'}`
          dom[3].style.color = '#ffffff'
          dom[4].style.color = '#D8D8D8'
        }
      }
    },
    updateColors (view, colorScheme) {
      const tableFontDomIndex = view === 'Demo' ? 7 : 9
      for (let i = 0; i < this.summaryBoxData.length; i++) {
        const cssId = this.summaryBoxData[i].cssId
        const dom = document.getElementsByClassName(`${cssId}`)
        if (dom.length !== 0 && dom[0].checked === true) {
          const checkedColor = colorScheme(this.summaryBoxData[i].colorIndex)
          dom[1].style.color = checkedColor // label
          dom[2].style.border = `1px solid ${checkedColor}` // connected div to checkbox
          dom[3].style.color = checkedColor // V
          dom[4].style.color = checkedColor // Topic text
          dom[tableFontDomIndex].style.color = checkedColor // Table name
        } else if (dom.length !== 0 && !dom[0].checked) {
          dom[1].style.color = '#D8D8D8'
          dom[2].style.border = '1px solid #D8D8D8'
          dom[3].style.color = '#ffffff'
          dom[4].style.color = '#D8D8D8'
          dom[tableFontDomIndex].style.color = '#212529'
        }
      }
    }
  },
  watch: {
    checkedItems () {
      this.updateData()
    },
    selectedYear () {
      this.uncheckAllCheckboxes()
      this.updateData()
    },
    selectedStatus () {
      if (this.selectedStatus === null) {
        this.viewMode = 'All'
        this.uncheckAllCheckboxes()
        this.updateData()
      } else {
        this.viewMode = 'Status'
        this.uncheckAllCheckboxes()
        this.updateData()
      }
    },
    selectedFund () {
      if (this.selectedFund === null) {
        this.viewMode = 'All'
        this.uncheckAllCheckboxes()
        this.updateData()
      } else {
        this.viewMode = 'Fund'
        this.uncheckAllCheckboxes()
        this.updateData()
      }
    },
    selectedDemo () {
      if (this.selectedFund !== null & this.selectedDemo === null) {
        this.viewMode = 'Fund'
      } else if (this.selectedFund === null & this.selectedDemo === null) {
        this.viewMode = 'All'
      } else {
        this.viewMode = 'Demo'
      }
        this.uncheckAllCheckboxes()
        this.updateData()
    }
  },
  computed: {
    setNoOfRows () {
      return Array(Math.ceil(this.summaryBoxData.length / 2)).keys()
    }
  }
}
</script>

<style>
main#employment {
  display: flex;
  flex-direction: column;
  margin: 6.2rem 0 0 6.2rem;
  padding: 8rem 4.5rem 4.5rem 8rem;
  max-width: 1440px;
}

.navbar {
  background: white;
}

.back {
  padding: 1.5rem 0 1.5rem 0;
}

.title {
  font-family: Helvetica;
  font-size: 3.4rem;
  color: var(--color-purple);
  font-weight: 300;
  text-align: left;
}

#page-title {
  display: flex;
  flex-direction: column;
  align-self: left;
}

#page-title .container {
  max-width: 125.5rem !important;
  padding-right: 1.2rem !important;
}

.doughnut-1 {
  padding-right: 4.2825rem;
}

.doughnut-2 {
  padding-left: 4.2825rem;
}

.progress-summary{
  display: flex;
  flex-direction: row;
  justify-content: flex-end;
}

.back {
  padding-bottom: 3rem;
  align-items: center;
}

.back a {
  color: var(--color-dark-grey);
  font-size: 1.2rem;
}

.title {
  padding-bottom: 2rem;
}

/* select box area start */
.employment-select-area {
  max-width: 125.5rem !important;
}

.employment-select-area .container {
  max-width: 125.5rem !important;
  align-items: center;
}

.employment-select-area h3 {
  font-size: 1.4rem;
  color: #858585;
  margin: 0;
}

.employment-select-country {
  margin: 0;
  padding: 0;
  text-align: left;
}

.employment-select-camp {
  text-align:right;
  margin: 0 1rem 0 0
}

.employment-select-school {
  text-align:right;
  margin:0 1rem 0 0;
}

/* select box area end */

/* selectbox design customizing start */
#employment .employment-select-area .vs__dropdown-toggle {
  width: 24.2rem;
  height: 3.9rem;
  background-color: #ffffff;
  border: none;
  font-size: 1.68rem;
  color: #686868;
  padding-left: 1rem;
}

#employment .employment-select-area .vs__dropdown-menu {
  background-color: #ffffff;
  box-shadow: none;
  border: none;
  border-radius: 2px;
  font-size: 1.68rem;
  width: 24.2rem !important;
  color: #686868;
}

#employment .employment-select-area .vs--disabled .vs__dropdown-toggle {
  background-color: rgba(255, 255, 255, 0.40);
  font-size: 14px;
  color: rgba(104,104,104,0.40) !important; 
}

#employment .employment-select-area .vs--disabled .vs__search {
  background-color: rgba(255, 255, 255, 0.40);
  font-size: 14px;
  color: rgba(104,104,104,0.40) !important; 
}

#employment .year-select-box .vs__dropdown-toggle {
  background-color: #ffffff;
  border: none;
  font-size: 1.4rem;
  min-width: 12rem;
  padding-left: 1rem;
}

#employment .year-select-box .vs__dropdown-menu {
  background-color:  #ffffff;
  box-shadow: none;
  border: none;
  border-radius: 2px;
  font-size: 1.4rem;
  min-width: 12rem !important;
  color: #686868;
}

#employment .vs__search {
  margin: 0;
  padding: 0;
  color: #686868;    
}

#employment .vs__selected {
  margin: 0;
  padding: 0;
  color: #686868;  
}
/* selectbox design customizing end */

.chart-title-area {
  display: flex;
  color: var(--color-purple);
  margin-top: 3rem;
}

.employment-sub-title {
  font-size: 2.88rem;
  font-family: 'Source Sans Pro';
  font-weight: 300 !important;
}

.chart-summary {
  display: flex;
  flex-direction: row;
}

.growth-rate {
  margin-left: 2rem;
}

.growth-rate h3 {
  font-size: 1rem;
  font-weight: 100;
}

.total-tenants {
  display: flex;
  flex-direction: column;
  padding-left: 7rem;
  padding-right: 2rem;
}

.total-tenants span {
  align-self: start;
}

.total-tenants h1,
.total-tenants h2,
.total-tenants h3 {
  display: inline;
  color: var(--color-purple);
}

.total-tenants h1 {
  margin-right: 1rem;
  font-size:3rem;
  font-weight: 500;
}

.total-tenants h2 {
  font-size: 2.2rem;
  font-weight: 300;
}

.total-tenants h3 {
  text-align: left;
  font-size: 1.4rem;
  font-weight: 100;
}

.growth-rate h1 {
  font-size: 3rem;
  font-weight: 500;
}

.growth-rate h3 {
  font-size: 1.4rem;
}

.container {
  max-width: 125.5rem !important;
  padding: 0 !important;
  margin: 0 !important;
}

#chart-area {
  margin-top: 4rem;
  padding: 0 2rem 3.5rem 2rem;
  max-width: 125.3rem;
  background-color: #ffffff;
}

.chart-title {
  display: flex;
  padding: 0 !important;
}

.chart-title h2 {
  text-align:left;
  margin: 0;
  color: var(--color-purple)
}

.line-chart-area {
  padding: 0 0 0 0 !important;
  display: flex;
  flex-direction: column;
  border-right: 1px solid var(--color-light-grey);
}

.data-v-300908ca .chartjs-size-monitor {
  position: relative !important;
}

canvas#line-chart.chartjs-render-monitor {
  padding: 0 1.8rem 0 2rem;
  margin: 0 0 0 0 !important;
  max-width: 80rem !important;
  max-height: 43.8rem !important;
}

.line-chart-area h3 {
  font-family: Source Sans Pro;
  font-size: 1.2rem;
  letter-spacing: -0.01px;
  text-align: left;
  margin: 2.5rem 0 1.5rem 2.5rem;
}

#employment .summary-grid-area,
#employment .summary-area {
  display: flex;
  flex-direction: column;
  max-height: 40rem;
  overflow-y: auto;
  border: none;
  align-items: left;
  margin: 8rem 0 5rem 0;
}

#employment .summary-grid-area label div {
  display:flex;
  width:18px;
  height:18px;
  background:white;
  border:1px solid var(--color-light-grey);
  cursor:pointer;
  border-radius: 3px;
  margin: 0 2rem 0 2rem;
}

#employment .summary-area::-webkit-scrollbar {
  width: 4px;
  height: 355px;
  background-color: rgba(216, 216, 216, 0.4);
}

#employment .summary-area::-webkit-scrollbar-thumb {
  width: 4px;
  height: 60px;
  background-color: #d8d8d8;
}

#employment .summary-grid {
 display: inline-flex;
  align-items: center;
  margin-bottom : 1.5rem;
  width: 100%;
  color: #d8d8d8;
  padding-left: 2.5rem;
}

#employment .summary-grid h1 {
  font-size: 2.5rem;
  font-family: Helvetica;
  font-weight: 500;
}

#employment .summary-grid h3 {
  font-size: 1rem;
  font-family:'Source Sans Pro'; 
  font-weight:300;
}

#checkbox-text-area {
  display: flex;
  flex-direction: column;
  text-align: left;
}

.text-container {
  display: flex;
  /* height: 80%; */
  width: 100%;
}

.summary-text{
  color: #D8D8D8;
  width: 100%;
}

.summary-text h1 {
  margin: 0 0 0 0;
}

.summary-bar-chart-container {
  display: flex;
  padding-left: 1.5rem;
}

.year-select-box {
  align-self: flex-end;
  margin: 1rem 3.5rem 0 0;
}

.summary-area #bar-chart #bar-chart {
  width: 100px !important;
  height: 50px !important;
}

#stacked-bar-chart canvas#bar-chart.chartjs-render-monitor {
  padding: 0 1.3rem 0 2rem;
  width: 80rem !important;
  height: 43.8rem !important;
}

.summary-wrapper {
  display: flex;
  align-items: center;
  height: 8rem;
  width: 35rem;
  padding: 1rem 0 1rem 2.75rem;
}

.text-container {
  display: flex;
  flex-direction: row;
  text-align: left;
  color: var(--color-light-grey);
  align-items: center;
}

.text-container label div {
  display:flex;
  width:2rem;
  height:2rem;
  background:white;
  border:1px solid var(--color-light-grey);
  cursor:pointer;
  border-radius: 3px;
  margin: 0 1rem 0 0;
}
</style>
