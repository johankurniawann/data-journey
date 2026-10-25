# Procurement Starter Kit

Dataset latihan untuk roadmap Data Analyst → Data Engineer. Semua data **fiktif** dan dibuat khusus
untuk latihan: grup 5 rumah sakit, 43 supplier, 252 item, 3.200 PO selama 2024–2025.
Kamu memakai dataset yang sama dari Fase 1 sampai Fase 12, jadi waktu habis untuk belajar
fitur baru, bukan mempelajari data baru.

## Isi folder

| Folder / file | Dipakai di | Isi |
|---|---|---|
| `phase1_excel/po_export_2025H1_messy.csv` | Fase 1 | Ekspor PO Jan–Jun 2025 yang sengaja dibuat berantakan |
| `data/*.csv` | Fase 3, 5, 7, 8 | 6 tabel bersih: sites, suppliers, items, purchase_orders, po_lines, receipts |
| `phase2_sql/procurement_seed.sql` | Fase 2+ | Membuat dan mengisi 6 tabel di PostgreSQL atau DuckDB |
| `phase2_sql/sql_solutions.sql` | Fase 2 | Kunci jawaban 21 latihan SQL |
| `phase5_python/monthly_reports/` | Fase 5 | 12 laporan bulanan 2025 (.xlsx) dengan baris judul di atas header |
| `phase5_python/phase5_solution.py` | Fase 5 | Contoh solusi latihan Python |

**Kerjakan dulu, baru buka kunci jawaban.** Kalau hasilmu beda, cari tahu kenapa. Di situlah belajarnya.

## Skema tabel

```
sites            (site_id PK, site_name, city, bed_count)
suppliers        (supplier_id PK, supplier_name, city, category, payment_terms_days)
items            (item_id PK, item_name, category, uom, std_price)
purchase_orders  (po_id PK, po_number, supplier_id FK, site_id FK, po_date, promised_date, status)
po_lines         (line_id PK, po_id FK, line_no, item_id FK, qty, unit_price)
receipts         (receipt_id PK, line_id FK, receipt_date, qty_received)
```

- Spend = `qty * unit_price`. Abaikan PO berstatus `Cancelled` saat menghitung spend.
- Status PO: `Open`, `Partially Received`, `Closed`, `Cancelled`.
- Sebagian `promised_date` sengaja kosong (86 PO). Satu line bisa diterima dalam 2 kali pengiriman.
- 3 supplier terdaftar tetapi tidak pernah menerima PO.

## Cara memuat database

**PostgreSQL + DBeaver:** buat database `procurement`, buka `procurement_seed.sql` di DBeaver,
lalu jalankan sebagai script (*Execute SQL Script*, Alt+X). Selesai dalam beberapa detik.

**DuckDB (tanpa instalasi server):**
```
duckdb procurement.duckdb
.read phase2_sql/procurement_seed.sql
```

Cek: `SELECT COUNT(*) FROM po_lines;` harus menghasilkan **9.636**.

## Angka untuk cek mandiri

**Fase 1 (file messy, setelah dibersihkan)**
- Baris kosong 15, header berulang 3, duplikat persis 22
- Baris data bersih: **2.424**
- Supplier unik: **39** (ditambah 10 baris tanpa nama supplier)
- Tanggal PO: 1 Jan 2025 s.d. 30 Jun 2025
- Baris dengan Total Amount ≠ Qty × Unit Price: **54**
- Total spend H1 2025 (Qty × Unit Price): **Rp 54.405.494.310**

**Fase 2 (SQL)**
- Jumlah PO: Closed 2.991, Cancelled 112, Partially Received 49, sisanya Open
- Spend 2024 ≈ Rp 83,43 miliar, spend 2025 ≈ Rp 112,04 miliar
- Supplier tanpa PO: 3, line belum diterima: 199, supplier pembentuk 80% spend: 19

**Fase 3 (Power BI):** fact tanpa PO Cancelled = **9.301** baris, YoY 2025 ≈ +34%

**Fase 5 (Python):** 12 file bulanan digabung = **5.230** baris; ABC 2025 ≈ 105 item A, 68 B, 79 C

## Aturan penting

Dataset ini aman dipublikasikan di GitHub karena fiktif. Jangan pernah mengunggah data asli
dari tempat kerja ke portofolio, repo publik, atau alat AI tanpa izin.
