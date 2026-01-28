<script setup lang="ts">

import { reactive, ref, computed } from 'vue'
import { onMounted } from 'vue'
import jsPDF from 'jspdf'

//ukuran kertas
const form = reactive({
  pageSize: 'A4',
  title: '',
  description: '',
  nominal: ''
})

const errors = reactive<Record<string, string>>({})
const loading = ref(false)

//rupiah
const formatRupiah = (value: string) => {
  const number = value.replace(/\D/g, '')
  if (!number) return ''
  return 'Rp ' + Number(number).toLocaleString('id-ID')
}

const handleNominalInput = (e: Event) => {
  const target = e.target as HTMLInputElement
  form.nominal = formatRupiah(target.value)
}

//untuk nominal
const getNominalNumber = () => {
  return Number(form.nominal.replace(/\D/g, ''))
}

//validasi data form
const validateForm = () => {
  Object.keys(errors).forEach(key => delete errors[key])

  if (!form.pageSize) {
    errors.pageSize = 'Ukuran halaman wajib dipilih'
  }

  if (!form.title || form.title.length < 5) {
    errors.title = 'Judul minimal 5 karakter'
  }

  if (!form.description || form.description.length < 10) {
    errors.description = 'Deskripsi minimal 10 karakter'
  }

  if (!form.nominal) {
    errors.nominal = 'Nominal wajib diisi'
  }

  return Object.keys(errors).length === 0
}

//sumbit form
const submitForm = async () => {
  if (!validateForm()) return

  loading.value = true

try {
  generatePdf()

  history.value.unshift({
    title: form.title,
    pageSize: form.pageSize,
    nominal: getNominalNumber(),
    date: new Date().toLocaleString('id-ID')
  })

  localStorage.setItem(
    'pdf-history',
    JSON.stringify(history.value)
  )

  alert('PDF berhasil di-generate')

  //reset form
  form.title = ''
  form.description = ''
  form.nominal = ''
  form.pageSize = 'A4'

  } catch (error) {
    alert('Terjadi kesalahan saat generate PDF')
  } finally {
    loading.value = false
  }
}

//membuat isi pdf
const generatePdf = () => {
  const nominalValue = getNominalNumber()

  const doc = new jsPDF({
    format: form.pageSize.toLowerCase()
  })

  let currentY = 20

  //judul
  doc.setFont('bold')
  doc.setFontSize(16)
  doc.text(form.title, 20, currentY)
  doc.setFont('normal')

  //ukuran halaman
  currentY += 12
  doc.setFontSize(12)
  doc.text(`Ukuran Halaman: ${form.pageSize}`, 20, currentY)

  //tanggal
  currentY += 10
  doc.text(`Tanggal: ${new Date().toLocaleString('id-ID')}`, 20, currentY)

  //deskripsi
  currentY += 14
  doc.setFontSize(13)
  doc.text('Deskripsi:', 20, currentY)

  currentY += 8
  doc.setFontSize(12)
  doc.text(form.description, 20, currentY, { maxWidth: 170 })

  //nominal
  currentY += 30
  doc.setFontSize(13)
  doc.text(
    `Nominal: Rp ${nominalValue.toLocaleString('id-ID')}`,
    20,
    currentY
  )

  doc.save(`${form.title}.pdf`)
}

//untuk tabel history pdf
interface HistoryItem {
  title: string
  pageSize: string
  nominal: number
  date: string
}

const history = ref<HistoryItem[]>([])

onMounted(() => {
  const saved = localStorage.getItem('pdf-history')
  if (saved) {
    history.value = JSON.parse(saved)
  }
})

//membersihkan history
const clearHistory = () => {
  const confirmClear = confirm('Yakin ingin menghapus semua history?')

  if (!confirmClear) return

  history.value = []
  localStorage.removeItem('pdf-history')
}

</script>

