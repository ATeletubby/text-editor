<template>
  <header>
    <h1 style="display: inline-block">
      <span>Text Editor</span>
    </h1>
    <editor-nav @loadFile="loadFile($event)" @createFile="addTab()" @saveFile="saveFile($event)"></editor-nav>
  </header>
  <a-tabs v-model:activeKey="activeKey" hide-add type="editable-card" @edit="onEdit" style="height: 100%">
    <a-tab-pane v-for="tab in tabs" :key="tab.key" :tab="tab.title" :closable="tab.closable">
      <textarea v-model="tab.content"></textarea>
    </a-tab-pane>
  </a-tabs>
  <footer> Powered by Teletubbies </footer>
</template>

<script setup lang="ts">
import EditorNav from './EditorNav.vue'
import {ref} from "vue";

const newTabIndex = ref(0);
const tabs = ref<{title: string; content: string; key: string; fileHandle?: any, closable?: boolean}[]>([
  { title: 'New File', content: '', key: newTabIndex.value, closable: false },
])
const activeKey = ref(tabs.value[0].key)

const loadFile = async() => {
  let [fileHandle] = await window.showOpenFilePicker();
  const file = await fileHandle.getFile();
  tabs.value.push({
    title: file.name,
    content: await file.text(),
    key: ++newTabIndex.value,
    fileHandle: fileHandle
  })
  activeKey.value = newTabIndex.value
}

const saveFile = async(operation: 'saveAs' | 'save') => {
  const currentTab = tabs.value[activeKey.value]

  if (operation === 'saveAs' || !currentTab.fileHandle) {
    const options = {
      types: [
        {
          description: "Test files",
          accept: {
            "text/plain": [".txt"],
          },
        },
      ],
    }
    const handle = await window.showSaveFilePicker(options);
    const writable = await handle.createWritable();

    await writable.write(currentTab.content);
    await writable.close();

    return handle;
  } else {
    const fileHandle = currentTab.fileHandle
    const writable = await fileHandle.createWritable();
    await writable.write(currentTab.content);
    await writable.close();
  }

}

const onEdit = (targetKey: string | MouseEvent, action: string) => {
  if (action === 'add') {
    addTab();
  } else {
    removeTab(targetKey as string);
  }
};

const addTab = () => {
  activeKey.value = `newTab${++newTabIndex.value}`;
  tabs.value.push({ title: 'New File', content: '', key: activeKey.value });
};

const removeTab = (targetKey: string) => {
  let lastIndex = 0;
  tabs.value.forEach((pane, i) => {
    if (pane.key === targetKey) {
      lastIndex = i - 1;
    }
  });
  tabs.value = tabs.value.filter(pane => pane.key !== targetKey);
  if (tabs.value.length && activeKey.value === targetKey) {
    if (lastIndex >= 0) {
      activeKey.value = tabs.value[lastIndex].key;
    } else {
      activeKey.value = tabs.value[0].key;
    }
  }
};

</script>

<style scoped>
header {
  background-color: #ccc;
  color: #666
}
h1 {
  margin: 0;
  padding: 0;
}
textarea {
  box-sizing: border-box;
  flex-grow: 1;
  font-family: -apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,Helvetica,Arial,sans-serif,"Apple Color Emoji","Segoe UI Emoji","Segoe UI Symbol";
  padding: 2px;
  white-space: pre;
  width: 100%;
  height: 77vh;
}

</style>