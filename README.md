# 🤖 IT Helpdesk AI Assistant
> Capstone Project — IBM SkillsBuild x Hacktiv8
> AI Agent berbasis RAG (Retrieval-Augmented Generation) yang menjawab pertanyaan teknis IT karyawan secara otomatis berdasarkan dokumen SOP perusahaan.

---

## 📌 Daftar Isi
- [Tentang Project](#-tentang-project)
- [Demo](#-demo)
- [Teknologi yang Digunakan](#-teknologi-yang-digunakan)
- [Arsitektur Sistem](#-arsitektur-sistem)
- [Cara Setup dari Nol](#-cara-setup-dari-nol)
- [Prompt Template](#-prompt-template)
- [Contoh Hasil](#-contoh-hasil)
- [Struktur Project](#-struktur-project)

---

## 📖 Tentang Project

### Latar Belakang
Karyawan perusahaan sering mengalami kendala teknis IT seperti masalah login, koneksi internet, VPN, dan perangkat keras yang menghambat produktivitas. Tim IT yang terbatas tidak bisa selalu merespons cepat, sehingga karyawan harus menunggu lama.

### Solusi
IT Helpdesk AI Assistant hadir sebagai chatbot berbasis AI yang dapat menjawab pertanyaan teknis karyawan secara **real-time**, **otomatis**, dan **akurat** berdasarkan dokumen SOP IT resmi perusahaan.

### Fitur Utama
- ✅ Jawab pertanyaan teknis IT secara otomatis
- ✅ Berikan solusi step-by-step yang mudah dipahami
- ✅ Arahkan eskalasi ke tim IT jika masalah kompleks
- ✅ Tolak pertanyaan di luar konteks IT dengan sopan
- ✅ Berbasis dokumen SOP sehingga jawaban selalu akurat

---

## 🎬 Demo

**Contoh pertanyaan yang bisa dijawab:**
```
"Laptop saya tiba-tiba tidak bisa menyala, apa yang harus saya lakukan?"
"Bagaimana cara reset password email saya?"
"VPN saya tidak bisa konek, server apa yang harus digunakan?"
"Software apa yang tersedia untuk video conference?"
```

**Contoh pertanyaan yang ditolak dengan sopan:**
```
"Tolong buatkan saya kopi" → AI menolak & tetap menawarkan bantuan IT
"Siapa presiden Indonesia?" → Di luar konteks SOP IT
```

---

## 🛠 Teknologi yang Digunakan

| Teknologi | Fungsi |
|---|---|
| **Langflow** | Platform visual untuk membangun AI workflow |
| **Astra DB (DataStax)** | Vector database untuk menyimpan embedding dokumen |
| **Google Gemini** | Language Model untuk menghasilkan jawaban |
| **Google Generative AI Embedding** | Mengubah teks dokumen menjadi vector embedding |
| **RAG (Retrieval-Augmented Generation)** | Teknik AI untuk menjawab berdasarkan dokumen |

---

## 🏗 Arsitektur Sistem

```
┌─────────────────────────────────────────────────────────┐
│                      FLOW 1                             │
│              (Simpan Dokumen ke Database)               │
│                                                         │
│  [PDF SOP IT] → [Read File] → [Split Text] →           │
│  [Google Embedding] → [Astra DB]                        │
└─────────────────────────────────────────────────────────┘
                          ↓ (data tersimpan)
┌─────────────────────────────────────────────────────────┐
│                      FLOW 2                             │
│                 (Chatbot IT Helpdesk)                   │
│                                                         │
│  [Chat Input] ──────────────────────────────┐          │
│                                             ↓          │
│  [Google Embedding] → [Astra DB Search] → [Parser] →  │
│  [Prompt Template] → [Language Model] → [Chat Output] │
└─────────────────────────────────────────────────────────┘
```

### Penjelasan Alur:
1. **Input** → Karyawan kirim pertanyaan via Chat Input
2. **Proses** → Astra DB mencari informasi relevan dari SOP menggunakan semantic search → Parser mengubah hasil ke teks → Prompt Template menggabungkan konteks + pertanyaan → Gemini memproses dan menghasilkan jawaban
3. **Output** → Jawaban teknis yang jelas, terstruktur, dan actionable

---

## 🚀 Cara Setup dari Nol

### Prasyarat
- [ ] Akun **Langflow** (download di langflow.org atau gunakan versi cloud)
- [ ] Akun **Astra DB** → daftar di [astra.datastax.com/signup](https://astra.datastax.com/signup)
- [ ] **Gemini API Key** → buat di [aistudio.google.com/api-keys](https://aistudio.google.com/api-keys)
- [ ] File **PDF dokumen SOP** yang akan dijadikan knowledge base

---

### Step 1: Setup Astra DB

1. Daftar/login di [astra.datastax.com](https://astra.datastax.com)
2. Klik **"Create Database"**
3. Isi konfigurasi:
   - Type: **Serverless (Vector)**
   - Database name: `company_documents` (bebas)
   - Provider: **Amazon Web Services**
   - Region: **us-east-2**
4. Klik **"Create Database"** dan tunggu hingga status **Active**
5. Buka tab **"Quickstart"** → klik **"Generate Token"**
6. **PENTING: Segera copy dan simpan token!** Token akan hilang jika halaman ditutup
7. Buat collection baru:
   - Name: `hr_document` (bebas)
   - Embedding generation method: **Bring your own**
   - Dimensions: `3072`

---

### Step 2: Dapatkan Gemini API Key

1. Buka [aistudio.google.com/api-keys](https://aistudio.google.com/api-keys)
2. Klik **"Create API Key"**
3. Pilih project Google yang ada
4. Copy API Key dan simpan

---

### Step 3: Setup Langflow

1. Buka aplikasi Langflow
2. Di halaman Projects, klik tombol **Import** (ikon upload)
3. Pilih file `IT_Helpdesk_Assistant.json` dari repo ini
4. Jika muncul notifikasi update komponen, klik **"Review All"** → **"Update Components"**

---

### Step 4: Konfigurasi Komponen

#### Flow 1 - Google Generative AI Embeddings:
- Google Generative AI API Key: `[Gemini API Key kamu]`
- Model Name: `models/gemini-embedding-001`

#### Flow 1 & Flow 2 - Astra DB:
- Astra DB Application Token: `[Token AstraDB kamu]`
- Database: pilih database yang sudah dibuat
- Collection: pilih collection yang sudah dibuat

#### Flow 2 - Language Model:
- Language Model: `gemini-2.5-flash`
- API Key: `[Gemini API Key kamu]`
- Matikan toggle **System Message**

---

### Step 5: Jalankan Flow 1 (Upload Dokumen)

1. Upload file PDF SOP ke komponen **Read File**
2. Klik tombol **Play** ▶ pada komponen **Astra DB**
3. Tunggu hingga proses selesai (dokumen tersimpan ke database)
4. Jika punya lebih dari 1 PDF, ulangi langkah ini untuk setiap file

---

### Step 6: Test Flow 2 (Chatbot)

1. Klik tombol **Playground** di pojok kanan atas
2. Ketik pertanyaan di kolom chat
3. Tunggu respons dari AI
4. Selamat! AI Helpdesk kamu sudah berjalan 🎉

---

## 📝 Prompt Template

```
Anda adalah IT Helpdesk Assistant yang bertugas membantu karyawan 
PT. Maju Teknologi Indonesia dalam menyelesaikan masalah teknis IT 
berdasarkan dokumen SOP resmi perusahaan.

Konteks dari dokumen SOP:
{context}

---

Berdasarkan konteks SOP di atas, jawab pertanyaan berikut sebagai 
IT Support yang profesional, sigap, dan solutif.

Dalam setiap jawaban kamu:
- Identifikasi masalah yang dialami pengguna secara singkat
- Berikan langkah-langkah solusi yang jelas dan berurutan (step by step)
- Sebutkan estimasi waktu penanganan jika relevan
- Cantumkan kontak atau channel IT yang tepat jika masalah perlu eskalasi
- Gunakan bahasa Indonesia yang jelas, ringkas, dan mudah dipahami 
  oleh karyawan non-teknis
- Jika pertanyaan tidak tercakup dalam SOP, sampaikan dengan sopan 
  dan arahkan ke tim IT langsung

Pertanyaan karyawan:
{question}

Jawaban IT Helpdesk:
```

---

## 💡 Contoh Hasil

**Pertanyaan:** *"Laptop saya tiba-tiba tidak bisa menyala"*

**Jawaban AI:**
```
Halo Bapak/Ibu,
Saya memahami bahwa laptop Anda tiba-tiba tidak bisa menyala.
Berikut langkah-langkah yang bisa dicoba:

1. Periksa Sumber Daya Listrik
   - Pastikan kabel charger terhubung dengan baik
   - Coba stopkontak lain

2. Periksa Indikator Lampu
   - Perhatikan apakah ada lampu indikator yang menyala

3. Lakukan Power Cycle
   - Cabut charger, tahan tombol power 15-30 detik
   - Pasang kembali dan coba nyalakan

Jika masih belum berhasil, segera hubungi tim IT:
📧 it.support@majuteknologi.com | 📞 Ext. 1001
```

---

## 📁 Struktur Project

```
IT-Helpdesk-Assistant/
│
├── README.md                    # Dokumentasi ini
├── IT_Helpdesk_Assistant.json   # File flow Langflow (import ini!)
└── IT_Helpdesk_SOP.pdf          # Dokumen SOP IT sebagai knowledge base
```

---

## 🔮 Potensi Pengembangan

- 🌐 **Multi-bahasa** — tambah dukungan Bahasa Inggris
- 🎫 **Integrasi Ticketing** — auto-buat tiket jika masalah perlu eskalasi
- 📊 **Dashboard Analitik** — rekap pertanyaan paling sering diajukan
- 💬 **Integrasi Slack/Teams** — akses langsung dari platform komunikasi internal
- 🔄 **Auto-update SOP** — knowledge base otomatis update saat dokumen direvisi

---

## 📚 Referensi

- [Langflow Documentation](https://docs.langflow.org)
- [Astra DB Documentation](https://docs.datastax.com/en/astra/home/astra.html)
- [Google Gemini API](https://aistudio.google.com)
- [IBM SkillsBuild](https://skillsbuild.org)

---

> Dibuat dengan ❤️ sebagai bagian dari program **IBM SkillsBuild x Hacktiv8**
