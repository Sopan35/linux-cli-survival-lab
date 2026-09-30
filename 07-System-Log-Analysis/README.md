
# 📝 Lab 07 — System Log Analysis & Troubleshooting

## 🎯 Objective

Memahami fungsi log pada sistem Linux dan mempraktikkan cara membaca, mencari, memfilter, serta menganalisis log untuk kebutuhan **troubleshooting**, monitoring, dan dasar-dasar security investigation.

Pada lab ini dipelajari dan dipraktikkan:

* Mengenal struktur log pada `/var/log`.
* Membaca log authentication pada Ubuntu.
* Membaca system journal menggunakan `journalctl`.
* Memfilter log berdasarkan service, waktu, dan priority.
* Mencari aktivitas SSH dan penggunaan `sudo`.
* Memahami log rotation seperti `auth.log.1`.
* Menggunakan `grep` untuk mencari pola tertentu.
* Menggunakan `awk` untuk mengambil data dari log.
* Menggunakan `sort` dan `uniq` untuk menghitung pola.
* Membuat timeline sederhana dari event authentication.
* Melakukan troubleshooting berdasarkan informasi dari log.

---

# 📚 Teori

## Apa itu Log?

**Log** adalah catatan mengenai event atau aktivitas yang terjadi pada sistem, service, atau aplikasi.

Log dapat membantu menjawab pertanyaan seperti:

```text
Apa yang terjadi?
Kapan terjadi?
Service apa yang terlibat?
User apa yang terkait?
Apakah terjadi error?
```

Dalam system administration, log sangat berguna ketika terjadi masalah karena kita dapat melihat event yang terjadi sebelum atau sesudah masalah.

Dalam cybersecurity, log dapat menjadi salah satu sumber evidence untuk monitoring dan investigation.

---

## Mengapa Log Penting?

Misalnya sebuah service mengalami masalah.

Daripada hanya mencoba menjalankan ulang service, kita dapat memeriksa log terlebih dahulu:

```text
Masalah
   ↓
Periksa status
   ↓
Periksa log
   ↓
Cari event yang relevan
   ↓
Analisis
   ↓
Perbaiki
   ↓
Verifikasi
```

Dengan cara ini, troubleshooting menjadi lebih terarah.

---

## Direktori `/var/log`

Pada Ubuntu, berbagai file log dapat ditemukan di:

```text
/var/log
```

Pada praktik, ditemukan beberapa log seperti:

```text
auth.log
auth.log.1
syslog
kern.log
daemon.log
journal/
```

Pada server lab juga terdapat log dari aplikasi seperti:

```text
apache2/
mysql/
```

Tidak semua sistem Linux memiliki struktur log yang sama karena isi `/var/log` bergantung pada distribusi dan konfigurasi sistem.

---

## `auth.log`

Pada Ubuntu, `/var/log/auth.log` digunakan untuk mencatat berbagai aktivitas authentication dan authorization, termasuk aktivitas SSH dan `sudo`.

Contoh event:

```text
Accepted publickey
Accepted password
Failed password
sudo
session
```

Log ini berguna untuk melihat aktivitas yang berkaitan dengan akses sistem.

---

## `systemd-journald` dan `journalctl`

Pada sistem yang menggunakan systemd, event sistem juga dapat dikumpulkan oleh **systemd-journald**.

Log tersebut dapat dibaca menggunakan:

```bash
journalctl
```

`journalctl` dapat memfilter log berdasarkan:

* Service
* Waktu
* Priority
* Event tertentu

Contoh:

```bash
sudo journalctl -u ssh
```

---

## Log Priority

Systemd journal memiliki level priority:

```text
0  emerg
1  alert
2  crit
3  err
4  warning
5  notice
6  info
7  debug
```

Contoh:

```bash
sudo journalctl -p err
```

digunakan untuk menampilkan event dengan priority `err` dan level yang lebih tinggi.

---

## Log Rotation

Log yang terus bertambah dapat diputar menggunakan mekanisme **log rotation**.

Pada praktik ditemukan:

```text
auth.log
auth.log.1
```

Secara sederhana:

```text
auth.log
   ↓
log bertambah
   ↓
log rotation
   ↓
auth.log.1
   ↓
auth.log baru
```

Karena itu, event lama tidak selalu berada di file log aktif.

---

# 💻 Environment

* **OS:** Ubuntu 22.04.5 LTS
* **Shell:** Bash
* **System:** Linux Server
* **Working Directory:** `~/linux-cli-survival-lab/07-System-Log-Analysis`
* **Log Source:** `/var/log` dan systemd journal

