<template>
  <div class="wrapper">
    <div class="btns-wrapper">
      <button>刷新</button>
      <button>新增</button>
      <button>编辑</button>
      <button>删除</button>
      <button>保存</button>
      <button>合并</button>
      <button>拆分</button>
    </div>
    <div class="gstc-wrapper" ref="gstc">

    </div>
  </div>
</template>
<script>
import GSTC from 'gantt-schedule-timeline-calendar';
import moment from 'moment'
import { Plugin as TimelinePointer } from 'gantt-schedule-timeline-calendar/dist/plugins/timeline-pointer.esm.min.js';
import { Plugin as Selection } from 'gantt-schedule-timeline-calendar/dist/plugins/selection.esm.min.js';
import { Plugin as ItemResizing } from 'gantt-schedule-timeline-calendar/dist/plugins/item-resizing.esm.min.js';
import { Plugin as ItemMovement } from 'gantt-schedule-timeline-calendar/dist/plugins/item-movement.esm.min.js';
import 'gantt-schedule-timeline-calendar/dist/style.css';


let gstc,state;

function generateColumns () {
  const columns = {
    data: {
      id: {
        id: "id",
        data: "id",
        width: 50,
        header: { content: "序号" }
      },
      workshop: {
        id: "workshop",
        data: "workshop",
        width: 100,
        expander: true,
        header: { content: "车间" }
      },
      lineEquipment: {
        id: "lineEquipment",
        data: "lineEquipment",
        width: 100,
        header: { content: "线别/设备" }
      }
    }
  };
  return columns;
}
function generateRows () {
  const rows = {
    "1": {
      id: "1",
      workshop: '组装',
      lineEquipment: "线体01",
    },
    "2": {
      id: "2",
      workshop: '组装',
      lineEquipment: "线体02",
    },
    "3": {
      id: "3",
      workshop: '组装',
      lineEquipment: "线体03",
    },
    "4": {
      id: "4",
      workshop: '冲压',
      lineEquipment: "机台01",
    },
    "5": {
      id: "5",
      workshop: '冲压',
      lineEquipment: "机台02",
    },
    "6": {
      id: "6",
      workshop: '冲压',
      lineEquipment: "机台03",
    }
  };
  return rows;
}
function generateItems () {
  const items = {};
  return items;
}
/**
 * 默认展示三个月时间
 */
export default {
  name: 'GSTC',
  mounted () {
    const config = {
      licenseKey: '====BEGIN LICENSE KEY====\nXOfH/lnVASM6et4Co473t9jPIvhmQ/l0X3Ewog30VudX6GVkOB0n3oDx42NtADJ8HjYrhfXKSNu5EMRb5KzCLvMt/pu7xugjbvpyI1glE7Ha6E5VZwRpb4AC8T1KBF67FKAgaI7YFeOtPFROSCKrW5la38jbE5fo+q2N6wAfEti8la2ie6/7U2V+SdJPqkm/mLY/JBHdvDHoUduwe4zgqBUYLTNUgX6aKdlhpZPuHfj2SMeB/tcTJfH48rN1mgGkNkAT9ovROwI7ReLrdlHrHmJ1UwZZnAfxAC3ftIjgTEHsd/f+JrjW6t+kL6Ef1tT1eQ2DPFLJlhluTD91AsZMUg==||U2FsdGVkX1/SWWqU9YmxtM0T6Nm5mClKwqTaoF9wgZd9rNw2xs4hnY8Ilv8DZtFyNt92xym3eB6WA605N5llLm0D68EQtU9ci1rTEDopZ1ODzcqtTVSoFEloNPFSfW6LTIC9+2LSVBeeHXoLEQiLYHWihHu10Xll3KsH9iBObDACDm1PT7IV4uWvNpNeuKJc\npY3C5SG+3sHRX1aeMnHlKLhaIsOdw2IexjvMqocVpfRpX4wnsabNA0VJ3k95zUPS3vTtSegeDhwbl6j+/FZcGk9i+gAy6LuetlKuARjPYn2LH5Be3Ah+ggSBPlxf3JW9rtWNdUoFByHTcFlhzlU9HnpnBUrgcVMhCQ7SAjN9h2NMGmCr10Rn4OE0WtelNqYVig7KmENaPvFT+k2I0cYZ4KWwxxsQNKbjEAxJxrzK4HkaczCvyQbzj4Ppxx/0q+Cns44OeyWcwYD/vSaJm4Kptwpr+L4y5BoSO/WeqhSUQQ85nvOhtE0pSH/ZXYo3pqjPdQRfNm6NFeBl2lwTmZUEuw==\n====END LICENSE KEY====',
      plugins: [TimelinePointer(),Selection(),ItemResizing(),ItemMovement()],
      list: {
        columns: generateColumns(),
        rows: generateRows()
      },
      chart: {
        time: {
          from: new Date().getTime() - 30 * 24 * 60 * 60 * 1000,
          to: new Date().getTime() + 30 * 24 * 60 * 60 * 1000,
          zoom: 22,
        },
        items: generateItems()
      },
      locale: {
        weekStart: 0,
        name: "zh",
        Now: "Now",
        weekdaysShort: ["日","一","二","三","四","五","六"],
        weekdays: ["周日","周一","周二","周三","周四","周五","周六"],
        months: ["一月","二月","三月","四月","五月","六月","七月","八月","九月","十月","十一月","十二月"],
      }
    };


    state = GSTC.api.stateFromConfig(config);

    gstc = GSTC({
      element: this.$refs.gstc,
      state,
    });
  }
}
</script>
<style lang="scss" scoped>
.wrapper {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  .btns-wrapper {
    height: 50px;
  }
  .gstc-wrapper {
    flex: 1;
  }
}
</style>