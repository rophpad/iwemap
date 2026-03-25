<script setup lang="ts">
import { computed } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import BookCard from '@/components/BookCard.vue'
import { Libraries } from '@/data/libraries'

const route = useRoute()
const router = useRouter()

const categoryLabels: Record<string, string> = {
  droit: 'Droit',
  technology: 'Technologie',
  science: 'Sciences',
  literature: 'Littérature',
  audiobook: 'Livres audio',
  paperback: 'Livres papier',
}

const statusLabels: Record<string, string> = {
  unborrowable: 'Non-empruntable',
  borrowable: 'Empruntable',
}

const selectedBookId = computed(() => String(route.params.id || ''))

const bookContext = computed(() => {
  for (const library of Libraries) {
    const book = library.books.find((item) => item.id === selectedBookId.value)
    if (book) {
      return { book, library }
    }
  }
  return null
})

const book = computed(() => bookContext.value?.book ?? null)
const library = computed(() => bookContext.value?.library ?? null)

const displayCategory = computed(() => {
  if (!book.value) return ''
  return categoryLabels[book.value.category] || book.value.category
})

const displayStatus = computed(() => {
  if (!book.value) return ''
  return statusLabels[book.value.status] || book.value.status
})

const otherBooksInLibrary = computed(() => {
  if (!book.value || !library.value) return []
  return library.value.books.filter((item) => item.id !== book.value?.id)
})
</script>

<template>
  <div
    class="w-full px-3 lg:w-6xl py-6 flex flex-col items-center justify-center gap-6 no-scrollbar overflow-y-auto"
  >
    <div v-if="!book || !library" class="w-full max-w-2xl bg-white border border-black/10 rounded-xl p-6 flex flex-col items-center justify-center">
      <p class="text-xl font-bold mb-2">Livre introuvable</p>
      <p class="text-sm text-gray-600 mb-4">Ce livre n'existe pas ou n'est plus disponible.</p>
      <button
        class="px-4 py-2 bg-[#0A5EBE] text-white rounded-full hover:scale-95 transition"
        @click="router.push('/livres')"
      >
        Retour à la liste des livres
      </button>
    </div>

    <template v-else>
      <div class="w-full flex items-center justify-between py-2 border-b border-black/20">
        <p class="font-bold text-xl">Détail du livre</p>
        <div
          class="p-2 border border-black/20 flex items-center justify-center rounded-full cursor-pointer hover:bg-black/5 transition"
          @click="router.back()"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="32"
            height="32"
            viewBox="0 0 24 24"
            class="size-4 fill-black/20"
          >
            <path
              fill=""
              d="m12 13.4l-2.917 2.925q-.277.275-.704.275t-.704-.275q-.275-.275-.275-.7t.275-.7L10.6 12L7.675 9.108Q7.4 8.831 7.4 8.404t.275-.704q.275-.275.7-.275t.7.275L12 10.625L14.892 7.7q.277-.275.704-.275t.704.275q.3.3.3.713t-.3.687L13.375 12l2.925 2.917q.275.277.275.704t-.275.704q-.3.3-.712.3t-.688-.3z"
            />
          </svg>
        </div>
      </div>

      <div class="w-full grid grid-cols-1 lg:grid-cols-[2fr_1fr] gap-4">
        <div class="w-full h-max border border-black/10 bg-white rounded-xl p-5 flex flex-col gap-4 shadow-sm">
          <div>
            <h1 class="text-2xl font-bold text-gray-900">{{ book.title }}</h1>
            <p class="text-sm text-gray-600">{{ book.author }}</p>
          </div>

          <div class="flex flex-wrap items-center gap-2">
            <span
              class="w-max px-3 py-1 rounded-full border border-gray-300 text-xs text-gray-700 bg-gray-50"
            >
              {{ displayCategory }}
            </span>
            <span
              class="px-3 py-1 rounded-full border border-gray-300 text-xs text-gray-700"
              :class="`${book.status === 'borrowable' && 'bg-[#0A5EBE]/10 text-[#0A5EBE] border-[#0A5EBE]/30'}`"
            >
              {{ displayStatus }}
            </span>
          </div>

          <div class="text-sm text-gray-700 leading-relaxed">
            <p class="font-semibold text-gray-900 mb-1">Description</p>
            <p>{{ book.description || 'Aucune description disponible pour ce livre.' }}</p>
          </div>
        </div>

        <div class="w-full h-max border border-black/10 bg-white rounded-xl p-5 flex flex-col gap-3 shadow-sm">
          <p class="text-lg font-semibold">Localisation</p>
          <p class="text-sm text-gray-700">
            Ce livre se trouve à la bibliothèque <span class="font-bold">{{ library.name }} ({{ library.location }})</span>
          </p>
          <!-- <p class="text-sm text-gray-600">Ville: {{ library.location }}</p> -->
          <a
            :href="`https://maps.google.com/?q=bibliothèque+${encodeURIComponent(library.location)}`"
            target="_blank"
            rel="noopener noreferrer"
            class="w-max text-sm text-[#0A5EBE] hover:underline"
          >
            Voir sur Google Maps
          </a>
          <RouterLink
            :to="`/bibliotheque/${library.id}`"
            class="w-max text-xs py-2 px-4 border border-black/20 rounded-full hover:bg-black/5 transition"
          >
            Voir la bibliothèque
          </RouterLink>
        </div>
      </div>

      <div class="w-full flex flex-col gap-4">
        <p class="text-xl font-bold">Autres livres dans cette bibliothèque</p>
        <div
          v-if="otherBooksInLibrary.length"
          class="w-full grid grid-cols-1 lg:grid-cols-4 items-center justify-center gap-4"
        >
          <BookCard
            v-for="relatedBook in otherBooksInLibrary"
            :key="`${library.id}-${relatedBook.id}-${relatedBook.title}`"
            :book-id="relatedBook.id"
            :title="relatedBook.title"
            :author="relatedBook.author"
            :category="relatedBook.category"
            :locations="relatedBook.locations"
            :status="relatedBook.status"
            :description="relatedBook.description"
          />
        </div>
        <p v-else class="text-sm text-gray-600">Aucun autre livre n'est disponible dans cette bibliothèque.</p>
      </div>
    </template>
  </div>
</template>
