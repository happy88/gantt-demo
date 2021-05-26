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
    <div class="bottom">
      <div class="gstc-wrapper" ref="gstc"></div>
      <div class="right">
        {{itemData}}
      </div>
    </div>

  </div>
</template>
<script>
import GSTC from 'gantt-schedule-timeline-calendar';
import { createPopper } from '@popperjs/core';
import { Plugin as TimelinePointer } from 'gantt-schedule-timeline-calendar/dist/plugins/timeline-pointer.esm.min.js';
import { Plugin as Selection } from 'gantt-schedule-timeline-calendar/dist/plugins/selection.esm.min.js';
import { Plugin as ItemResizing } from 'gantt-schedule-timeline-calendar/dist/plugins/item-resizing.esm.min.js';
import { Plugin as ItemMovement } from 'gantt-schedule-timeline-calendar/dist/plugins/item-movement.esm.min.js';
import tippy from 'tippy.js';
import 'tippy.js/dist/tippy.css';
import 'gantt-schedule-timeline-calendar/dist/style.css';
const GSTCID = GSTC.api.GSTCID;
const date = GSTC.api.date;

let gstc,state;
const rowsFromDB = [
  {
    id: "1",
    workshop: '组装',
    lineEquipment: "线体01",
  },
  {
    id: "2",
    workshop: '组装',
    lineEquipment: "线体02",
  },
  {
    id: "3",
    workshop: '组装',
    lineEquipment: "线体03",
  },{
    id: "4",
    workshop: '冲压',
    lineEquipment: "机台01",
  },{
    id: "5",
    workshop: '冲压',
    lineEquipment: "机台02",
  },{
    id: "6",
    workshop: '冲压',
    lineEquipment: "机台03",
  }
];

const itemsFromDB = [
  {
    id: "1",
    rowId: "1",
    label: "WORK1-1",
    time: {
      start: date('2021-05-26').startOf('day').valueOf(),
      end: date('2021-05-28').endOf('day').valueOf(),
    },
    style: {  // 每个块的样式
      background: 'blue'
    }
  },
  {
    id: "2",
    rowId: "1",
    label: "WORK1-2",
    time: {
      start: date('2021-05-29').startOf('day').valueOf(),
      end: date('2021-06-07').endOf('day').valueOf(),
    }
  },
  {
    id: '3',
    label: 'Item 3',
    rowId: '2',
    time: {
      start: date('2020-01-15').startOf('day').valueOf(),
      end: date('2020-01-20').endOf('day').valueOf(),
    },
  },
  {
    id: "4",
    rowId: "2",
    label: "WORK2-1ddd",
    time: {
      start: date('20210526').startOf('day'),
      end: date('20210526').endOf('day') + 5 * 24 * 60 * 60 * 1000
    },
    style: {  // 每个块的样式
      background: 'blue'
    }
  }
];

const columnsFromDB = [
  {
    id: "id",
    // data: "id",
    data: ({ row }) => GSTC.api.sourceID(row.id), // show original id (not internal GSTCID)
    sortable: ({ row }) => Number(GSTC.api.sourceID(row.id)), // sort by id converted to number
    width: 80,
    header: { content: "序号" }
  },
  {
    id: "workshop",
    data: "workshop",
    width: 100,
    expander: true,
    header: { content: "车间" }
  },
  {
    id: "lineEquipment",
    data: "lineEquipment",
    width: 100,
    header: { content: "线别/设备" }
  }
];

/**
 * Convert data from array into GSTC object
 */
function fromArray (array) {
  const resultObj = {};
  for (const item of array) {
    item.id = GSTCID(item.id);
    if ('rowId' in item) {
      item.rowId = GSTCID(item.rowId);
    }
    if ('parentId' in item) {
      item.parentId = GSTCID(item.parentId);
    }
    resultObj[item.id] = item;
  }
  return resultObj;
}
// Item slot
function itemSlot (vido,props) {
  const { onChange,update,html,api,getElement } = vido;

  // Get element and initialize tippy instance
  let element,tippyInstance;
  function initialize (el) {
    element = el;
    // @ts-ignore
    if (!tippyInstance) tippyInstance = tippy(element);
  }

  let itemData,startDate,endDate,tooltipContent;
  onChange((newProps) => {
    props = newProps;
    if (!props) return;
    itemData = api.getItemData(props.item.id);
    startDate = itemData.time.startDate;
    endDate = itemData.time.endDate;
    tooltipContent = `${props.item.label} from ${startDate.format(
      'YYYY-MM-DD'
    )} to ${endDate.format('YYYY-MM-DD')}`;

    // render the view and after that set tippy content
    update(() => {
      tippyInstance.setContent(tooltipContent);
    });
  });

  return (content) =>
    html`<div
      directive=${getElement(initialize)}
      class="my-item"
      style="width:100%;display:flex;"
    >
      ${content}
    </div>`;
}
/**
 * 默认展示三个月时间
 */
