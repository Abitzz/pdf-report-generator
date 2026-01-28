
# 📊 Dashboard & PDF Report Generator

Aplikasi dashboard responsif berbasis **Nuxt** yang digunakan untuk membuat laporan PDF.  
Project ini dibuat sebagai bagian dari **Technical Test Frontend Developer**.

---

## ✨ Fitur

- Membuat laporan PDF berdasarkan input pengguna
- Riwayat pembuatan PDF (disimpan menggunakan LocalStorage)
- Menghapus seluruh riwayat PDF
- Tampilan responsif (mobile, tablet, desktop)
- UI rapi dan user-friendly

---

## 🛠️ Teknologi yang Digunakan

- **Framework:** Nuxt
- **Bahasa:** TypeScript
- **Styling:** Tailwind CSS
- **PDF Generator:** jsPDF
- **Penyimpanan State:** LocalStorage

---

## 📱 Desain Responsif

Aplikasi ini menggunakan pendekatan **mobile-first** dan mendukung ukuran layar:

- Minimum: **320px**
- Maksimum: **1920px**

Responsivitas diatur menggunakan breakpoint Tailwind CSS (`sm`, `md`, `lg`, dll), termasuk:
- Layout satu kolom pada layar kecil
- Layout multi-kolom pada layar lebih besar
- Tabel dapat di-scroll secara horizontal pada layar kecil

---

## 📂 Struktur Project (Sederhana)

```

.
├── app/
│   ├── pages/
│   │   └── index.vue        # Halaman utama dashboard
│   ├── layouts/
│   │   └── default.vue     # Layout utama
│   └── app.vue
├── public/
├── nuxt.config.ts
├── package.json
├── tsconfig.json
└── README.md

````

---

## ⚙️ Prasyarat

Pastikan tools berikut sudah ter-install:

- **Node.js** (disarankan versi 18 atau lebih baru)
- **npm**

Cek versi:
```bash
node -v
npm -v
````

---

## 🚀 Cara Menjalankan Project

### 1️⃣ Clone Repository

```bash
git clone https://github.com/Abitzz/pdf-report-generator
cd pdf-report-generator
```

---

### 2️⃣ Install Dependency

```bash
npm install
```

---

### 3️⃣ Jalankan Development Server

```bash
npm run dev
```

Aplikasi akan berjalan di:

```
http://localhost:3000
```

## 📝 Catatan & Batasan

* Project ini bersifat **frontend-only** (tanpa backend).
* LocalStorage memiliki batas ukuran (~5MB), sehingga implementasi ini ditujukan untuk kebutuhan demo atau technical test.
* Untuk kebutuhan production, PDF sebaiknya disimpan di backend atau object storage.

---

## 💡 Catatan Implementasi

* Menggunakan Composition API (`<script setup>`)
* State dikelola secara lokal menggunakan reaktivitas Vue
* Struktur kode dibuat bersih dan mudah dibaca
* Styling menggunakan Tailwind CSS (utility-first)
* Tidak menggunakan UI framework tambahan agar project tetap ringan

---

## 👨‍💻 Author

**Fauzi Tsabit Kemal**
Calon Frontend Developer

---

## 📄 Lisensi

Project ini dibuat untuk keperluan technical test dan demonstrasi.

```
