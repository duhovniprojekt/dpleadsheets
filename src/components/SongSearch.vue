<script setup lang="ts">
import { ref, onMounted, computed } from "vue";
import { refDebounced } from '@vueuse/core';
import { Badge } from "@/components/ui/badge";

const searchValue = ref('')
const debouncedSearch = refDebounced(searchValue, 250)

const songCollection = ref<Song[]>([]);

interface Song {
  composer: string;
  lyricist: string;
  lyrics: string;
  name: string;
  number: number;
  songbook: string;
  source: string;
  transposition_bass: string;
  transposition_bass_f: string;
  transposition_bb: string;
  transposition_c: string;
  transposition_eb: string;
}

const emit = defineEmits<{
  (e: 'handleClick', link: string): void;
}>();

onMounted(async () => {
  try {
    const response = await fetch('https://pitfarcenim.scifidelityorchestra.com/files/dpleadsheets/songs_collection.json');
    if (!response.ok) {
      throw new Error('Network response was not ok');
    }
    songCollection.value = await response.json();
    console.log(songCollection.value)
  } catch (error) {
    console.error('Failed to fetch song collection:', error);
  }
});

const filteredSongList = computed(() => {
  let output
  const searchValue = debouncedSearch.value?.trim()
  if (!searchValue) {
    output = songCollection.value
  }

  else {
    output = songCollection.value.filter((item) => {
      return item.composer.includes(debouncedSearch.value)
        || item.lyricist.includes(debouncedSearch.value)
        || item.lyrics.includes(debouncedSearch.value)
        || item.name.includes(debouncedSearch.value)
        || item.number === Number(debouncedSearch.value)
    })
  }

  return output
})

const openSong = (link: string) => {
    emit('handleClick', link)
}
</script>

<template>
    <div class="flex flex-col gap-2">

        <div
          v-for="item of filteredSongList"
          :key="item.number"
          class="border border-gray-500 flex flex-col rounded-xl bg-slate-700 bg-opacity-100 p-3 gap-2"
        >
          <div class="flex w-full flex-col gap-1">
            <div class="flex items-center">
              <div class="flex items-center gap-2">
                <div class="text-lg font-semibold text-white">
                  {{ item.name }}
                </div>
              </div>
            </div>
          </div>
          <div class="line-clamp-2 text-xs text-slate-300">
            {{ item.songbook }}
          </div>
          <div class="flex items-center gap-2 flex-wrap">
            <Badge class="cursor-pointer" v-if="item.transposition_c" variant="secondary" @click="openSong(`transposition_c/${item.transposition_c}`)">
                Song in C
            </Badge>
            <Badge class="cursor-pointer" v-if="item.transposition_eb" variant="secondary" @click="openSong(`transposition_eb/${item.transposition_eb}`)">
                Song in Eb
            </Badge>
            <Badge class="cursor-pointer" v-if="item.transposition_bb" variant="secondary" @click="openSong(`transposition_bb/${item.transposition_bb}`)">
                Song in Bb
            </Badge>
            <Badge class="cursor-pointer" v-if="item.transposition_bass_f" variant="secondary" @click="openSong(`transposition_bass_f/${item.transposition_bass_f}`)">
                Song in F
            </Badge>
            <Badge class="cursor-pointer text-white border-2" v-if="item.transposition_bass !== ''" variant="outline" @click="openSong(`transposition_bass/${item.transposition_bass}`)">
                Song in Bass
            </Badge>
          </div>
        </div>
    </div>
</template>
