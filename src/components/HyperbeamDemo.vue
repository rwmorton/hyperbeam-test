<template>
  <div class="flex flex-row">
    <div
      id="hyperbeam1"
      class="flex flex-row border-2 border-black border-dotted"
      v-bind:style="{
        width: '800px',
        height: '600px'
      }"
    />
    <div
      id="hyperbeam2"
      class="flex flex-row border-2 border-black border-dotted"
      v-bind:style="{
        width: '800px',
        height: '600px'
      }"
    />
  </div>
</template>

<script setup lang="ts">
import { onMounted, onBeforeUnmount } from 'vue'
import Hyperbeam, {
  type HyperbeamEmbed, TimedOutError,
  type SessionTerminatedError
} from '@hyperbeam/web'
import axios from 'axios'

const HYPERBEAM_SK = import.meta.env.VITE_HYPERBEAM_SK

let hyperbeam1: HyperbeamEmbed
let hyperbeam2: HyperbeamEmbed

const roles = [
  "control",
  "clipboard_copy",
  "programmatic_navigation",
  "cursor_data",
]

const config = {
  hide_cursor: true,
  default_roles: roles,
  ublock: true,
  timeout: {
    offline: 10,
  },
  quality: {
    mode: "sharp",
  }
}

interface VirtualMachine {
  session_id: string
  embed_url: string
  admin_token: string
}
let virtualMachine: VirtualMachine

onMounted(async () => {
  const response = await axios.post('/vm', config, {
    headers: { Authorization: `Bearer ${HYPERBEAM_SK}` }
  })
  virtualMachine = await response.data

  const container1 = document.getElementById('hyperbeam1')
  const container2 = document.getElementById('hyperbeam2')
  const { embed_url } = virtualMachine

  console.log(virtualMachine)

  try {
    hyperbeam1 = await Hyperbeam(container1 as HTMLDivElement, embed_url) as HyperbeamEmbed
    hyperbeam2 = await Hyperbeam(container2 as HTMLDivElement, embed_url) as HyperbeamEmbed
  } catch (e) {
    console.error(e)
  }
})

onBeforeUnmount(() => {
  hyperbeam1.destroy()
  hyperbeam2.destroy()
})
</script>
