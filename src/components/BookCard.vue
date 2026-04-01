<script setup lang="ts">
import { computed } from 'vue'
import { RouterLink } from 'vue-router'

interface Props {
  bookId?: string
  title: string
  author: string
  category: string
  locations: string[]
  status: string
  description?: string
  coverImage?: string
}

const props = defineProps<Props>()

// Map category to display label
const categoryLabels: Record<string, string> = {
  droit: 'Droit',
  technology: 'Technologie',
  science: 'Sciences',
  literature: 'Littérature',
  audiobook: 'Livres audio',
  paperback: 'Livres papier',
}

// Map status to display label
const statusLabels: Record<string, string> = {
  unborrowable: 'Non-empruntable',
  borrowable: 'Empruntable',
}

const displayCategory = categoryLabels[props.category] || props.category
const displayStatus = statusLabels[props.status] || props.status
const cardComponent = computed(() => (props.bookId ? RouterLink : 'div'))
const bookLink = computed(() => (props.bookId ? `/livres/${props.bookId}` : undefined))
</script>

<template>
  <component
    :is="cardComponent"
    :to="bookLink"
    class="w-full h-max border border-black/10 bg-white rounded-xl p-5 flex flex-col gap-4 shadow-sm hover:shadow-md transition"
  >
    <!-- Title & Author -->
    <div>
      <h3 class="text-lg font-semibold text-gray-900">{{ title }}</h3>
      <p class="text-sm text-gray-600">{{ author }}</p>
    </div>

    <!-- Category -->
    <span
      class="w-max px-3 py-1 rounded-full border border-gray-300 text-xs text-gray-700 bg-gray-50"
    >
      {{ displayCategory }}
    </span>

    <!-- Availability -->
    <div class="text-sm">
      <p class="text-gray-600">Disponible à la bibliothèque de :</p>

      <p class="font-medium text-gray-800">
        <template v-for="(location, index) in locations" :key="location">
          <a
            :href="`https://maps.google.com/?q=bibliothèque+${encodeURIComponent(location)}`"
            target="_blank"
            rel="noopener noreferrer"
            class="hover:underline hover:text-[#0A5EBE] transition-colors"
            >{{ location }}</a
          ><span v-if="index < locations.length - 1">, </span>
        </template>
      </p>
    </div>

    <!-- Status -->
    <div class="flex flex-wrap gap-2">
      <span
        class="px-3 py-1 rounded-full border border-gray-300 text-xs text-gray-700"
        :class="`${status === 'borrowable' && 'bg-[#0A5EBE]/10 text-[#0A5EBE] border-[#0A5EBE]/30'}`"
      >
        {{ displayStatus }}
      </span>
    </div>
  </component>
</template>
