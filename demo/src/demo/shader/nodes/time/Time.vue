<script setup lang="ts">
import { PropType } from 'vue';
import { ShaderNode } from '../types';

const props = defineProps({
  node: { type: Object as PropType<ShaderNode>, required: true },
  registerPoint: { type: Function as PropType<(node: ShaderNode, point: string) => (el: any) => void>, required: true }
})

defineEmits({
  'register-point': (pointName: string, el: HTMLElement) => true,
  'connect-to': (pointName: string) => true,
  'connect-from': (pointName: string) => true,
})
</script>

<template>
  <DemoNode :title="node.title" @delete="$emit('delete')">
    <template #outputs>
      <DemoButton 
        color="linear-gradient(to right, #1d976c, #93f9b9)" 
        right 
        @register-point="registerPoint(node, 'out')($event)" 
        @circle-click="$emit('connect-from', 'out')"/>
    </template>
    
    <template #inputs>
      <DemoButton
        v-for="pointName in ['speed']"
        :key="pointName"
        color="linear-gradient(145deg, #ff4e50, #f9d423)"
        @register-point="registerPoint(node, pointName)($event)"
        @circle-click="$emit('connect-to', pointName)"
      >
        <span style="margin-right: 8px;"> {{ pointName }} </span>
        <DemoInput v-model="node.value[pointName]" />
      </DemoButton>
    </template>
  </DemoNode>
</template>
