<template>
  <div>
    <div>Input</div>
    <json-viewer :value="alInput" :expand-depth="4" copyable boxed></json-viewer>
    <hr />
    <div>Charts</div>
    <Chart :option="chartOption" style="height: 400px" />
    <hr />
    <div>Output</div>
    <json-viewer v-if="outputData" :value="outputData" :expand-depth="4" copyable boxed></json-viewer>
  </div>
</template>

<script>
import JsonViewer from "vue-json-viewer";
import YCFam from "yc-fam-js";
import { v4 as uuidv4 } from "uuid";
import Chart from "./Chart";
import dateFormat from "../utils/dateFormat";

export default {
  components: {
    JsonViewer,
    Chart,
  },
  data() {
    return {
      chartOption: {},
      outputData: {},
      // 100200/6e1b1049a9486d49ba015af00d5a0: 测试专用 appId 和 appSecret，正式上线时应改为孕橙分配的正式 appId 和 appSecret
      // 用户唯一标识符: luopk@ikangtai.com 为测试账号，正式上线时应改为真实的用户唯一标识符
      fam: new YCFam(
        "100200",
        "6e1b1049a9486d49ba015af00d5a0",
        "luopk@ikangtai.com"
      ),
      alInput: {
        debug: 0,
        param: { version: 1 },
        userData: {
          userAverageCycleLength: 34,
          userCycleLengthError: 1,
          userCycleRegularity: 1,
          userAverageMenstruationLength: 3,
          userAverageLuteumLength: 14,
        },
        daysInput: [
          {
            impactTempFlag: 0,
            BBT: 0,
            ovulationResultByUser: 0,
            ovulationResultByLH: 0,
            cervicalMunusRecord: 0,
            timestamp: 1461902400,
            dayOfCycle: 0,
            menstruationRecord: 10,
          },
          {
            impactTempFlag: 0,
            BBT: 0,
            ovulationResultByUser: 0,
            ovulationResultByLH: 0,
            cervicalMunusRecord: 0,
            timestamp: 1461988800,
            dayOfCycle: 0,
            menstruationRecord: 10,
          },
        ],
      },
    };
  },
  mounted() {
    this.testFAMDays();
    setTimeout(() => {
      this.testFAMDays();
    }, 2000);
  },
  methods: {
    getChartData() {
      let output = this.outputData.data.daysOutput
      let datas = output.map((dayItem) => {
        return [dateFormat(new Date(dayItem.date * 1000), "yyyy-MM-dd"), 0]
      });
      let pieces = []
      for (let i = 0; i < output.length; i++) {
        let outputI = output[i]
        if (outputI.pa === 1) {
          pieces.push({
            value: i,
            color: '#68A3FF',
          })
        } else if (outputI.pa === 3 || outputI.pa === 4) {
          pieces.push({
            value: i,
            color: '#ff7486',
          })
        }
      }
      let markData = []
      let preMark = []
      let preData = {}
      for (let i = 0; i < output.length; i++) {
        let outputI = output[i]
        if (outputI.pa === 1 || outputI.pa === 3 || outputI.pa === 4) {
          let color = ''
          if (outputI.pa === 1) {
            color = '#FF7486'
          } else if (outputI.pa === 3) {
            color = '#68A3FF1A'
          } else if (outputI.pa === 4) {
            color = '#68A3FF'
          }
          if (preMark && preData.pa === outputI.pa) {
            preMark[1].xAxis = datas[i+1][0]
          } else {
            let markI = [{
              itemStyle: { color: color },
              xAxis: datas[i][0]
            }, {
              xAxis: datas[i+1][0]
            }]
            markData.push(markI)
            preMark = markI
          }
        }
        preData = outputI
      }
      this.chartOption = {
        title: {
          text: "FAM 算法输出",
        },
        tooltip: {},
        xAxis: {
          type: 'category',
          boundaryGap: false
        },
        yAxis: {
          type: 'value',
          boundaryGap: [0, '30%']
        },
        visualMap: {
          type: "piecewise",
          show: false,
          dimension: 0,
          seriesIndex: 0,
          pieces: pieces,
        },
        series: [
          {
            name: '周期',
            type: 'line',
            smooth: 0.6,
            symbol: 'none',
            lineStyle: {
                color: 'green',
                width: 2
            },
            areaStyle: {},
            data: datas,
            markArea: {
              data: markData
            }
          },
        ],
      };
    },
    async testFAMDays() {
      try {
        const data = await this.fam.getFAMDays(uuidv4(), this.alInput);
        console.log(data);
        this.outputData = data;
        this.getChartData();
      } catch (error) {
        console.error(error);
      }
    },
  },
};
</script>

<style>
</style>