---

# 🛠️ Commands Used

```text
ls
grep
tail
head
awk
sort
uniq
journalctl
```

---

# 🔬 Praktik dan Hasil

> **Catatan Praktikum:**
> Praktik dilakukan pada Ubuntu Server milik sendiri. Output yang digunakan untuk dokumentasi publik telah disanitasi untuk menghapus informasi environment yang tidak perlu, seperti IP address, hostname, dan fingerprint SSH.

---

## 1. Menjelajahi Struktur `/var/log`

### Command

```bash
ls -lah /var/log
```

### Hasil

Pada server ditemukan beberapa file dan directory log, termasuk:

```text
auth.log
auth.log.1
syslog
kern.log
daemon.log
journal/
apache2/
mysql/
```

### Observation

Hasil tersebut menunjukkan bahwa sistem menyimpan berbagai jenis log untuk kebutuhan yang berbeda.

Contohnya:

```text
auth.log → authentication / authorization
syslog   → aktivitas sistem umum
kern.log → event kernel
journal/ → systemd journal
```

---

## 2. Membaca Authentication Log

### Command

```bash
sudo tail -n 20 /var/log/auth.log
```

### Hasil

Pada log ditemukan event seperti:

```text
<timestamp> <lab-host> sshd[<PID>]: Accepted publickey for <LAB_USER> from <SOURCE_IP> port <PORT> ssh2
<timestamp> <lab-host> sudo: <LAB_USER> : ... COMMAND=...
```

Juga ditemukan event authentication failure pada aktivitas `sudo` karena kesalahan password saat praktik.

### Observation

`auth.log` dapat digunakan untuk melihat aktivitas yang berhubungan dengan authentication dan authorization.

---

# 🔎 Analisis Log SSH

## 3. Mencari Aktivitas SSH

### Command Awal

```bash
sudo grep "sshd" /var/log/auth.log | tail -n 20
```

### Observation

Command tersebut menemukan event SSH, tetapi juga dapat menemukan baris `sudo` yang kebetulan berisi teks `sshd`.

Contoh:

```text
sudo: ... COMMAND=/usr/bin/grep sshd /var/log/auth.log
```

### Analysis

Masalah ini terjadi karena `grep` hanya mencari **teks yang cocok**.

Ia tidak otomatis memahami apakah teks tersebut berasal dari event SSH atau hanya merupakan bagian dari command yang sedang dijalankan.

---

## 4. Membuat Filter yang Lebih Spesifik

Untuk menghindari false match, digunakan pola:

```bash
sudo grep -E 'sshd\[[0-9]+\]: Accepted publickey' /var/log/auth.log
```

### Hasil

Ditemukan event:

```text
<timestamp> <lab-host> sshd[<PID>]: Accepted publickey for <LAB_USER> from <SOURCE_IP> port <PORT> ssh2
```

### Observation

Sekarang hasil yang muncul benar-benar berasal dari proses `sshd`.

Hal ini menunjukkan bahwa filtering log perlu dibuat cukup spesifik agar data yang dianalisis lebih akurat.

---

## 5. Mencari `Accepted password`

### Command

```bash
sudo grep -E 'sshd\[[0-9]+\]: Accepted password' /var/log/auth.log
```

### Hasil

Tidak ditemukan event pada `auth.log` aktif.

### Analysis

Hal tersebut sesuai dengan kondisi setelah SSH hardening pada Lab 06, ketika password authentication sudah dinonaktifkan.

Namun hal ini tidak berarti server **tidak pernah** menggunakan password. Event login password sebelumnya dapat berada pada log hasil rotation.

---

# 📂 Analisis Log Lama

## 6. Membaca `auth.log.1`

### Command

```bash
sudo grep -aE 'sshd\[[0-9]+\]: Accepted password' /var/log/auth.log.1
```

### Hasil

Ditemukan beberapa event login SSH menggunakan password:

```text
Apr 26 <time> <lab-host> sshd[<PID>]: Accepted password for <LAB_USER> from <SOURCE_IP> port <PORT> ssh2
Sep 23 <time> <lab-host> sshd[<PID>]: Accepted password for <LAB_USER> from <SOURCE_IP> port <PORT> ssh2
Sep 27 <time> <lab-host> sshd[<PID>]: Accepted password for <LAB_USER> from <SOURCE_IP> port <PORT> ssh2
```

Pada data yang dianalisis terdapat **5 event `Accepted password`**.

### Observation

