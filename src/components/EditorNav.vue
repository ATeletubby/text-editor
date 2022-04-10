<template>
  <a-menu mode="horizontal" triggerSubMenuAction="click" class="editor-menu">
    <a-sub-menu key="sub1">
      <template #title>File</template>
      <a-menu-item key="setting:1" @click="emitsController('createFile')">New</a-menu-item>
      <a-menu-item key="setting:2" @click="emitsController('loadFile')">Open</a-menu-item>
      <a-menu-item key="setting:3" @click="emitsController('saveFile', 'save')">Save</a-menu-item>
      <a-menu-item key="setting:4" @click="emitsController('saveFile', 'saveAs')">Save As</a-menu-item>
    </a-sub-menu>
  </a-menu>
</template>

<script setup lang="ts">
import { ref, defineEmits } from "vue";
const emitsController = defineEmits(['loadFile', 'createFile', 'saveFileAs'])

</script>

<style scoped>
.editor-menu{
  display: inline-block;
  background-color: red;
}
</style>