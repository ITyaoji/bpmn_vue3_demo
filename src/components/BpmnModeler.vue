<template>
  <div class="containers">
    <div ref="canvas" class="canvas"></div>
    <div id="properties" class="properties-panel-parent"></div>
  </div>
</template>

<script setup lang="ts">
import BpmnModeler from 'bpmn-js/lib/Modeler'
import { ref, onMounted, onBeforeUnmount } from 'vue'
import * as PropertiesPanelModule from '@bpmn-io/properties-panel'
import { xmlStr } from '@/mock/xmlStr'
import '@bpmn-io/properties-panel/dist/assets/properties-panel.css'
import 'bpmn-js/dist/assets/diagram-js.css'
import 'bpmn-js/dist/assets/bpmn-font/css/bpmn.css'

import {
  BpmnPropertiesPanelModule,
  BpmnPropertiesProviderModule,
  BpmnPropertiesProvider,
} from 'bpmn-js-properties-panel'

const bpmnModeler = ref<any>(null)
const canvas = ref<HTMLDivElement>()

// 添加默认的空白 BPMN 图表 XML
const defaultXml = `<?xml version="1.0" encoding="UTF-8"?>
<bpmn2:definitions xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                   xmlns:bpmn2="http://www.omg.org/spec/BPMN/20100524/MODEL"
                   xmlns:bpmndi="http://www.omg.org/spec/BPMN/20100524/DI"
                   xmlns:dc="http://www.omg.org/spec/DD/20100524/DC"
                   xmlns:di="http://www.omg.org/spec/DD/20100524/DI"
                   id="sample-diagram"
                   targetNamespace="http://bpmn.io/schema/bpmn">
  <bpmn2:process id="Process_1" isExecutable="true">
    <bpmn2:startEvent id="StartEvent_1"/>
  </bpmn2:process>
  <bpmndi:BPMNDiagram id="BPMNDiagram_1">
    <bpmndi:BPMNPlane id="BPMNPlane_1" bpmnElement="Process_1">
      <bpmndi:BPMNShape id="_BPMNShape_StartEvent_2" bpmnElement="StartEvent_1">
        <dc:Bounds height="36.0" width="36.0" x="412.0" y="240.0"/>
      </bpmndi:BPMNShape>
    </bpmndi:BPMNPlane>
  </bpmndi:BPMNDiagram>
</bpmn2:definitions>`

onMounted(() => {
  initBpmnModeler()
})

function initBpmnModeler() {
  bpmnModeler.value = new BpmnModeler({
    container: canvas.value,
    propertiesPanel: {
      parent: '#properties',
    },
    additionalModules: [
      BpmnPropertiesPanelModule,
      BpmnPropertiesProviderModule,
      PropertiesPanelModule,
    ],
  })

  createNewDiagram()
}

async function createNewDiagram() {
  try {
    await bpmnModeler.value.importXML(defaultXml)
    // 自动调整视图
    const canvas = bpmnModeler.value.get('canvas')
    canvas.zoom('fit-viewport')
  } catch (err) {
    console.error('could not import BPMN 2.0 diagram', err)
  }
}

onBeforeUnmount(() => {
  if (bpmnModeler.value) {
    bpmnModeler.value.destroy()
  }
})
</script>

<style lang="less">
.containers {
  position: relative;
  width: 100%;
  height: calc(100vh - 52px);

  .canvas {
    width: 100%;
    height: 100%;
    position: relative;
    overflow: hidden;
  }

  .properties-panel-parent {
    position: absolute;
    right: 0;
    top: 0;
    width: 300px;
    height: 100%;
    border-left: 1px solid #ccc;
    overflow: auto;
    background-color: #fff;
    z-index: 1;
  }

  // 添加必要的样式
  :deep(.djs-palette) {
    position: absolute;
    left: 0;
    top: 0;
    width: auto;
    background: white;
    border-right: 1px solid #ccc;
    overflow: hidden;
  }
}

// 添加���性面板的基础样式
:deep(.bio-properties-panel) {
  height: 100%;
  padding: 0;
}
</style>
