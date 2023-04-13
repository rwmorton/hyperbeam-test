<template>
  <div
    id="hyperbeam"
    class="flex flex-row border-2 border-black border-dotted"
    v-bind:style="{
      width: '800px',
      height: '600px'
     }"
  />
</template>

<script setup lang="ts">
import { onMounted, onBeforeUnmount } from 'vue'
import Hyperbeam, {
  type HyperbeamEmbed, TimedOutError,
  type SessionTerminatedError
} from '@hyperbeam/web'
import axios from 'axios'

const HYPERBEAM_SK = import.meta.env.VITE_HYPERBEAM_SK

let hyperbeam: HyperbeamEmbed

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
  virtualMachine = await axios.post('/vm', config, {
    headers: { Authorization: `Bearer ${HYPERBEAM_SK}` }
  })

  const container = document.getElementById('hyperbeam')
  const { embed_url } = virtualMachine

  console.log(virtualMachine)

  try {
    hyperbeam = await Hyperbeam(container as HTMLDivElement, embed_url) as HyperbeamEmbed
  } catch (e) {
    console.error(e)
  }
})

onBeforeUnmount(() => {
  hyperbeam.destroy()
})
</script>