Hal ini menunjukkan bahwa sebelum hardening, server memang pernah menerima login SSH menggunakan password.

---

## 7. Mengapa Menggunakan `grep -a`?

Saat mencoba membaca:

```bash
sudo grep -E 'sshd\[[0-9]+\]: Accepted password' /var/log/auth.log.1
```

`grep` menampilkan:

```text
binary file matches
```

### Analysis

`auth.log.1` tidak diperlakukan oleh `grep` sebagai text biasa pada kondisi tersebut.

Untuk tetap melakukan pencarian sebagai text, digunakan:

```bash
sudo grep -aE 'sshd\[[0-9]+\]: Accepted password' /var/log/auth.log.1
```

Option:

```text
-a
```

meminta `grep` memperlakukan file sebagai text.

---

# 📊 Mengolah Data Log

## 8. Mengambil User dan IP dengan `awk`

### Command

```bash
sudo grep -aE 'sshd\[[0-9]+\]: Accepted password' /var/log/auth.log.1 | awk '{print "User:", $9, "| IP:", $11}'
```

### Hasil

Data berhasil diubah menjadi bentuk yang lebih sederhana:

```text
User: <LAB_USER> | IP: <SOURCE_IP>
User: <LAB_USER> | IP: <SOURCE_IP>
User: <LAB_USER> | IP: <SOURCE_IP>
User: <LAB_USER> | IP: <SOURCE_IP>
User: <LAB_USER> | IP: <SOURCE_IP>
```

### Observation

`awk` digunakan untuk mengambil field tertentu dari sebuah baris.

Pada format log yang dianalisis:

```text
$9  → username
$11 → source IP
```

---

## 9. Menghitung Login Berdasarkan IP

### Command

```bash
sudo grep -aE 'sshd\[[0-9]+\]: Accepted password' /var/log/auth.log.1 | awk '{print $11}' | sort | uniq -c | sort -nr
```

### Hasil

Pada data praktik:

```text
3 <SOURCE_IP>
1 <SOURCE_IP>
1 <SOURCE_IP>
```

### Observation

Artinya salah satu source IP muncul sebanyak 3 kali, sedangkan dua source IP lainnya masing-masing muncul 1 kali.

Hal ini menunjukkan **frekuensi event**, bukan otomatis menunjukkan aktivitas malicious.

---

## 10. Mengambil Timeline Login

### Command

```bash
sudo grep -aE 'sshd\[[0-9]+\]: Accepted password' /var/log/auth.log.1 | awk '{print $1, $2, $3, "| User:", $9, "| IP:", $11}'
```

### Hasil

Diperoleh data berupa:

```text
<DATE> <TIME> | User: <LAB_USER> | IP: <SOURCE_IP>
<DATE> <TIME> | User: <LAB_USER> | IP: <SOURCE_IP>
<DATE> <TIME> | User: <LAB_USER> | IP: <SOURCE_IP>
```

### Observation

Data sekarang dapat dibaca sebagai timeline.

Informasi yang dapat diamati:

```text
Kapan
↓
User siapa
↓
Dari IP mana
↓
Jenis authentication apa
```

---

# 🔐 Analisis Public-Key Authentication

## 11. Menganalisis Login SSH dengan Public Key

### Command

```bash
sudo grep -E 'sshd\[[0-9]+\]: Accepted publickey' /var/log/auth.log
```

Kemudian data diolah:

```bash
sudo grep -E 'sshd\[[0-9]+\]: Accepted publickey' /var/log/auth.log | awk '{print $1, $2, $3, "| User:", $9, "| IP:", $11}'
```

### Hasil

Ditemukan 2 event login menggunakan SSH public key:

```text
<DATE> <TIME> | User: <LAB_USER> | IP: <SOURCE_IP>
<DATE> <TIME> | User: <LAB_USER> | IP: <SOURCE_IP>
```

Perhitungan berdasarkan source IP:

```bash
sudo grep -E 'sshd\[[0-9]+\]: Accepted publickey' /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -nr
```

menghasilkan:

```text
1 <SOURCE_IP>
1 <SOURCE_IP>
```

### Observation

Hal ini menunjukkan bahwa public-key authentication yang dipraktikkan pada Lab 06 meninggalkan jejak pada authentication log.

---

# 🔗 Hubungan Lab 06 dan Lab 07

Praktik pada kedua lab saling berhubungan.

