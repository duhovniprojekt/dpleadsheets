<script setup lang="ts">
import {
  ResizableHandle,
  ResizablePanel,
  ResizablePanelGroup,
} from "@/components/ui/resizable";

import { ScrollArea } from '@/components/ui/scroll-area'
import PdfPreview from "@/components/PdfPreview.vue"
import SongSearch from "@/components/SongSearch.vue";
import { ref } from "vue";

const SongPdfLink = ref('')

const picksongPdf = (data) => {
  console.log('clicked', data)
  SongPdfLink.value = data
}


</script>

<template>
  <ResizablePanelGroup
    id="handle-demo-group-1"
    direction="horizontal"
    class="h-full"
  >
    <ResizablePanel id="handle-demo-panel-1" :default-size="20" :max-size="40" :min-size="15" class="bg-primary border-none">
      <ScrollArea class="flex h-screen items-center justify-center p-2">
        <SongSearch @handleClick="picksongPdf"  />
      </ScrollArea>
    </ResizablePanel>
    <ResizableHandle id="handle-demo-handle-1" with-handle />
    <ResizablePanel id="handle-demo-panel-2" :default-size="75">
      <div class="flex h-full items-center justify-center p-6">
        <PdfPreview :pdfUrl="SongPdfLink"/>
      </div>
    </ResizablePanel>
  </ResizablePanelGroup>
</template>
