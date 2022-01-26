<script setup lang="ts">
import { createSourceNode, createTimeNode, createFragmentShaderNode, createVec4Node, createOutputNode, createMathNode } from './../../demo/shader/nodes'
import { Nodes, useConnections } from '../../../../src'
import { ref } from 'vue'
import type { Connection, ShaderNode } from '../../demo/shader/nodes';
import { itemsPreset, connectionsPreset } from '../../demo/shader/preset'
import AppLayout from '../../components/AppLayout.vue'
import AppButton from '../../components/AppButton.vue'
import AppSelect from '../../components/AppSelect.vue'

const items = ref<ShaderNode<any>[]>(itemsPreset)

const connections = ref<Connection<ShaderNode>[]>(connectionsPreset)

const { connectFrom, connectTo, registerPoint } = useConnections(connections)

const shaderNodeList:any = {
  time: createTimeNode,
  source: createSourceNode,
  fragment: createFragmentShaderNode,
  vector: createVec4Node,
  output: createOutputNode,
  math: createMathNode,
}

const selectOptions = Object.keys(shaderNodeList)

const addNode = (nodeName:string) => {
  const fn = shaderNodeList[nodeName]
  const node = fn()
  items.value.push(node)
}

const onDelete = (node: ShaderNode) => {
  items.value = items.value.filter((item) => {return item !== node})
}
</script>

<template>
  <AppLayout>
    <template #sidebar>
      <p>This is a shader generator example, where you can create GLSL shader and preview it.</p>
      
      <AppSelect :nodeList="selectOptions" @nodeSelected="addNode" />
      <AppButton class="add-node-btn">
        Add node
      </AppButton>
    </template>

    <Nodes v-model:nodes="items" v-model:connections="connections">
      <template #node-content="{ node }">
        <component
          :is="(node as ShaderNode).component"
          :connections="connections"
          :node="node"
          :register-point="registerPoint"
          @connect-from="connectFrom((node as ShaderNode) , $event)"
          @connect-to="connectTo((node as ShaderNode), $event)"
          @delete="onDelete(node)"
        />
      </template>
    </Nodes>  
  </AppLayout>
</template>

<style lang="scss">
.add-node-btn {
  margin-top: 15px;
}
</style>
