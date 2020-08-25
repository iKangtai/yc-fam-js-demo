<template>
  <div>
    <div>Input</div>
    <json-viewer
      :value="alInput"
      :expand-depth=4
      copyable
      boxed>
    </json-viewer>
    <hr />
    <div>Output</div>
    <json-viewer v-if="jsonData"
      :value="jsonData"
      :expand-depth=4
      copyable
      boxed>
    </json-viewer>
  </div>
</template>

<script>
import JsonViewer from 'vue-json-viewer'
import YCFam from 'yc-fam-js'
import { v4 as uuidv4 } from 'uuid'

export default {
  components: {
    JsonViewer
  },
  data() {
    return {
      jsonData: {},
      // 100200/6e1b1049a9486d49ba015af00d5a0: 测试专用 appId 和 appSecret，正式上线时应改为孕橙分配的正式 appId 和 appSecret
      // 用户唯一标识符: luopk@ikangtai.com 为测试账号，正式上线时应改为真实的用户唯一标识符
      fam: new YCFam('100200', '6e1b1049a9486d49ba015af00d5a0', 'luopk@ikangtai.com'),
      alInput: {
        debug: 0,
        userData: {
          userAverageCycleLength: 34,
          userCycleLengthError: 1,
          userCycleRegularity: 1,
          userAverageMenstruationLength: 3,
          userAverageLuteumLength: 14
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
            menstruationRecord: 1
          },
          {
            impactTempFlag: 0,
            BBT: 0,
            ovulationResultByUser: 0,
            ovulationResultByLH: 0,
            cervicalMunusRecord: 0,
            timestamp: 1461988800,
            dayOfCycle: 0,
            menstruationRecord: 1
          }
        ]
      }
    }
  },
  mounted() {
    this.testFAMDays()
    setTimeout(() => {
      this.testFAMDays()
    }, 2000);
  },
  methods: {
    async testFAMDays() {
      try {
        const data = await this.fam.getFAMDays(uuidv4(), this.alInput)
        console.log(data)
        this.jsonData = data
      } catch (error) {
        console.error(error)
      }
    }
  }
}
</script>

<style>

</style>