
# Module 00 — Operating System Fundamentals

## 1. Apa itu Sistem Operasi (OS)?
Sistem Operasi (OS) adalah "Manajer Utama" di dalam komputer. Tanpa OS, komponen komputer seperti CPU, RAM, dan Harddisk hanyalah perangkat keras mati yang tidak bisa saling berkomunikasi atau menjalankan aplikasi.

## 2. Analogika Komponen Inti OS

* **Kernel (Koki Utama):**
  Bagian terpenting dari OS yang bekerja di belakang layar. Kernel bertugas mengatur pembagian tugas hardware, seperti membagi kapasitas memori dan memproses perintah CPU.
* **Shell (Pelayan / Kasir):**
  Antarmuka tempat pengguna memberikan perintah. Shell menerima teks/instruksi dari kita, lalu menerjemahkannya agar bisa dimengerti oleh Koki (Kernel).
* **Process Management (Manajemen Antrean Tugas):**
  Aturan OS dalam mengatur jalannya berbagai program sekaligus agar komputer tidak *crash* atau *lag*.
* **Memory Management (Manajemen Ruang Kerja):**
  Pengaturan pembagian tempat di RAM agar setiap program memiliki area kerjanya masing-masing tanpa mengganggu program lain.
* **Filesystem (Lemari Arsip):**
  Cara OS menyusun, menamai, dan menyimpan file agar mudah dicari dan tidak tertukar.

## 3. Beda Mendasar: Linux vs Windows

| Fitur | Linux 🐧 | Windows 🪟 |
| :--- | :--- | :--- |
| **Gaya Utama** | Berbasis Teks/Command Line (Ringan & Cepat) | Berbasis Visual/Grafis (Mudah Digunakan) |
| **Penyimpanan File** | Semuanya dianggap file, disusun dari titik awal `/` (Root) | Terbagi berdasarkan drive letter (`C:\`, `D:\`) |
| **Pengaturan Sistem** | Menggunakan file teks sederhana yang mudah diedit | Terpusat dalam sistem database (*Windows Registry*) |
| **Akses Tertinggi** | Pengguna utama disebut `root` | Pengguna utama disebut `Administrator` |

## 4. Kenapa Memahami OS Penting untuk Cybersecurity?
Dalam dunia keamanan siber, pemahaman OS ibarat memahami denah dan struktur bangunan. Kita harus tahu lokasi pintu, denah ruangan (file log/konfigurasi), serta siapa saja yang memiliki kunci akses (permission/hak akses) untuk bisa menjaga atau memeriksa keamanan sistem tersebut secara menyeluruh.
