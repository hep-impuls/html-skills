# Type C — Info Dossier Reference

For news dossiers, topic overviews, and media-rich information pages.

---

## Page Shell

```html
<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Topic] — Dossier</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <script>
    tailwind.config = {
      theme: { extend: { colors: { brand: '[brand color]' } } }
    }
  </script>
  <style>
    /* Dark mode via data-theme attribute */
    [data-theme="dark"] body { background: #0f172a !important; color: #f1f5f9 !important; }
    [data-theme="dark"] .bg-white { background: #1e293b !important; }
    [data-theme="dark"] .bg-slate-50 { background: #0f172a !important; }
    [data-theme="dark"] .text-slate-600 { color: #94a3b8 !important; }
    [data-theme="dark"] .text-slate-800 { color: #f1f5f9 !important; }
    [data-theme="dark"] .border-slate-100 { border-color: #334155 !important; }
    /* Timeline scroll */
    .timeline-scroll::-webkit-scrollbar { width: 6px; }
    .timeline-scroll::-webkit-scrollbar-track { background: #f1f1f1; border-radius: 4px; }
    .timeline-scroll::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 4px; }
    @media print {
      .no-print { display: none !important; }
      body { background: white !important; color: black !important; }
    }
  </style>
</head>
<body class="bg-slate-50 text-slate-800 font-sans antialiased" id="pageBody">

  <!-- Header -->
  <header class="bg-brand text-white py-8 shadow-lg">
    <div class="max-w-6xl mx-auto px-4">
      <div class="flex items-center justify-between">
        <div>
          <h1 class="text-3xl md:text-4xl font-bold mb-2">[Title]</h1>
          <p class="text-opacity-80 text-lg">[Subtitle / source / date]</p>
        </div>
        <div class="flex gap-3 no-print">
          <button onclick="toggleTheme()" class="bg-white bg-opacity-20 hover:bg-opacity-30 px-4 py-2 rounded-lg text-sm font-medium transition" id="themeBtn">🌙 Dark</button>
          <button onclick="window.print()" class="bg-white bg-opacity-20 hover:bg-opacity-30 px-4 py-2 rounded-lg text-sm font-medium transition">🖨️ Print</button>
        </div>
      </div>
    </div>
  </header>

  <!-- Main Content -->
  <main class="max-w-6xl mx-auto px-4 py-8">
    <!-- Fact Cards Grid -->
    <!-- Media Section -->
    <!-- Timeline Section -->
  </main>

  <footer class="bg-slate-800 text-slate-400 py-6 mt-12 text-center text-sm">
    <p>Erstellt für Bildungszwecke. Quelle: [Source]</p>
  </footer>

  <script>
    function toggleTheme() {
      const isDark = document.documentElement.getAttribute('data-theme') === 'dark';
      document.documentElement.setAttribute('data-theme', isDark ? 'light' : 'dark');
      document.getElementById('themeBtn').textContent = isDark ? '🌙 Dark' : '☀️ Light';
    }
  </script>
</body>
</html>
```

---

## Fact Cards Grid

```html
<div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-12">
  <div class="bg-white rounded-xl shadow-sm p-6 border-l-4 border-brand">
    <div class="flex items-center mb-4">
      <i class="fa-solid fa-[icon] text-2xl text-brand mr-3"></i>
      <h2 class="text-xl font-bold">[Card Title]</h2>
    </div>
    <ul class="space-y-2 text-slate-600">
      <li><strong>Key:</strong> Value</li>
    </ul>
  </div>
</div>
```

Border color classes for variety: `border-brand`, `border-orange-500`, `border-blue-600`, `border-purple-600`, `border-green-600`

---

## Media / Video Grid

```html
<section class="mb-12">
  <h2 class="text-2xl font-bold mb-6 border-b-2 border-slate-200 pb-2">
    <i class="fa-solid fa-play-circle mr-2 text-brand"></i> Video- & Audiomaterial
  </h2>
  <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
    <a href="[URL]" target="_blank" class="block group">
      <div class="bg-slate-800 rounded-lg overflow-hidden relative aspect-video flex items-center justify-center">
        <i class="fa-solid fa-play text-white text-3xl opacity-80 group-hover:scale-110 transition-transform"></i>
        <div class="absolute bottom-2 right-2 bg-brand text-white text-xs px-2 py-1 rounded">[Duration/Label]</div>
      </div>
      <h3 class="font-semibold mt-2 text-sm">[Title]</h3>
      <p class="text-xs text-slate-500">[Subtitle / date]</p>
    </a>
  </div>
</section>
```

---

## Timeline Section

```html
<section>
  <h2 class="text-2xl font-bold mb-6 border-b-2 border-slate-200 pb-2">
    <i class="fa-solid fa-clock-rotate-left mr-2 text-slate-600"></i> Chronologie
  </h2>
  <div class="bg-white rounded-xl shadow-sm border border-slate-100 p-6 h-[500px] overflow-y-auto timeline-scroll">
    <div class="relative border-l-2 border-slate-200 ml-3 space-y-8">

      <!-- Most recent first -->
      <div class="relative pl-6">
        <div class="absolute w-4 h-4 bg-brand rounded-full -left-[9px] top-1 border-4 border-white"></div>
        <div class="text-sm text-brand font-bold mb-1">[Date/Time]</div>
        <h3 class="text-lg font-bold text-slate-800">[Event Title]</h3>
        <p class="text-slate-600 mt-1">[Description]</p>
      </div>

    </div>
  </div>
</section>
```

Timeline dot colors: `bg-brand` (latest/key), `bg-orange-500` (humanitarian), `bg-blue-500` (economic), `bg-slate-400` (neutral/background)

---

## Data-Driven Rendering (optional, for large dossiers)

If the dossier has many items, put data in JS and render:

```js
const FACT_CARDS = [
  { title: "Hauptakteure", icon: "crosshairs", color: "brand", items: [...] },
];
const TIMELINE = [
  { date: "3. März", title: "Event", desc: "...", color: "brand" },
];

document.addEventListener('DOMContentLoaded', () => {
  renderFactCards();
  renderTimeline();
});
```

---

## Print Considerations for Type C

Type C pages print well without special body-class tricks because they're already scroll-through.
Just add `.no-print` to interactive elements (theme toggle, print button).
Use `@media print` to reset background colors and remove shadows.
