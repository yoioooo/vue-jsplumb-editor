<template>
  <el-container class="flowChartWrap">
    <el-main>
      <div class="mainContainer" @drop="dropHandle" @dragover="dragoverHandle">
        <div id="mainContainer" @click="clickBgHandle"></div>
      </div>
    </el-main>
  </el-container>
</template>
<script lang="ts">
import Vue from "vue";
import API from "./api/index";
import FlowChart from "./FlowChart/index";

export default Vue.extend({
  data() {
    return {
      isShowNode: false,
      currentNodeId: "",
      isUndoDisable: true,
      isExecDisable: false,
      nodeData: []
    };
  },
  mounted() {
    FlowChart.setContainer("mainContainer");
    FlowChart.on("commandListEmpty", () => {
      this.isUndoDisable = true;
    });
    FlowChart.on("showNodeData", () => {});
    FlowChart.on("addCommand", () => {
      this.isUndoDisable = false;
    });
    FlowChart.on("selectNode", (data: string) => {
      this.isShowNode = true;
      this.currentNodeId = data;
    });
    API.getFlowChartData().then((data: any) => {
      FlowChart.loadData(data.data);
    });
    API.getMenuData().then((data: any) => {
      this.nodeData = data.data;
    });
  },
  methods: {
    filterNode(value: string, data: any) {
      if (!value) return true;
      return data.label.indexOf(value) !== -1;
    },
    dragoverHandle(ev: Event) {
      ev.preventDefault();
    },
    dragHandle(ev: any) {
      ev.dataTransfer.setData("target", ev.target.id);
    },
    dropHandle(ev: any) {
      FlowChart.addNode({ pageX: ev.pageX, pageY: ev.pageY }, ev.dataTransfer.getData("target"));
    },
    clickBgHandle() {
      this.isShowNode = false;
    },
    zoomOut() {
      FlowChart.zoomOut();
    },
    zoomIn() {
      FlowChart.zoomIn();
    },
    undo() {
      FlowChart.undo();
    },
    execModel() {
      this.isExecDisable = true;
      FlowChart.execModel().then(() => {
        this.isExecDisable = false;
      });
    }
  }
});
</script>

<style lang="scss">
.flowChartWrap {
  height: 100%;
  .mainContainer {
    height: 100%;
    position: relative;
    overflow: hidden;
    outline: none !important;
    #mainContainer {
      outline: none !important;
      height: 100%;
      width: 100%;
      position: relative;
    }
  }
}
</style>
