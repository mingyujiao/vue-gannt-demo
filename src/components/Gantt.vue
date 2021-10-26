<template>
  <div ref="gantt"></div>
</template>

<script>
import 'dhtmlx-gantt'
import "dhtmlx-gantt/codebase/ext/dhtmlxgantt_tooltip.js"
import dayjs from "dayjs";

export default {
  name: 'gantt',
  props: {
    tasks: {
      type: Object,
      default () {
        return {data: [], links: []}
      }
    }
  },

  methods: {
    setScaleConfig(value) {
      gantt.attachEvent("onGanttReady", () => {
        let tooltips = gantt.ext.tooltips;
        tooltips.tooltip.setViewport(gantt.$task_data);
      })
      gantt.templates.tooltip_text = function (start, end, task) {
        if (task.text === '实际' || task.text === '计划') {
          return "<b>项目名称:</b> " + task.project_num
        }
        return "<b>阶段:</b> " + task.text + "<br/><b>开始时间:</b> "
            + dayjs(start).format('YYYY-MM-DD')
            + "<br/><b>结束时间:</b> "
            + dayjs(end).format('YYYY-MM-DD');
      };
      gantt.config.readonly = true // 只读禁止在甘特图上拖拽
      switch (value) {
        case "3":
          gantt.config.end_date= new Date(2022,0,1);
          gantt.config.scale_unit = 'month';
          gantt.config.step = 1;
          gantt.config.date_scale="%Y年%m月";
          gantt.templates.date_scale = null;
          break;
        case "4":
          gantt.config.end_date= new Date(2022,12,1);
          gantt.config.scale_unit = "year";
          gantt.config.step = 1;
          gantt.config.date_scale = "%Y年";
          gantt.templates.date_scale = null;
          break;
      }
      gantt.config.start_date= new Date(2021,0,1);

      gantt.config.columns = [
        { name: "project_num", label: "项目名称", width: 80,align: "center"},
        { name: "project_type", label: "项目负责人", width: 80, align: "center"},
        { name: "text", label: "备注", width: 100, align: "center" },
        // { name: "add", label: "" }, // 点击弹出添加框
      ];
      gantt.init(this.$refs.gantt);
      gantt.clearAll(); // 防止数据缓存问题
      gantt.parse(this.$props.tasks);

      this.$_initDataProcessor();
    },
    $_initGanttEvents: function() {
      if (!gantt.$_eventsInitialized) {
        gantt.attachEvent('onTaskSelected', (id) => {
          let task = gantt.getTask(id);
          this.$emit('task-selected', task);
        });

        gantt.attachEvent('onTaskIdChange', (id, new_id) => {
          if (gantt.getSelectedId() === new_id) {
            let task = gantt.getTask(new_id);
            this.$emit('task-selected', task);
          }
        });

        gantt.$_eventsInitialized = true;
      }
    },

    $_initDataProcessor: function() {
      if (!gantt.$_dataProcessorInitialized) {
        gantt.createDataProcessor((entity, action, data, id) => { 
          this.$emit(`${entity}-updated`, id, action, data);
        });

        gantt.$_dataProcessorInitialized = true;
      }
    }
  },

  mounted: function () {
    this.$_initGanttEvents();
    gantt.config.xml_date = "%Y-%m-%d";

    this.setScaleConfig('4')

    // gantt.config.scale_unit = 'month';
    // gantt.config.step = 1;
    // gantt.config.date_scale="%Y年%m月";
  }
}
</script>

<style>
    @import "~dhtmlx-gantt/codebase/dhtmlxgantt.css";
</style>