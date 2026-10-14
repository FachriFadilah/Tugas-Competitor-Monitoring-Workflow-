# Tugas-Competitor-Monitoring-Workflow-
# Competitor Monitoring Workflow (n8n)

Workflow otomatis untuk memantau berita terbaru tentang sebuah brand dan dua kompetitornya, menghitung **Share of Voice (SoV)**, menganalisis sentimen dengan AI, lalu mengirim laporan **PDF** ke Telegram.

> Tugas 5 - [Nama Mata Kuliah]
> Disusun oleh: [Nama] - [NIM]

---

## Fitur

- Pencarian berita terbaru untuk 3 brand secara **paralel** menggunakan Serper API
- Perhitungan **Share of Voice** dengan skrip JavaScript
- Analisis sentimen dan **skor reputasi (1-10)** menggunakan AI (OpenRouter)
- Laporan HTML berisi **ringkasan eksekutif** dan **tabel perbandingan**
- Konversi HTML ke **PDF** dengan PDFShift
- Pengiriman laporan otomatis lewat **Telegram**

## Alur Workflow

```
Manual Trigger
   ├─► Serper - Brand Utama (Tokopedia)
   ├─► Serper - Kompetitor 1 (Shopee)      <- 3 node paralel
   └─► Serper - Kompetitor 2 (Lazada)
            ▼
          Merge (Append, 3 input)
            ▼
   Hitung SoV (JavaScript)
            ▼
   Analisis Sentimen AI (Basic LLM Chain + OpenRouter)
            ▼
   Susun HTML (JavaScript)
            ▼
   PDFShift - Convert PDF (HTTP Request)
            ▼
   Kirim ke Telegram
```

## Daftar Node

| Node | Tipe | Fungsi |
|---|---|---|
| When clicking 'Execute workflow' | Manual Trigger | Memulai workflow |
| Serper - Brand Utama / Kompetitor 1 / Kompetitor 2 | HTTP Request | Mengambil berita dari endpoint `/news` Serper |
| Merge | Merge | Menggabungkan hasil ketiga pencarian |
| Hitung SoV | Code (JavaScript) | Menghitung mention dan Share of Voice |
| Analisis Sentimen AI | Basic LLM Chain | Menilai sentimen, skor reputasi, dan membuat ringkasan eksekutif |
| OpenRouter Chat Model | Chat Model | Model AI yang dipakai oleh LLM Chain |
| Susun HTML | Code (JavaScript) | Menyusun laporan HTML |
| PDFShift - Convert PDF | HTTP Request | Mengubah HTML menjadi PDF |
| Kirim ke Telegram | Telegram | Mengirim PDF ke chat |

## Metrik Share of Voice

```
SoV brand = (mention brand / total mention semua brand) x 100%
```

Sebuah berita dihitung sebagai **mention** jika judul atau snippet-nya menyebut nama brand.

## Contoh Hasil

Periode pencarian: 1 minggu terakhir.

| Brand | Mention | Share of Voice | Sentimen | Skor Reputasi |
|---|---|---|---|---|
| Tokopedia | 9 | 33,3% | Positif | 8/10 |
| Shopee | 10 | 37,0% | Positif | 9/10 |
| Lazada | 8 | 29,6% | Netral | 7/10 |

## Prasyarat

- Akun [n8n](https://n8n.io) (Cloud atau self-hosted)
- API key [Serper](https://serper.dev)
- API key [OpenRouter](https://openrouter.ai) (atau Gemini)
- API key [PDFShift](https://pdfshift.io)
- Bot Telegram dari [@BotFather](https://t.me/BotFather) dan Chat ID dari [@userinfobot](https://t.me/userinfobot)

## Cara Menggunakan

1. Download file workflow `.json` dari repository ini.
2. Buka n8n, lalu pilih menu `...` -> **Import from File**.
3. Ganti placeholder berikut:
   - `YOUR_SERPER_API_KEY` pada header `X-API-KEY` di ketiga node Serper
   - `YOUR_TELEGRAM_CHAT_ID` pada node Telegram
4. Buat credential di n8n untuk:
   - **OpenRouter** (API key)
   - **PDFShift** (Header Auth `X-API-Key` atau Basic Auth, sesuai dokumentasi PDFShift)
   - **Telegram** (Bot Token)
5. Ubah nama brand pada kotak JSON di tiap node Serper dan pada array `brands` di node **Hitung SoV** (nama harus sama).
6. Pada node OpenRouter Chat Model, pilih model yang tersedia (model berakhiran `:free` jika ingin gratis).
7. Klik **Execute Workflow**. PDF laporan akan masuk ke Telegram.

## Catatan

- Nama node di dalam kode JavaScript harus **sama persis** dengan nama node di canvas.
- Serper membatasi hasil `/news` sebesar 10 berita per request.
- Model AI gratis kadang penuh atau lambat. Jika error, ganti model atau coba lagi.
- Jangan menyimpan API key, Bot Token, atau Chat ID asli di repository publik.

## Teknologi

n8n, Serper API, OpenRouter, PDFShift, Telegram Bot API, JavaScript

## Struktur Repository

```
.
├── README.md
├── competitor-monitoring.json
└── laporan-tugas-5.pdf
```
