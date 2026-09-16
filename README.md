
# 🐧 Linux CLI Survival Lab

![Linux](https://img.shields.io/badge/Linux-CLI-black?logo=linux\&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu-22.04-orange?logo=ubuntu\&logoColor=white)
![Bash](https://img.shields.io/badge/Shell-Bash-4EAA25?logo=gnu-bash\&logoColor=white)
![VirtualBox](https://img.shields.io/badge/Virtualization-VirtualBox-blue?logo=virtualbox\&logoColor=white)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow)

Repository ini berisi dokumentasi **hands-on Linux CLI dan dasar administrasi sistem** sebagai fondasi untuk mempelajari Cybersecurity dan Penetration Testing.

Fokus utama repository adalah memahami bagaimana sistem operasi Linux bekerja melalui praktik langsung menggunakan **command line**, pengelolaan filesystem, user dan group, permission, process, service, SSH, log, hingga otomatisasi menggunakan Bash.

Repository ini merupakan bagian dari jalur pembelajaran Cybersecurity:

**Operating System → Networking → Python untuk Cybersecurity → Penetration Testing**

---

## 📑 Daftar Isi

* [🎯 Tujuan Repository](#-tujuan-repository)
* [🧭 Posisi dalam Roadmap Cybersecurity](#-posisi-dalam-roadmap-cybersecurity)
* [💻 Lingkungan Lab](#-lingkungan-lab)
* [🗺️ Roadmap Pembelajaran](#️-roadmap-pembelajaran)
* [📚 Metode Pembelajaran](#-metode-pembelajaran)
* [🧪 Struktur Setiap Lab](#-struktur-setiap-lab)
* [🔐 Security dan Privacy](#-security-dan-privacy)
* [⚠️ Disclaimer](#️-disclaimer)
* [🎯 Tujuan Akhir](#-tujuan-akhir)

---

# 🎯 Tujuan Repository

Repository ini dibuat untuk membangun pemahaman Linux dari tingkat dasar melalui praktik langsung.

Kemampuan yang dipelajari meliputi:

* Linux Command Line
* Bash
* Filesystem
* File dan Directory Management
* Users dan Groups
* File Permissions
* Ownership
* SUID
* Process Management
* Memory Management
* Systemd Services
* SSH
* Basic Linux Hardening
* System Logs
* Text Processing
* Bash Automation
* Troubleshooting

Selain memahami cara menggunakan command, setiap praktik juga dianalisis dari perspektif **system administration dan cybersecurity**.

---

# 🧭 Posisi dalam Roadmap Cybersecurity

Repository ini merupakan fondasi pertama sebelum masuk ke materi Cybersecurity yang lebih lanjut.

```text
Operating System
       │
       ▼
Networking
       │
       ▼
Python untuk Cybersecurity
       │
       ▼
Penetration Testing
       │
       ▼
Web / Network Security
```

Pemahaman Linux menjadi dasar penting karena banyak aktivitas Cybersecurity dilakukan menggunakan sistem berbasis Linux, terutama pada lingkungan server, security lab, CTF, dan penetration testing.

---

# 💻 Lingkungan Lab

Lingkungan utama yang digunakan dalam pembelajaran:

| Komponen        | Lingkungan          |
| --------------- | ------------------- |
| OS              | Ubuntu 22.04 LTS    |
| Shell           | Bash                |
| Virtualisasi    | VirtualBox / VMware |
| Sistem          | Virtual Machine     |
| Version Control | Git                 |
| Repository      | GitHub              |

Tools tambahan akan digunakan sesuai kebutuhan masing-masing lab.

---

# 🗺️ Roadmap Pembelajaran

| Fase | Materi                      | Fokus                                             |
| ---- | --------------------------- | ------------------------------------------------- |
| 01   | Konsep Dasar OS & CLI       | Memahami sistem operasi dan command line          |
| 02   | Filesystem & Navigasi       | Memahami struktur filesystem Linux                |
| 03   | Users, Groups & Permissions | Mengelola user, group, ownership, dan permission  |
| 04   | Process & Memory Management | Memahami process, resource, dan penggunaan memory |
| 05   | Systemd Services            | Memahami service dan proses background            |
| 06   | SSH & Basic Hardening       | Remote access dan keamanan dasar                  |
| 07   | System Log Analysis         | Membaca dan menganalisis log sistem               |
| 08   | Bash Automation             | Membuat otomatisasi menggunakan Bash              |

---

# 📚 Metode Pembelajaran

Setiap materi menggunakan pendekatan:

**Teori → Praktik → Observasi → Analisis → Troubleshooting → Dokumentasi**

Pembelajaran tidak hanya berfokus pada menjalankan command, tetapi juga memahami:

* Apa fungsi command
* Bagaimana command bekerja
* Output yang dihasilkan
* Mengapa output tersebut muncul
* Bagaimana melakukan troubleshooting
* Apa hubungan materi dengan Cybersecurity

---

# 🧪 Struktur Setiap Lab

Setiap lab akan didokumentasikan menggunakan struktur:

### 1. Tujuan

Menjelaskan kemampuan yang ingin dipelajari.

### 2. Teori

Menjelaskan konsep dasar sebelum melakukan praktik.

### 3. Lingkungan

Menjelaskan sistem dan tools yang digunakan.

### 4. Praktik

Melakukan hands-on exercise menggunakan Linux CLI.

### 5. Observasi

Mencatat output dan perubahan yang terjadi pada sistem.

### 6. Analisis

Menjelaskan hasil praktik dan alasan di baliknya.

### 7. Troubleshooting

Mendokumentasikan error atau masalah yang ditemukan serta cara penyelesaiannya.

### 8. Security Relevance

Menjelaskan hubungan materi dengan keamanan sistem.

### 9. Lessons Learned

Mencatat hal-hal penting yang diperoleh dari lab.

---

# 📂 Struktur Repository

Struktur repository dirancang berdasarkan tahapan pembelajaran:

```text
linux-cli-survival-lab/
│
├── 01-system-information/
│   └── README.md
│
├── 02-filesystem-permissions/
│   └── README.md
│
├── 03-users-and-permissions/
│   └── README.md
│
├── 04-process-memory/
│   └── README.md
│
├── 05-systemd-services/
│   └── README.md
│
├── 06-ssh-hardening/
│   └── README.md
│
├── 07-system-log-analysis/
│   └── README.md
│
├── 08-bash-automation/
│   └── README.md
│
└── README.md
```

Setiap direktori berisi dokumentasi praktik dan analisis dari masing-masing materi.

---

# 🔐 Security dan Privacy

Repository ini dipublikasikan sebagai portfolio sehingga seluruh dokumentasi harus diperiksa sebelum dipublikasikan.

Informasi sensitif seperti berikut tidak boleh dimasukkan ke repository:

* Password
* Credential
* API Key
* Access Token
* Private Key
* IP Address sensitif
* MAC Address
* Hostname pribadi
* Username pribadi
* Informasi jaringan internal
* Domain internal
* Informasi pribadi lainnya

Jika informasi tersebut muncul dalam output atau screenshot, informasi tersebut harus **dihapus, disamarkan, atau diganti dengan data lab** sebelum dipublikasikan.

Alur publikasi:

**Tulis → Review → Test → Privacy/Security Check → Sanitize → Commit → Push**

---

# ⚠️ Disclaimer

Repository ini dibuat untuk tujuan **pembelajaran Linux, system administration, dan pengembangan fundamental Cybersecurity**.

Seluruh praktik dilakukan pada lingkungan yang dimiliki sendiri atau lingkungan yang secara eksplisit memberikan izin untuk dilakukan pengujian.

Materi dalam repository ini tidak ditujukan untuk melakukan akses, pemindaian, eksploitasi, atau aktivitas lain terhadap sistem tanpa izin.

---

# 🎯 Tujuan Akhir

Setelah menyelesaikan seluruh lab, diharapkan mampu:

* Menggunakan Linux CLI dengan percaya diri
* Memahami struktur filesystem Linux
* Mengelola user dan group
* Memahami file permission dan ownership
* Memahami process dan resource management
* Mengelola system service
* Menggunakan SSH dengan aman
* Membaca dan menganalisis system logs
* Membuat automation sederhana menggunakan Bash
* Melakukan troubleshooting dasar Linux
* Memahami aspek keamanan dasar pada sistem Linux

Fundamental tersebut kemudian menjadi dasar untuk mempelajari:

**Networking → Python untuk Cybersecurity → Penetration Testing**





