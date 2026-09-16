
# Modul 1: Konsep Dasar OS & CLI

Modul ini membahas fundamental Sistem Operasi dan praktik langsung melakukan *system enumeration* (pengumpulan informasi sistem) melalui **Command Line Interface (CLI)**.

---

## 📖 Bagian 1: Operating System Fundamentals

### 1. Apa itu Sistem Operasi (OS)?

Sistem Operasi (OS) adalah "Manajer Utama" di dalam komputer. Tanpa OS, komponen komputer seperti CPU, RAM, dan Harddisk hanyalah perangkat keras mati yang tidak bisa saling berkomunikasi atau menjalankan aplikasi.

### 2. Analogi Komponen Inti OS

* **Kernel (Koki Utama):** Bagian terpenting dari OS yang bekerja di belakang layar. Kernel bertugas mengatur pembagian tugas hardware, seperti membagi kapasitas memori dan memproses perintah CPU.
* **Shell (Pelayan / Kasir):** Antarmuka tempat pengguna memberikan perintah. Shell menerima teks/instruksi dari kita, lalu menerjemahkannya agar bisa dimengerti oleh Kernel.
* **Process Management (Manajemen Antrean Tugas):** Aturan OS dalam mengatur jalannya berbagai program sekaligus agar komputer tidak *crash* atau *lag*.
* **Memory Management (Manajemen Ruang Kerja):** Pengaturan pembagian tempat di RAM agar setiap program memiliki area kerjanya masing-masing tanpa mengganggu program lain.
* **Filesystem (Lemari Arsip):** Cara OS menyusun, menamai, dan menyimpan file agar mudah dicari dan tidak tertukar.

### 3. Beda Mendasar: Linux vs Windows

