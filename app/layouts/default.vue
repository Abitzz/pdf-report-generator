<template>
  <div class="min-h-screen bg-gray-100">
    <!-- HEADER -->
    <header class="bg-white shadow">
      <div class="max-w-7xl mx-auto px-6 py-4 flex items-center gap-3">
        <div class="w-8 h-8 bg-blue-600 rounded text-white flex items-center justify-center font-bold">
          P
        </div>
        <h1 class="text-lg font-semibold">
          PDF Report Generator
        </h1>
      </div>
    </header>

    <!-- MAIN CONTENT -->
    <main class="max-w-7xl mx-auto px-6 py-6">
      <slot/>
    </main>
  </div>
</template>