```text
Lab 06
SSH & Basic Hardening
        ↓
Login menggunakan password
        ↓
Membuat SSH key
        ↓
Login menggunakan public key
        ↓
Password authentication dinonaktifkan
        ↓
Lab 07
        ↓
Membaca authentication log
        ↓
Accepted password
Accepted publickey
        ↓
Menganalisis perubahan aktivitas authentication
```

Hal ini menunjukkan bahwa log dapat digunakan untuk melihat event yang terjadi setelah perubahan konfigurasi sistem.

---

# 🧪 Journal Analysis

## 12. Melihat Log Terbaru

### Command

```bash
sudo journalctl -n 20 --no-pager
```

### Observation

Beberapa event yang ditemukan berasal dari:

* systemd service
* `sudo`
* `apt`
* `snapd`
* service lainnya

Salah satu event yang ditemukan:

```text
404 Not Found
```

pada proses update package.

### Analysis

Event tersebut menunjukkan masalah saat proses mengambil package dari repository.

Event tersebut **tidak otomatis merupakan masalah keamanan**.

Ini menjadi contoh bahwa log error perlu dibaca berdasarkan konteks.

---

## 13. Memfilter Log Berdasarkan Service

### Command

```bash
sudo journalctl -u ssh -n 20 --no-pager
```

### Hasil

Ditemukan event seperti:

```text
Accepted publickey
session opened
Reloading OpenBSD Secure Shell server
Reloaded OpenBSD Secure Shell server
Server listening on ...
```

### Observation

Option:

```text
-u ssh
```

membatasi hasil hanya pada service SSH.

Hal ini membantu ketika troubleshooting hanya berfokus pada satu service.

---

## 14. Memfilter Berdasarkan Waktu

### Command

```bash
sudo journalctl -u ssh --since "1 hour ago" --no-pager
```

### Observation

Hasil hanya menampilkan event SSH dalam satu jam terakhir.

Filter waktu berguna ketika kita sudah mengetahui perkiraan waktu terjadinya masalah atau aktivitas tertentu.

---

## 15. Mencari Error

### Command

```bash
sudo journalctl -p err -n 20 --no-pager
```

### Hasil

Salah satu event yang ditemukan:

```text
<timestamp> kernel: [drm:vmwgfx] *ERROR* Failed to send host log message.
```

Selain itu terdapat beberapa event lama dari boot sebelumnya.

### Observation

`journalctl -p err` menampilkan event dengan priority `err` dan level yang lebih tinggi.

Hasil ini menunjukkan bahwa tidak semua error berasal dari masalah aplikasi atau keamanan.

Karena event `vmwgfx` berkaitan dengan komponen graphics/virtualization environment, event tersebut perlu dilihat sesuai konteks sistem dan tidak langsung dianggap sebagai security incident.

---

# ⚠️ Troubleshooting / Problem Solving

## 1. False Match pada `grep`

### Masalah

Saat menggunakan:

```bash
sudo grep "Failed password" /var/log/auth.log
```

hasil yang muncul justru memuat command `grep` itu sendiri.

Contoh:

```text
sudo: ... COMMAND=/usr/bin/grep 'Failed password' /var/log/auth.log
```

### Analisis

Command yang sedang dijalankan menggunakan `sudo` juga dicatat ke `auth.log`.

Karena command tersebut mengandung teks:

```text
Failed password
```

`grep` ikut menemukannya.

### Solusi

Filter dibuat lebih spesifik menggunakan pola `sshd`:

```bash
sudo grep -E 'sshd\[[0-9]+\]: Failed password' /var/log/auth.log
```

Hasilnya tidak menunjukkan event `Failed password` dari `sshd`.

### Lessons

**Jangan hanya mencari keyword. Perhatikan juga sumber event yang menghasilkan log tersebut.**

---

## 2. Log Lama Tersimpan pada `auth.log.1`

### Masalah

Pencarian `Accepted password` pada `auth.log` aktif tidak menghasilkan event.

### Analisis

Ditemukan file:

```text
auth.log.1
```

yang berisi log lebih lama.

### Solusi

Gunakan:

```bash
sudo grep -aE 'sshd\[[0-9]+\]: Accepted password' /var/log/auth.log.1
```

### Hasil

Ditemukan login password dari periode sebelumnya.

### Lessons

**Jangan hanya memeriksa log aktif ketika melakukan investigation. Log lama juga dapat berisi informasi penting.**

---

## 3. Kesalahan Password `sudo`

Saat membaca log, sempat terjadi kesalahan memasukkan password `sudo`.

Log kemudian mencatat:

```text
authentication failure
```

Setelah password yang benar dimasukkan, command dapat dijalankan.