<template>

  <div class="space-y-6">
    <h2 class="text-2xl font-bold">
      Dashboard
    </h2>

    <!-- FORM CARD -->
    <div class="bg-white rounded shadow p-6 space-y-6">
      <h3 class="text-lg font-semibold">
        Form PDF Generator
      </h3>

      <!-- GRID -->
      <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
        <!-- PAGE SIZE -->
        <div>
          <label class="block text-sm font-medium mb-1">
            Ukuran Halaman
          </label>
          <select
            v-model="form.pageSize"
            :class="[
            'w-full border rounded px-3 py-2 focus:outline-none focus:ring',
            errors.pageSize && 'border-red-500'
            ]"
          >
            <option value="A4">A4</option>
            <option value="A5">A5</option>
            <option value="Letter">Letter</option>
          </select>

          <p v-if="errors.pageSize" class="text-red-500 text-sm mt-1">
              {{ errors.pageSize }}
          </p>
        </div>

        <!-- TITLE -->
        <div>
          <label class="block text-sm font-medium mb-1">
            Judul Laporan
          </label>
          <input
            v-model="form.title"
            type="text"
            maxlength="100"
            placeholder="Masukkan judul laporan..."
            :class="[
                'w-full border rounded px-3 py-2 focus:outline-none focus:ring',
                errors.title && 'border-red-500'
            ]"
          />
          <p v-if="errors.title" class="text-red-500 text-sm mt-1">
          {{ errors.title }}
          </p>
        </div>
      </div>

      <!-- DESCRIPTION -->
      <div>
        <label class="block text-sm font-medium mb-1">
          Deskripsi / Isi Laporan
        </label>
        <textarea
          v-model="form.description"
          rows="4"
          placeholder="Masukkan isi laporan..."
          :class="[
            'w-full border rounded px-3 py-2 focus:outline-none focus:ring',
            errors.description && 'border-red-500'
          ]"
        />
        <p v-if="errors.description" class="text-red-500 text-sm mt-1">
          {{ errors.description }}
        </p>
      </div>

      <!-- NOMINAL -->
      <div>
        <label class="block text-sm font-medium mb-1">
            Nominal
        </label>
        <input
          :value="form.nominal"
          @input="handleNominalInput"
          type="text"
          placeholder="Rp 0"
          :class="[
            'w-full border rounded px-3 py-2 focus:outline-none focus:ring',
            errors.nominal && 'border-red-500'
          ]"
        />
        <p v-if="errors.nominal" class="text-red-500 text-sm mt-1">
          {{ errors.nominal }}
        </p>
      </div>

      <!-- SUBMIT BUTTON -->
      <div class="flex justify-end">
        <button
          @click="submitForm"
          :disabled="loading"
          class="bg-blue-600 text-white px-6 py-2 rounded
                hover:bg-blue-700 active:bg-blue-800
                transition
                disabled:opacity-50 disabled:cursor-not-allowed
                flex items-center gap-2"
        >
          <!-- ICON DOCUMENT -->
          <svg
            xmlns="http://www.w3.org/2000/svg"
            class="w-5 h-5"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
            stroke-width="2"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M9 12h6m-6 4h6M7 4h10l2 2v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6a2 2 0 0 1 2-2z"
            />
          </svg>

          <span>
            {{ loading ? 'Generating...' : 'Generate PDF' }}
          </span>

        </button>
      </div>
    </div>

      <!-- HISTORY TABLE -->
      <div class="bg-white rounded shadow p-6">
        <h3 class="text-lg font-semibold mb-4">
          History Generate
        </h3>

        <div class="overflow-x-auto">
          <table class="w-full border-collapse">
            <thead>
              <tr class="bg-blue-200 text-left">
                <th class="border px-3 py-2">No</th>
                <th class="border px-3 py-2">Judul</th>
                <th class="border px-3 py-2">Page Size</th>
                <th class="border px-3 py-2">Nominal</th>
                <th class="border px-3 py-2">Tanggal</th>
              </tr>
            </thead>

            <tbody>
              <!-- HISTORY EMPTY STATE -->
              <tr v-if="history.length === 0">
                <td
                  colspan="5"
                  class="text-center text-gray-500 py-6"
                >
                  Belum ada data
                </td>
              </tr>

            <!-- DATA HISTORY PDF -->
              <tr
                v-for="(item, index) in history"
                :key="index"
                class="odd:bg-white even:bg-blue-100"
              >
                <td class="border px-3 py-2">
                  {{ index + 1 }}
                </td>
                <td class="border px-3 py-2">
                  {{ item.title }}
                </td>
                <td class="border px-3 py-2">
                  {{ item.pageSize }}
                </td>
                <td class="border px-3 py-2">
                  Rp {{ item.nominal.toLocaleString('id-ID') }}
                </td>
                <td class="border px-3 py-2">
                  {{ item.date }}
                </td>
              </tr>
            </tbody>
          </table>

          <!--CLEAN HISTORY BUTTON-->
          <button
            v-if="history.length > 0"
            @click="clearHistory"
            class="text-sm text-red-600 hover:text-red-700 hover:underline transition"
          >
            Clear History
          </button>
          
        </div>
      </div>
    </div>

</template>
