<template>
  <div class="container">
    <button type="button" @click="clickDate('3')">月</button>
    <button type="button" @click="clickDate('4')">年</button>
    <gantt class="left-container" :tasks="tasks" @task-updated="logTaskUpdate" @link-updated="logLinkUpdate"
           @task-selected="selectTask" ref="gantt"></gantt>
  </div>
</template>

<script>
import Gantt from './components/Gantt.vue';

export default {
  name: 'app',
  components: {Gantt},
  data() {
    return {
      tasks: {
        data: [
          {
            id: 1, // 任务ID
            project_num: '测试项目一',
            project_type: '李四',
            text: "计划",
            start_date: "2021-01-01",
            type: "project",
            render: "split",
            parent: 0, //父任务ID  自己为父任务 ID为0
          },
          {
            id: 2,
            text: "计划时间",
            start_date: "2021-01-01", //开始时间
            end_date: "2021-12-12", //结束时间
            parent: 1,
          },
          {
            id: 6,
            project_num: '测试项目一',
            project_type: '张三',
            text: "实际",
            start_date: "2021-01-01",
            type: "project",
            render: "split",
            parent: 0,
          },
          {
            id: 7,
            text: "阶段一",
            start_date: "2021-01-01", //开始时间
            end_date: "2021-02-01", //结束时间
            parent: 6,
          },
          {
            id: 8,
            text: "阶段二",
            start_date: "2021-02-01",
            end_date: "2021-03-12",
            duration: 2,
            parent: 6,
            color: 'red'
          },
          {
            id: 9,
            text: "阶段三",
            start_date: "2021-03-12",
            end_date: "2021-06-12",
            duration: 2,
            parent: 6,
            color: 'green'
          },
          {
            id: 10,
            text: "阶段四",
            start_date: "2021-06-12",
            end_date: "2021-09-12",
            duration: 2,
            parent: 6,
            color: '#161256'
          },
          {
            id: 11, // 任务ID
            project_num: '测试项目二',
            project_type: '李四',
            text: "计划",
            start_date: "2021-06-01",
            type: "project",
            render: "split",
            parent: 0, //父任务ID  自己为父任务 ID为0
          },
          {
            id: 12,
            text: "计划时间",
            start_date: "2021-06-01", //开始时间
            end_date: "2022-06-12", //结束时间
            parent: 11,
          },
          {
            id: 13,
            project_num: '测试项目二',
            project_type: '李四',
            text: "实际",
            start_date: "2021-08-01",
            type: "project",
            render: "split",
            parent: 0,
          },
          {
            id: 14,
            text: "阶段一",
            start_date: "2021-08-01", //开始时间
            end_date: "2021-11-01", //结束时间
            parent: 13,
          },
          {
            id: 15,
            text: "阶段二",
            start_date: "2021-11-01",
            end_date: "2022-03-12",
            duration: 2,
            parent: 13,
            color: 'red'
          },
          {
            id: 16,
            text: "阶段三",
            start_date: "2022-03-12",
            end_date: "2022-10-12",
            duration: 2,
            parent: 13,
            color: 'green'
          },
        ],
      },
      selectedTask: null,
      messages: []
    }
  },
  methods: {
    clickDate(value) {
      this.$refs.gantt.setScaleConfig(value)
    },
    selectTask(task) {
      this.selectedTask = task
    },
    addMessage(message) {
      this.messages.unshift(message)
      if (this.messages.length > 40) {
        this.messages.pop()
      }
    },
    logTaskUpdate(id, mode, task) {
      let text = (task && task.text ? ` (${task.text})` : '')
      let message = `Task ${mode}: ${id} ${text}`
      this.addMessage(message)
    },

    logLinkUpdate(id, mode, link) {
      let message = `Link ${mode}: ${id}`
      if (link) {
        message += ` ( source: ${link.source}, target: ${link.target} )`
      }
      this.addMessage(message)
    }
  },
}
</script>

<style>
html, body {
  height: 100%;
  margin: 0;
  padding: 0;
}

.container {
  height: 100%;
  width: 100%;
}

.left-container {
  overflow: hidden;
  position: relative;
  height: 100%;
}

.right-container {
  border-right: 1px solid #cecece;
  float: right;
  height: 100%;
  width: 340px;
  box-shadow: 0 0 5px 2px #aaa;
  position: relative;
  z-index: 2;
}

.gantt-messages {
  list-style-type: none;
  height: 50%;
  margin: 0;
  overflow-x: hidden;
  overflow-y: auto;
  padding-left: 5px;
}

.gantt-messages > .gantt-message {
  background-color: #f4f4f4;
  box-shadow: inset 5px 0 #d69000;
  font-family: Geneva, Arial, Helvetica, sans-serif;
  font-size: 14px;
  margin: 5px 0;
  padding: 8px 0 8px 10px;
}

.gantt-selected-info {
  border-bottom: 1px solid #cecece;
  box-sizing: border-box;
  font-family: Geneva, Arial, Helvetica, sans-serif;
  height: 50%;
  line-height: 28px;
  padding: 10px;
}

.gantt-selected-info h2 {
  border-bottom: 1px solid #cecece;
}

.select-task-prompt h2 {
  color: #d9d9d9;
}

</style>
