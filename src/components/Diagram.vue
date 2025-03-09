<script setup>
import mermaid from 'mermaid';
import { onMounted, ref, watch } from 'vue';

onMounted(() => {
  mermaid.initialize({ startOnLoad: true, theme: 'default' });
  renderDiagram();
})

const props = defineProps(['code']);

const renderDiagram = async () => {
  const { svg } = await mermaid.render('diagram', props.code);

  diagram.value = svg;
}

watch(() => props.code, () => {
  renderDiagram();
})

const diagram = ref('');

</script>

<template>
  <div v-html="diagram" class="flex justify-center"></div>
</template>