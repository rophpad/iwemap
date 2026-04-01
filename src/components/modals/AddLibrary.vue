<template>
  <div class="w-full h-100 bg-white text-black flex flex-col items-center space-y-4">
    <div class="p-3 rounded-full">
      <svg
        xmlns="http://www.w3.org/2000/svg"
        width="32"
        height="32"
        viewBox="0 0 24 24"
        class="size-16 stroke-[#0A5EBE]"
      >
        <path
          fill="none"
          stroke=""
          stroke-linecap="round"
          stroke-linejoin="round"
          stroke-width="1.5"
          d="M3 16h18M2 22h20M3 9h18M3 22V8c0-2.828 0-4.243.93-5.121S6.358 2 9.353 2h5.294c2.995 0 4.492 0 5.423.879C21 3.757 21 5.172 21 8v14m-10-3h2M10 9L9 5M6.5 9V5M14 16v-4m-2-3V5m4 11l1-4m2 4v-4"
        />
      </svg>
    </div>

    <h3 class="text-lg font-semibold">Ajouter une bibliothèque</h3>

    <p class="text-sm text-center">
      Renseignez les informations de la bibliothèque puis ajoutez vos livres manuellement ou via
      un fichier CSV.
    </p>

    <form class="w-full space-y-4" @submit.prevent="submitForm">
      <div class="grid grid-cols-1 md:grid-cols-2 gap-3">
        <div class="flex flex-col gap-1">
          <label class="text-sm font-medium" for="library-name">Nom de la bibliothèque</label>
          <input
            id="library-name"
            v-model.trim="libraryName"
            type="text"
            required
            class="w-full rounded border border-black/20 px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#0A5EBE]/30"
            placeholder="Ex: Bibliothèque de Calavi"
          />
        </div>

        <div class="flex flex-col gap-1">
          <label class="text-sm font-medium" for="library-contact">Contact de la bibliothèque</label>
          <input
            id="library-contact"
            v-model.trim="libraryContact"
            type="text"
            required
            class="w-full rounded border border-black/20 px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#0A5EBE]/30"
            placeholder="Ex: +229 XX XX XX XX"
          />
        </div>
      </div>

      <div class="flex flex-col gap-1">
        <label class="text-sm font-medium" for="library-website">Site web de la bibliothèque</label>
        <input
          id="library-website"
          v-model.trim="libraryWebsite"
          type="url"
          required
          class="w-full rounded border border-black/20 px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-[#0A5EBE]/30"
          placeholder="https://example.com"
        />
      </div>

      <div class="border border-black/10 rounded-lg p-3 space-y-3">
        <div class="flex items-center justify-between">
          <p class="text-sm font-semibold">Liste des livres (manuel)</p>
          <button
            type="button"
            @click="addBook"
            class="text-xs px-3 py-1 rounded-full border border-black/20 hover:bg-black/5"
          >
            + Ajouter un livre
          </button>
        </div>

        <div class="space-y-3">
          <div
            v-for="(book, index) in books"
            :key="book.id"
            class="grid grid-cols-1 md:grid-cols-2 gap-2 border border-black/10 rounded p-2"
          >
            <input
              v-model.trim="book.name"
              type="text"
              class="rounded border border-black/20 px-2 py-1.5 text-sm"
              placeholder="Nom du livre"
            />
            <input
              v-model.trim="book.author"
              type="text"
              class="rounded border border-black/20 px-2 py-1.5 text-sm"
              placeholder="Auteur"
            />
            <input
              v-model.trim="book.category"
              type="text"
              class="rounded border border-black/20 px-2 py-1.5 text-sm"
              placeholder="Catégorie"
            />
            <div class="flex gap-2">
              <select
                v-model="book.status"
                class="w-full rounded border border-black/20 px-2 py-1.5 text-sm bg-white"
              >
                <option value="borrowable">Borrowable</option>
                <option value="unborrowable">Non-borrowable</option>
              </select>
              <button
                type="button"
                @click="removeBook(index)"
                class="px-2 py-1.5 text-xs rounded border border-red-200 text-red-600 hover:bg-red-50"
              >
                Retirer
              </button>
            </div>
          </div>
        </div>
      </div>

      <div class="border border-black/10 rounded-lg p-3 space-y-2">
        <p class="text-sm font-semibold">Ou importer un CSV</p>
        <input
          type="file"
          accept=".csv,text/csv"
          @change="onCsvChange"
          class="w-full text-sm"
        />
        <p class="text-xs text-gray-600">
          Format attendu: <span class="font-medium">book_name,author,category,status</span>
        </p>
        <p class="text-xs text-gray-600">
          Exemple: <span class="font-medium">Clean Code,Robert C. Martin,technology,borrowable</span>
        </p>
      </div>

      <p v-if="errorMessage" class="text-sm text-red-600">{{ errorMessage }}</p>
      <p v-if="successMessage" class="text-sm text-green-700">{{ successMessage }}</p>

      <div class="w-full flex items-center justify-end gap-2">
        <button
          type="button"
          @click="emit('close')"
          class="px-4 py-2 rounded border border-black/20 text-sm hover:bg-black/5"
        >
          Annuler
        </button>
        <button
          type="submit"
          class="px-4 py-2 bg-[#0A5EBE] text-white rounded text-sm hover:scale-95 transition"
        >
          Soumettre
        </button>
      </div>
    </form>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