| Fitur                 | Linux 🐧                                                   | Windows 🪟                                        |
| :-------------------- | :--------------------------------------------------------- | :------------------------------------------------ |
| **Gaya Utama**        | Berbasis Teks/Command Line (Ringan & Cepat)                | Berbasis Visual/Grafis (Mudah Digunakan)          |
| **Penyimpanan File**  | Semuanya dianggap file, disusun dari titik awal `/` (Root) | Terbagi berdasarkan *drive letter* (`C:\`, `D:\`) |
| **Pengaturan Sistem** | Menggunakan file teks sederhana yang mudah diedit          | Terpusat dalam sistem database (Windows Registry) |
| **Akses Tertinggi**   | Pengguna utama disebut `root`                              | Pengguna utama disebut Administrator              |

### 4. Kenapa Memahami OS Penting untuk Cybersecurity?

Dalam dunia keamanan siber, pemahaman OS ibarat memahami denah dan struktur bangunan.

Kita harus tahu lokasi pintu, denah ruangan (file log/konfigurasi), serta siapa saja yang memiliki kunci akses (*permission*/hak akses) untuk bisa menjaga atau memeriksa keamanan sistem tersebut secara menyeluruh.

---

# 🔬 Bagian 2: Lab 01 — System Information

## Objective

Memahami cara mengidentifikasi informasi dasar sistem Linux melalui CLI.

Pada lab ini, informasi sistem dikumpulkan secara langsung dari Ubuntu.

## Environment

* **OS:** Ubuntu 22.04.5 LTS (Jammy Jellyfish)
* **Shell:** Bash
* **Hostname:** `ubuntu-lab`
* **Architecture:** x86-64

## Commands Used

```bash
uname -a
cat /etc/os-release
hostnamectl
whoami
id
pwd
```

---

## Practice & Results

### 1. Identifikasi Kernel dan Arsitektur

#### Command

`uname` digunakan untuk menampilkan informasi mengenai sistem, termasuk kernel dan arsitektur.

```bash
uname -a
```

#### Output

```text
Linux ubuntu-lab 6.8.0-124-generic #124~22.04.1-Ubuntu SMP PREEMPT_DYNAMIC Tue May 26 21:05:19 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux
```

#### Observation

Sistem menjalankan kernel Linux `6.8.0-124-generic` dengan arsitektur `x86_64`.

---

### 2. Identifikasi Distribusi OS

#### Command

```bash
cat /etc/os-release
```

#### Output

```text
PRETTY_NAME="Ubuntu 22.04.5 LTS"
NAME="Ubuntu"
VERSION_ID="22.04"
VERSION="22.04.5 LTS (Jammy Jellyfish)"
ID=ubuntu
ID_LIKE=debian
```

#### Observation

Sistem menggunakan **Ubuntu 22.04.5 LTS (Jammy Jellyfish)**.

Field `ID_LIKE=debian` menunjukkan bahwa Ubuntu merupakan distribusi yang memiliki hubungan erat dengan Debian.

---

### 3. Detail Hostname

#### Command

```bash
hostnamectl
```

#### Output

```text
Static hostname: ubuntu-lab
        Chassis: laptop
Operating System: Ubuntu 22.04.5 LTS
          Kernel: Linux 6.8.0-124-generic
    Architecture: x86-64
  Hardware Model: HP Laptop (Redacted)
```

#### Observation

Informasi yang berhasil dikumpulkan meliputi:

* Hostname: `ubuntu-lab`
* Operating System: Ubuntu 22.04.5 LTS
* Kernel: Linux 6.8.0-124-generic
* Architecture: x86-64
* Hardware Model: HP Laptop

---

### 4. Identifikasi User Aktif

#### Commands

```bash
whoami
id
```

#### Output

```text
user

uid=1000(user) gid=1000(user) groups=1000(user),27(sudo),999(docker)
```

#### Observation

Shell dijalankan oleh user `user`.

Detail identitas:

* **UID:** `1000`
* **GID:** `1000`
* **Groups:** `user`, `sudo`, `docker`

Keanggotaan pada grup `sudo` dan `docker` menunjukkan bahwa user memiliki akses administratif tertentu yang perlu diperhatikan dalam security assessment.

---

### 5. Cek Direktori Kerja

#### Command

```bash
pwd
```

#### Output

```text
/home/user/linux-cli-survival-lab/01-Basic-OS-and-CLI
```

#### Observation

Direktori kerja saat ini berada di:

```text
/home/user/linux-cli-survival-lab/01-Basic-OS-and-CLI
```

---

# 🔐 Analysis & Security Relevance

Dalam lingkungan administrasi atau **security assessment yang terotorisasi**, informasi seperti OS, kernel, arsitektur, hostname, user aktif, UID/GID, dan group membership sangat krusial dalam fase **Enumeration**.

Beberapa informasi tersebut dapat digunakan untuk memahami attack surface suatu sistem.

Contohnya:

* **Kernel version** dapat digunakan untuk memetakan potensi kerentanan atau CVE yang relevan.
* **OS distribution dan version** membantu menentukan software repository, konfigurasi, dan security update yang digunakan.
* **Hostname** memberikan informasi mengenai identitas sistem dalam jaringan.
* **User dan UID/GID** membantu memahami konteks privilege user yang sedang aktif.
* **Group membership** seperti `sudo` dan `docker` dapat menunjukkan akses tambahan yang dimiliki user.

> Semua aktivitas security assessment harus dilakukan pada sistem yang dimiliki sendiri atau sistem yang telah memberikan izin pengujian.

---

# 📚 Lessons Learned

Dari lab ini saya mempelajari cara:

* Mengidentifikasi kernel Linux menggunakan `uname`.
* Mengidentifikasi versi dan distribusi OS menggunakan `/etc/os-release`.
* Mengumpulkan informasi hostname dan hardware menggunakan `hostnamectl`.
* Memeriksa user aktif menggunakan `whoami`.
* Memeriksa UID, GID, dan group membership menggunakan `id`.
* Mengetahui direktori kerja menggunakan `pwd`.
* Mengumpulkan informasi dasar sistem Linux melalui CLI tanpa menggunakan GUI.
* Memahami pentingnya **system enumeration** dalam proses security assessment.

---

# 🎯 Lab Status

**Status:** ✅ Completed

**Focus:** Linux Fundamentals & System Enumeration

**Next:** Filesystem, Permissions & Privilege Analysis