### Lessons

Bahkan aktivitas troubleshooting dan kesalahan autentikasi lokal juga dapat meninggalkan jejak pada log.

---

# 🔐 Analysis & Security Relevance

## 1. Authentication Monitoring

`auth.log` dapat membantu memonitor event seperti:

```text
Accepted password
Accepted publickey
Failed password
sudo
session
```

Informasi tersebut dapat digunakan untuk memahami aktivitas authentication dan authorization.

---

## 2. Mendeteksi Aktivitas Mencurigakan

Pola tertentu dapat menjadi alasan untuk dilakukan investigation lebih lanjut.

Contohnya:

```text
Banyak login gagal
        ↓
Sumber yang sama
        ↓
Terjadi dalam waktu singkat
        ↓
Perlu dianalisis
```

Namun satu event atau jumlah login tertentu **tidak cukup untuk membuktikan adanya serangan**.

Analisis perlu mempertimbangkan:

* Waktu
* Source IP
* Username
* Frekuensi
* Event lain
* Kondisi normal sistem

---

## 3. Incident Response

Log dapat membantu menjawab:

```text
Apa yang terjadi?
Kapan terjadi?
User apa yang terlibat?
Service apa yang terkait?
Dari mana koneksi berasal?
Apa yang terjadi sebelum dan sesudah event?
```

Karena itu, log merupakan salah satu sumber evidence yang penting dalam incident response.

---

## 4. Log Tampering Awareness

Jika attacker mendapatkan hak akses tinggi, log dapat menjadi salah satu target untuk dihapus atau dimodifikasi.

Karena itu, pada lingkungan yang lebih besar, log biasanya perlu dikelola dan dikumpulkan secara terpusat agar investigation tidak hanya bergantung pada satu host.

---

## 5. SIEM

Dalam lingkungan enterprise, log dari berbagai sistem dapat dikumpulkan ke platform **SIEM (Security Information and Event Management)**.

Alur sederhananya:

```text
Collect
   ↓
Centralize
   ↓
Search
   ↓
Correlate
   ↓
Alert
   ↓
Investigate
```

---

# 📊 Ringkasan Konsep

| Konsep       | Fungsi                                           |
| ------------ | ------------------------------------------------ |
| Log          | Catatan event sistem atau aplikasi               |
| `/var/log`   | Lokasi umum file log Linux                       |
| `auth.log`   | Log authentication dan authorization pada Ubuntu |
| `auth.log.1` | Log hasil rotation sebelumnya                    |
| `journalctl` | Membaca system journal                           |
| `-u`         | Filter berdasarkan service/unit                  |
| `--since`    | Filter berdasarkan waktu                         |
| `-p err`     | Filter berdasarkan priority                      |
| `grep`       | Mencari pola text                                |
| `awk`        | Mengambil dan memproses field                    |
| `sort`       | Mengurutkan data                                 |
| `uniq -c`    | Menghitung kemunculan data                       |
| Log Rotation | Mekanisme pergantian dan penyimpanan log lama    |
| Timeline     | Urutan event berdasarkan waktu                   |
| SIEM         | Pengumpulan dan analisis log secara terpusat     |

---

# 📚 Lessons Learned

Dari lab ini dipelajari:

* Memahami fungsi log pada Linux.
* Mengenal berbagai sumber log di `/var/log`.
* Membaca `auth.log`.
* Memahami hubungan SSH dengan authentication log.
* Membedakan `Accepted password` dan `Accepted publickey`.
* Memahami log rotation melalui `auth.log.1`.
* Menggunakan `grep` untuk mencari event tertentu.
* Memahami pentingnya membuat filter yang spesifik untuk menghindari false match.
* Menggunakan `grep -a` untuk membaca log hasil rotation.
* Menggunakan `awk` untuk mengambil user, IP, dan waktu dari log.
* Menggunakan `sort` dan `uniq -c` untuk menghitung pola.
* Membuat timeline sederhana dari authentication event.
* Menggunakan `journalctl` untuk membaca system journal.
* Memfilter journal berdasarkan service, waktu, dan priority.
* Memahami bahwa error log tidak otomatis berarti security incident.
* Melakukan troubleshooting berdasarkan evidence dari log.
* Memahami hubungan log analysis dengan security monitoring dan incident response.

---

# 🎯 Lab Status

**Status:** ✅ Completed

**Focus:** System Log Analysis, Authentication Monitoring, Journal Analysis, Troubleshooting, dan Basic Security Monitoring

**Next Topic:** Bash Automation
