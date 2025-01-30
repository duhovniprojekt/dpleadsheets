<script setup lang="ts">
import { ref, defineProps, computed } from "vue";
import { ZoomIn, ZoomOut, ChevronRight, ChevronLeft } from "lucide-vue-next";
import { VuePDF, usePDF } from "@tato30/vue-pdf";
const props = defineProps<{
  pdfUrl: string;
}>();

const scaleRatio = ref(1);
const page = ref(1);
const watermarkOptions = ref({
  columns: 3,
  rows: 3,
  color: "#7828e911",
  rotation: 45,
  fontSize: 40,
});

const completeUrl = computed(() => {
  return `https://pitfarcenim.scifidelityorchestra.com/files/dpleadsheets/${props.pdfUrl}`
})

const { pdf, pages } = usePDF(completeUrl)


const scalePdf = (option: string) => {
  if (option === "zoomin" && scaleRatio.value < 2.5) {
    scaleRatio.value = scaleRatio.value + 0.25
  } else if (option === "zoomout" && scaleRatio.value > 0.25) {
    scaleRatio.value = scaleRatio.value - 0.25
  }
}

const nextPage = (type: string) => {
  if (type === 'plus') {
    page.value = page.value + 1
  }
  else {
    page.value = page.value - 1
  }
}
</script>

<template>
  <VuePDF :pdf=pdf :page=page :src=completeUrl :scale=scaleRatio watermark-text="Duhovni projekt"
    :watermark-options="watermarkOptions" />


  <div class="rounded-xl p-2 fixed bottom-5 bg-slate-100 flex gap-2">
    <ZoomIn :size="40" stroke-width="2" class="p-1 rounded-md text-primary border-2 border-primary cursor-pointer"
      @click="scalePdf('zoomin')" />
    <ZoomOut :size="40" stroke-width="2" class="p-1 rounded-md text-primary border-2 border-primary cursor-pointer"
      @click="scalePdf('zoomout')" />
    <ChevronRight v-if="pages > 1 && page < pages" :size="40" stroke-width="2"
      class="p-1 rounded-md text-primary border-2 border-primary cursor-pointer" @click="nextPage('plus')" />
    <ChevronLeft v-else :size="40" stroke-width="2"
      class="p-1 rounded-md text-primary border-2 border-primary cursor-pointer" @click="nextPage('minus')" />
  </div>
</template>
