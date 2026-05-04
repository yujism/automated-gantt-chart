# 📊 Gantt Chart Builder

**Production Planning Tool** — Web app berbasis dark mode untuk membuat Gantt chart production planning dengan kalender Indonesia, drag-and-drop scheduling, dan export ke Excel.

![Version](https://img.shields.io/badge/version-4.0-blue)
![License](https://img.shields.io/badge/license-MIT-green)
![Tech](https://img.shields.io/badge/tech-Vanilla%20JS-yellow)

---

## ✨ Features

### 🎯 Core Features
- **Multi-Product Planning** — Kelola multiple products dalam satu project
- **6 Production Stages** — SM, Production, Packing, Rework SM, Plant to Depo, Stuffing
- **Optional Stages** — Centang/uncheck stage sesuai kebutuhan (bukan semua stage wajib)
- **Flexible Scheduling** — Sequential (auto) atau parallel (manual drag)
- **Indonesian Calendar** — Tanggal merah Indonesia 2025-2026 auto OFF
- **Weekend Rules** — Sabtu/Minggu = 1 shift (visualisasi lebih pudar)
- **Drag & Drop Timeline** — Geser bar kiri/kanan untuk reschedule
- **Export to Excel** — Full-color Excel export dengan formatting identik ke UI

### 🎨 UI/UX
- **Dark Mode** — Minimalist futuristic design dengan palet pastel
- **Real-time Visual** — Update langsung saat drag atau edit
- **Color-coded Stages** — Setiap stage punya warna berbeda
- **Tooltips** — Hover untuk info, drag untuk preview date
- **Responsive Legend** — Auto-update dengan stage yang aktif

---

## 🚀 Quick Start

### Option 1: Direct Use (No Installation)
1. Download `gantt_chart_builder_v4.html`
2. Buka file di browser (Chrome/Edge/Firefox recommended)
3. Done! Langsung bisa dipakai

### Option 2: Local Server (Recommended for Development)
```bash
# Clone repo
git clone https://github.com/yourusername/gantt-chart-builder.git
cd gantt-chart-builder

# Serve dengan Python (atau web server lain)
python -m http.server 8000

# Buka browser
# http://localhost:8000/gantt_chart_builder_v4.html
```

---

## 📖 How to Use

### 1️⃣ Setup Project
1. **Isi Project Name** di bagian atas sidebar
2. **Klik "+ Add"** atau **"+ Tambah Product"** untuk tambah product baru

### 2️⃣ Configure Products
Untuk setiap product:
- **Nama Product** — Klik field nama untuk edit
- **Start Date** — Pilih tanggal mulai production
- **Stages** — Centang stage yang dibutuhkan, isi jumlah hari per stage

**Contoh:**
```
Product: Sandal Model X
Start Date: 2025-06-01
✓ SM — 3 hari
✓ Production — 10 hari
✓ Packing — 2 hari
✗ Rework SM — (skip)
✓ Plant to Depo — 1 hari
✓ Stuffing — 1 hari
```

### 3️⃣ View Gantt Chart
Gantt chart auto-generate di panel kanan dengan:
- **Header bulan** — Group per bulan
- **Tanggal** — Warna merah = tanggal merah, biru = weekend
- **Bars** — Warna solid = hari kerja, pudar = weekend, lebih pudar = OFF

### 4️⃣ Drag to Reschedule (Advanced)
**Sequential Mode (Default):**
- Stage berjalan berurutan
- Setiap stage tunggu stage sebelumnya selesai

**Parallel Mode (Manual):**
1. **Drag bar** stage ke kiri/kanan
2. Stage jadi **pinned** (manual start) — ditandai **⚲**
3. Stage lain yang masih auto akan adjust otomatis
4. **Double-click** bar untuk reset ke sequential

**Tooltip saat drag:**
```
Production → 2025-06-15 (+3d)
```

### 5️⃣ Export to Excel
1. Klik **"⬇ Export to Excel"**
2. File `.xlsx` auto-download dengan:
   - Format identik ke UI (dark background, colored bars)
   - Frozen panes di header
   - Weekend/holiday tinting
   - Ready untuk presentasi atau sharing

---

## 🎨 Stage Colors

| Stage | Color | Hex |
|-------|-------|-----|
| **SM** | Purple | `#c4b5fd` |
| **Production** | Cyan | `#67e8f9` |
| **Packing** | Red | `#fca5a5` |
| **Rework SM** | Pink | `#f9a8d4` |
| **Plant to Depo** | Slate | `#94a3b8` |
| **Stuffing** | Green | `#86efac` |

---

## 🗓️ Calendar Rules

### Tanggal Merah Indonesia (Hardcoded 2025-2026)
Semua aktivitas **OFF** di tanggal merah:
- Tahun Baru, Imlek, Nyepi, Idul Fitri, Waisak, Kenaikan Isa Al-Masih
- Hari Buruh, Isra Mi'raj, Pancasila, Idul Adha, Tahun Baru Islam
- Maulid Nabi, HUT RI, Natal

### Weekend Rules
- **Sabtu & Minggu** = **1 shift** (bukan OFF total)
- Visualisasi: warna bar lebih pudar (55% opacity vs 100%)
- Hari kerja tetap dihitung **work days only** (tanggal merah di-skip)

### Work Days Calculation
```javascript
// Contoh: 5 work days dari 2025-06-01
// Jika 2025-06-01 = Minggu (tanggal merah)
// → Skip ke 2025-06-02 (Senin)
// → Hitung 5 hari kerja (skip weekend & holidays)
// → Selesai di 2025-06-06
```

---

## 🛠️ Technical Details

### Tech Stack
- **Pure HTML/CSS/JS** — No framework, no build tools
- **ExcelJS 4.3.0** — Excel export via CDN
- **Vanilla DOM** — Lightweight, fast rendering

### Architecture
```
products[]
  ├─ id, name, startDate
  └─ stages{}
       └─ SM, Production, Packing, ...
            ├─ days (integer)
            ├─ enabled (boolean)
            └─ manualStart (YYYY-MM-DD | null)
```

### Key Functions

**`computeSchedule(product)`**
```javascript
// Input: product object
// Output: array of {stageKey, start, end, manual}
// Logic:
//   - Sequential: start = max(previous ends)
//   - Manual: start = manualStart (user-pinned)
//   - Work days only (skip holidays)
```

**`renderGantt()`**
```javascript
// 1. Compute min/max dates from all products
// 2. Generate date range (minD → maxD + 3 buffer)
// 3. Render table: months, dates, stage bars
// 4. Attach data attrs for drag system
```

**Drag System**
```javascript
// mousedown → capture prodId, stageKey, origStart
// mousemove → delta = pixels / 24px per day
//           → updateDragVisual() real-time
// mouseup   → commit manualStart = origStart + deltaD
// dblclick  → reset manualStart = null
```

### File Structure
```
gantt_chart_builder_v4.html
├─ <style>       — Dark mode CSS vars + layout
├─ <div.wrap>    — Sidebar + Gantt split view
├─ <script>      — Business logic (1 file, ~460 lines)
│   ├─ HOLIDAYS     — Indonesia 2025-2026
│   ├─ STAGES       — Config {key, label, color, argb}
│   ├─ products[]   — State
│   ├─ computeSchedule()
│   ├─ renderSidebar(), renderGantt()
│   ├─ Drag handlers
│   └─ exportExcel()
└─ ExcelJS CDN
```

---

## 🎓 Use Cases

### ✅ Production Planning
- Jadwal produksi multi-SKU di pabrik sandal
- Visual timeline untuk koordinasi SM, Production, Packing
- Track lead time per product

### ✅ Project Management
- Timeline deliverables dengan dependency
- Resource allocation (stage = resource type)
- Deadline tracking dengan holiday awareness

### ✅ Supply Chain
- Jadwal Plant to Depo + Stuffing
- Container planning untuk export
- Visibility end-to-end dari raw material → shipment

---

## 🐛 Known Limitations

1. **Holiday List** — Hardcoded 2025-2026 only (perlu manual update untuk tahun lain)
2. **Single Page** — No data persistence (refresh = reset)
3. **No Undo** — Drag action langsung commit (workaround: refresh page)
4. **Excel Formula** — Export static values only (bukan formula dinamis)
5. **Browser Only** — No mobile app, desktop app, atau API

---

## 🔮 Future Roadmap

- [ ] **LocalStorage** — Auto-save state
- [ ] **JSON Import/Export** — Save/load project sebagai .json
- [ ] **Dynamic Holidays** — API integration atau user-defined holidays
- [ ] **Undo/Redo** — History stack untuk drag actions
- [ ] **Multi-user** — Real-time collaboration (requires backend)
- [ ] **Template Library** — Pre-built templates untuk berbagai industri
- [ ] **Print Mode** — PDF export + printer-friendly layout

---

## 📝 License

MIT License — Free to use, modify, distribute.

```
Copyright (c) 2025 Wahyu Aji L

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software...
```

---

## 🤝 Contributing

Contributions welcome! Untuk bug reports, feature requests, atau pull requests:

1. Fork this repo
2. Create feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open Pull Request

---

## 💬 Support

- **Issues:** [GitHub Issues](https://github.com/yujism/gantt-chart-builder/issues)
- **Discussions:** [GitHub Discussions](https://github.com/yujism/gantt-chart-builder/discussions)
- **Email:** wahyual2024@gmail.com

---

## 🙏 Acknowledgments

- **ExcelJS** — Excel generation library
- **Indonesian Public Holidays** — Data from [publicholidays.co.id](https://publicholidays.co.id)
- **Design Inspiration** — Linear, Notion, Asana

---

## 📸 Screenshots

### Main Interface

![Main Interface](gantt-chart-screenshots.png)


---

**Made with ❤️ for Production Planners**