type BookStatus = 'borrowable' | 'unborrowable'

interface ManualBook {
  id: number
  name: string
  author: string
  category: string
  status: BookStatus
}

interface SubmittedBook {
  name: string
  author: string
  category: string
  status: BookStatus
}

const emit = defineEmits<{
  close: []
}>()

const libraryName = ref('')
const libraryContact = ref('')
const libraryWebsite = ref('')
const csvBooks = ref<SubmittedBook[]>([])
const books = ref<ManualBook[]>([
  {
    id: Date.now(),
    name: '',
    author: '',
    category: '',
    status: 'borrowable',
  },
])

const errorMessage = ref('')
const successMessage = ref('')

const addBook = () => {
  books.value.push({
    id: Date.now() + Math.floor(Math.random() * 1000),
    name: '',
    author: '',
    category: '',
    status: 'borrowable',
  })
}

const removeBook = (index: number) => {
  books.value.splice(index, 1)
  if (books.value.length === 0) {
    addBook()
  }
}

const onCsvChange = async (event: Event) => {
  const input = event.target as HTMLInputElement
  const file = input.files?.[0]
  csvBooks.value = []
  errorMessage.value = ''
  successMessage.value = ''

  if (!file) return

  try {
    const text = await file.text()
    const lines = text
      .split('\n')
      .map((line) => line.trim())
      .filter(Boolean)

    if (!lines.length) {
      errorMessage.value = 'Le fichier CSV est vide.'
      return
    }

    const parsedBooks: SubmittedBook[] = []

    for (const line of lines) {
      const columns = line.split(',').map((item) => item.trim())

      if (columns.length !== 4) continue
      const [name, author, category, statusRaw] = columns

      if (!name || !author || !category || !statusRaw) continue

      if (name.toLowerCase() === 'book_name') continue

      const status = statusRaw === 'borrowable' ? 'borrowable' : 'unborrowable'

      parsedBooks.push({
        name,
        author,
        category,
        status,
      })
    }

    if (!parsedBooks.length) {
      errorMessage.value = 'Aucun livre valide trouvé dans le CSV.'
      return
    }

    csvBooks.value = parsedBooks
    successMessage.value = `${parsedBooks.length} livre(s) importé(s) depuis le CSV.`
  } catch {
    errorMessage.value = 'Impossible de lire le fichier CSV.'
  }
}

const submitForm = () => {
  errorMessage.value = ''
  successMessage.value = ''

  if (!libraryName.value || !libraryContact.value || !libraryWebsite.value) {
    errorMessage.value = 'Veuillez remplir toutes les informations de la bibliothèque.'
    return
  }

  let submittedBooks: SubmittedBook[] = []

  if (csvBooks.value.length) {
    submittedBooks = csvBooks.value
  } else {
    submittedBooks = books.value
      .filter((book) => book.name && book.author && book.category)
      .map((book) => ({
        name: book.name,
        author: book.author,
        category: book.category,
        status: book.status,
      }))
  }

  if (!submittedBooks.length) {
    errorMessage.value = 'Ajoutez au moins un livre valide ou importez un CSV conforme.'
    return
  }

  const payload = {
    library: {
      name: libraryName.value,
      contact: libraryContact.value,
      website: libraryWebsite.value,
    },
    books: submittedBooks,
  }

  console.log('Add Library submission:', payload)
  successMessage.value = 'Votre demande a été soumise avec succès.'
}
</script>