export default {
  name: 'GSTC',
  data () {
    return {
      itemData: {}
    }
  },
  mounted () {
    const config = {
      licenseKey: '====BEGIN LICENSE KEY====\nXOfH/lnVASM6et4Co473t9jPIvhmQ/l0X3Ewog30VudX6GVkOB0n3oDx42NtADJ8HjYrhfXKSNu5EMRb5KzCLvMt/pu7xugjbvpyI1glE7Ha6E5VZwRpb4AC8T1KBF67FKAgaI7YFeOtPFROSCKrW5la38jbE5fo+q2N6wAfEti8la2ie6/7U2V+SdJPqkm/mLY/JBHdvDHoUduwe4zgqBUYLTNUgX6aKdlhpZPuHfj2SMeB/tcTJfH48rN1mgGkNkAT9ovROwI7ReLrdlHrHmJ1UwZZnAfxAC3ftIjgTEHsd/f+JrjW6t+kL6Ef1tT1eQ2DPFLJlhluTD91AsZMUg==||U2FsdGVkX1/SWWqU9YmxtM0T6Nm5mClKwqTaoF9wgZd9rNw2xs4hnY8Ilv8DZtFyNt92xym3eB6WA605N5llLm0D68EQtU9ci1rTEDopZ1ODzcqtTVSoFEloNPFSfW6LTIC9+2LSVBeeHXoLEQiLYHWihHu10Xll3KsH9iBObDACDm1PT7IV4uWvNpNeuKJc\npY3C5SG+3sHRX1aeMnHlKLhaIsOdw2IexjvMqocVpfRpX4wnsabNA0VJ3k95zUPS3vTtSegeDhwbl6j+/FZcGk9i+gAy6LuetlKuARjPYn2LH5Be3Ah+ggSBPlxf3JW9rtWNdUoFByHTcFlhzlU9HnpnBUrgcVMhCQ7SAjN9h2NMGmCr10Rn4OE0WtelNqYVig7KmENaPvFT+k2I0cYZ4KWwxxsQNKbjEAxJxrzK4HkaczCvyQbzj4Ppxx/0q+Cns44OeyWcwYD/vSaJm4Kptwpr+L4y5BoSO/WeqhSUQQ85nvOhtE0pSH/ZXYo3pqjPdQRfNm6NFeBl2lwTmZUEuw==\n====END LICENSE KEY====',
      plugins: [TimelinePointer(),Selection(),ItemResizing(),ItemMovement()],
      list: {
        columns: {
          data: fromArray(columnsFromDB),
        },
        rows: fromArray(rowsFromDB),
      },
      chart: {
        time: {
          from: new Date().getTime() - 10 * 24 * 60 * 60 * 1000,
          to: new Date().getTime() + 30 * 24 * 60 * 60 * 1000,
          zoom: 22,
        },
        items: fromArray(itemsFromDB),
      },
      locale: {
        weekStart: 0,
        name: "zh",
        Now: "Now",
        weekdaysShort: ["日","一","二","三","四","五","六"],
        weekdays: ["周日","周一","周二","周三","周四","周五","周六"],
        months: ["一月","二月","三月","四月","五月","六月","七月","八月","九月","十月","十一月","十二月"],
      },
      actions: {
        'chart-timeline-items-row-item': [this.clickAction] // 监听右击事件
      },
      slots: {
        'chart-timeline-items-row-item': { inner: [itemSlot] },
      },
    };

    state = GSTC.api.stateFromConfig(config);

    gstc = GSTC({
      element: this.$refs.gstc,
      state,
    });
  },
  methods: {
    clickAction (element,data) {
      function onClick (event) {
        // data variable will be updated in update method below so it will be always actual
        // alert(`Event ${data.item.id} clicked!`);
        console.log(data);
        this.itemData = JSON.stringify(data.item);
        this.$nextTick(() => {
          console.log()
        })
      }

      element.addEventListener('click',onClick);

      return {
        update (element,newData) {
          data = newData; // data from parent scope updated
        },

        destroy (element,data) {
          element.removeEventListener('click',onClick);
        },
      };
    }

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
  .bottom {
    flex: 1;
    display: flex;
    .gstc-wrapper {
      flex: 1;
    }
    .right {
      width: 200px;
    }
  }
}
</style>