
# ⚙️  Lab 04 — Linux Process & Memory Management

## 🎯 Objective

Memahami cara kerja, pemantauan, dan pengelolaan **process** pada sistem operasi Linux, serta memahami penggunaan **RAM** dan **Swap**.

Pada lab ini dipelajari dan dipraktikkan:

* Memeriksa **uptime** dan **load average** sistem.
* Memeriksa kapasitas dan penggunaan **RAM** serta **Swap**.
* Menampilkan daftar process yang sedang berjalan.
* Memantau process secara **real-time**.
* Menjalankan process sebagai **background job**.
* Mengelola foreground dan background job.
* Memahami **PID (Process ID)** dan **Job ID**.
* Mengirim signal untuk menghentikan process.
* Memahami perbedaan **SIGTERM**, **SIGKILL**, dan **SIGTSTP**.
* Menganalisis process dan resource usage dari perspektif administrasi sistem dan keamanan.

---

## 📚 Theory

### Apa itu Process?

**Process** adalah instance dari sebuah program yang sedang dijalankan oleh sistem operasi.

Ketika sebuah program dijalankan, Linux membuat process dan memberikan identitas berupa **PID (Process ID)**.

```text
Program → Process → PID
```

Informasi process dapat mencakup:

* PID
* Parent PID (PPID)
* User pemilik process
* Status process
* Penggunaan CPU
* Penggunaan memory
* Command yang menjalankan process

---

### PID — Process ID

**PID (Process ID)** adalah nomor identitas yang diberikan Linux kepada setiap process.

Contoh:

```text
PID 1
PID 1200
PID 45123
```

PID digunakan untuk mengidentifikasi process ketika melakukan monitoring atau mengirim signal.

---

### Parent dan Child Process

Sebuah process dapat membuat process lain.

Process yang membuat process lain disebut **parent process**, sedangkan process yang dibuat disebut **child process**.

```text
Parent Process
      │
      ├── Child Process
      ├── Child Process
      └── Child Process
```

---

### Process State

Beberapa status process yang umum:

| Status | Arti                  |
| ------ | --------------------- |
| `R`    | Running atau runnable |
| `S`    | Interruptible sleep   |
| `D`    | Uninterruptible sleep |
| `T`    | Stopped               |
| `Z`    | Zombie                |

Status process dapat dilihat melalui `ps`, `top`, atau `htop`.

---

### Foreground dan Background Process

**Foreground process** berjalan langsung menggunakan terminal dan biasanya membuat shell menunggu hingga process selesai.

Contoh:

```bash
sleep 30
```

Sedangkan **background process** berjalan tanpa memblokir terminal.

Contoh:

```bash
sleep 30 &
```

Tanda `&` digunakan untuk menjalankan command sebagai background job.

---

### Job ID dan PID

Ketika menjalankan background job, shell dapat memberikan **Job ID**, sedangkan Linux memberikan **PID**.

Contoh:

```text
[1] 45123
```

Keterangan:

```text
[1]   → Job ID
45123 → PID
```

Job ID digunakan oleh shell untuk mengelola job pada session tersebut, sedangkan PID merupakan identitas process pada sistem.

---

### Signal

Linux menggunakan **signal** untuk memberikan instruksi atau notifikasi kepada process.

Signal yang digunakan dalam lab:

| Signal    | Nomor | Fungsi                                       |
| --------- | ----: | -------------------------------------------- |
| `SIGTERM` |    15 | Meminta process berhenti secara normal       |
| `SIGKILL` |     9 | Menghentikan process secara paksa            |
| `SIGTSTP` |    20 | Menghentikan sementara process dari terminal |

Perintah:

```bash
kill <PID>
```

secara default mengirim:

```text
SIGTERM
```

Sedangkan:

```bash
kill -9 <PID>
```

mengirim:

```text
SIGKILL
```

`SIGKILL` menghentikan process secara paksa dan tidak dapat ditangani oleh process.

---

### RAM dan Swap

**RAM** merupakan memory utama yang digunakan untuk menjalankan program dan menyimpan data yang sedang aktif digunakan.

**Swap** merupakan ruang pada storage yang digunakan Linux sebagai tambahan virtual memory ketika diperlukan.

Swap bukan pengganti RAM secara langsung dan memiliki performa lebih rendah dibanding memory fisik.

---

### Load Average

**Load average** menunjukkan rata-rata jumlah task yang sedang runnable atau menunggu kondisi tertentu selama:

* 1 menit
* 5 menit
* 15 menit

Contoh:

```text
load average: 2.24, 1.77, 1.54
```

Load average bukan persentase penggunaan CPU. Interpretasinya perlu mempertimbangkan jumlah CPU/core dan workload sistem.

---

# 💻 Environment

* **OS:** Ubuntu 22.04 LTS
* **Shell:** Bash
* **System:** Linux Virtual Machine
* **Working Directory:** `~/linux-cli-survival-lab/04-Process-and-Memory`

---

# 🛠️ Commands Used

```text
uptime
free
ps
top
htop
jobs
fg
bg
kill
pgrep
```

---

# 🔬 Practice & Results

## 1. Memeriksa Uptime dan Load Average

### Command

```bash
uptime
```

### Result

Hasil praktik menunjukkan:

```text
up 16:11, 1 user, load average: 2.24, 1.77, 1.54
```

### Observation

Perintah `uptime` menampilkan:

* Waktu sistem
* Lama sistem menyala
* Jumlah user yang login
* Load average 1, 5, dan 15 menit

Load average yang diperoleh dari praktik:

```text
1 menit  → 2.24
5 menit  → 1.77
15 menit → 1.54
```

---

## 2. Memeriksa Penggunaan RAM dan Swap

### Command

```bash
free -h
```

### Result

Hasil praktik:

```text
Mem:
total       9.6Gi
used        5.7Gi
free        440Mi
buff/cache  3.5Gi
available   3.3Gi

Swap:
total       6.8Gi
used        321Mi
free        6.5Gi
```

### Observation

`free -h` menampilkan informasi penggunaan memory dalam format yang mudah dibaca.

Pada hasil praktik, memory `available` masih menunjukkan sekitar **3.3 GiB** yang tersedia untuk kebutuhan aplikasi.

Swap juga digunakan sebesar sekitar **321 MiB** dari total sekitar **6.8 GiB**.

---

## 3. Menampilkan Process dengan `ps`

### Command

```bash
ps aux | head -n 10
```

### Result

Hasil praktik menampilkan process sistem seperti:

```text
/sbin/init
[kthreadd]
[kworker/...]
```

### Observation

`ps aux` digunakan untuk melihat snapshot process yang sedang berjalan.

Kolom penting:

| Kolom     | Keterangan                           |
| --------- | ------------------------------------ |
| `USER`    | User pemilik process                 |
| `PID`     | Process ID                           |
| `%CPU`    | Penggunaan CPU                       |
| `%MEM`    | Penggunaan memory                    |
| `VSZ`     | Virtual memory size                  |
| `RSS`     | Resident Set Size                    |
| `STAT`    | Status process                       |
| `COMMAND` | Command atau program yang dijalankan |

Output asli tidak disertakan secara penuh untuk menjaga privasi environment lokal.

---

## 4. Monitoring Process Secara Real-Time

### Command

```bash
top
```

### Observation

`top` berhasil dijalankan dan menampilkan informasi process secara real-time.

Hasil praktik menunjukkan:

```text
Tasks: 289 total
3 running
286 sleeping
0 stopped
0 zombie
```

Informasi yang diamati:

* Total process
* Running process
* Sleeping process
* Stopped process
* Zombie process
* CPU usage
* Memory usage
* Swap usage
* Process berdasarkan PID

Pada saat pengujian, tidak terdapat zombie process:

```text
0 zombie
```

---

## 5. Monitoring Process dengan `htop`

### Command

```bash
htop
```

### Observation

`htop` berhasil dijalankan untuk memonitor process secara interaktif.

Informasi yang dapat diamati:

* CPU usage
* Memory usage
* Swap usage
* PID
* User
* Process state
* Command

---

## 6. Menjalankan Background Process

### Command

```bash
sleep 1000 &
```

### Result

Shell menghasilkan:

```text
[1] <PID>
```

Kemudian:

```bash
jobs
```

digunakan untuk melihat background job.

### Observation

Tanda `&` menjalankan command sebagai background job.

Shell memberikan:

```text
[1] → Job ID
<PID> → Process ID
```

---

## 7. Membawa Background Job ke Foreground

### Command

```bash
fg %1
```

Kemudian:

```text
Ctrl + Z
```

### Result

Job menjadi:

```text
[1]+  Stopped  sleep 1000
```

### Observation

`fg %1` membawa Job ID `1` ke foreground.

`Ctrl + Z` menyebabkan job dihentikan sementara menggunakan `SIGTSTP`.

---

## 8. Melanjutkan Job ke Background

### Command

```bash
bg %1
```

Kemudian:

```bash
jobs
```

### Result

Job kembali berjalan di background:

```text
[1]+  sleep 1000 &
```

### Observation

`bg %1` melanjutkan job yang sebelumnya dihentikan sementara agar kembali berjalan di background.

---

## 9. Mengidentifikasi Process dengan `pgrep`

### Command

```bash
pgrep -a sleep
```

### Result

Hasil praktik menunjukkan process `sleep` beserta PID-nya.

### Observation

`pgrep` dapat digunakan untuk mencari process berdasarkan nama.

Option `-a` menampilkan PID beserta command yang cocok.

---

## 10. Menghentikan Process dengan `SIGTERM`

### Command

```bash
kill <PID>
```

Kemudian:

```bash
jobs
```

### Result

Shell menunjukkan job telah berhenti:

```text
[1]+  Terminated  sleep 1000
```

### Observation

`kill <PID>` secara default mengirimkan **SIGTERM (15)**.

Signal tersebut meminta process untuk melakukan terminasi secara normal.

---

## 11. Menghentikan Process dengan `SIGKILL`

### Command

```bash
sleep 2000 &
```

Kemudian:

```bash
kill -9 %1
```

### Observation

Option `-9` mengirimkan **SIGKILL** untuk menghentikan process secara paksa.

Dalam praktik, command berhasil menghentikan background job.

`SIGKILL` sebaiknya digunakan ketika terminasi normal tidak berhasil atau memang diperlukan.

---

# ⚠️ Troubleshooting / Mistake Encountered

Salah satu konsep penting yang diuji adalah pembatasan akses terhadap process.

User biasa tidak selalu memiliki hak untuk menghentikan process milik user lain atau process sistem.

Contoh:

```bash
kill <PID>
```

dapat menghasilkan:

```text
Operation not permitted
```

Dalam kondisi tertentu, hak administratif dapat diperlukan:

```bash
sudo kill <PID>
```

Namun penggunaan `sudo kill` harus dilakukan dengan hati-hati karena process sistem yang penting dapat menyebabkan service terganggu atau sistem menjadi tidak stabil.

Untuk latihan, process milik sendiri seperti `sleep` lebih aman digunakan.

---

# 🔐 Analysis & Security Relevance

Pemahaman process dan memory memiliki hubungan langsung dengan:

* System Administration
* Security Monitoring
* Incident Response
* Threat Hunting
* Troubleshooting

### 1. Process Monitoring

Command seperti:

```bash
ps
top
htop
```

dapat digunakan untuk mengamati process yang tidak biasa.

Hal yang perlu diperhatikan antara lain:

* Nama process
* Command line
* User pemilik process
* Penggunaan CPU
* Penggunaan memory
* Process state
* Hubungan parent dan child process

Process yang tidak dikenal belum tentu merupakan malware sehingga diperlukan analisis lebih lanjut.

---

### 2. Resource Monitoring

Peningkatan penggunaan resource dapat menjadi indikator untuk melakukan investigasi.

Contohnya:

```text
CPU usage meningkat
Memory usage meningkat
Load average meningkat
```

Namun kondisi tersebut tidak otomatis menunjukkan serangan karena aplikasi normal juga dapat menggunakan resource yang besar.

---

### 3. Process Control dalam Incident Response

Kemampuan mengelola process dapat membantu dalam:

* Investigasi process mencurigakan
* Troubleshooting service
* Containment awal
* Pengelolaan resource
* Analisis aktivitas sistem

Pemilihan signal juga harus disesuaikan dengan kebutuhan:

```text
SIGTERM → terminasi normal
SIGKILL → terminasi paksa
```

---

### 4. Least Privilege

Kontrol terhadap process berkaitan dengan prinsip:

**Least Privilege**

User hanya diberikan hak akses yang diperlukan untuk menjalankan tugasnya.

---

# 📊 Concepts Summary

| Konsep       | Fungsi                                                      |
| ------------ | ----------------------------------------------------------- |
| Process      | Instance program yang sedang berjalan                       |
| PID          | Identitas process                                           |
| PPID         | Identitas parent process                                    |
| Job ID       | Identitas job pada shell                                    |
| Foreground   | Job berjalan langsung pada terminal                         |
| Background   | Job berjalan tanpa memblokir terminal                       |
| RAM          | Memory utama sistem                                         |
| Swap         | Virtual memory berbasis storage                             |
| Load Average | Rata-rata task yang runnable atau menunggu kondisi tertentu |
| SIGTERM      | Terminasi normal                                            |
| SIGKILL      | Terminasi paksa                                             |
| SIGTSTP      | Suspend process dari terminal                               |

---

# 📚 Lessons Learned

Dari lab ini dipelajari:

* Memeriksa uptime dan load average dengan `uptime`.
* Memeriksa RAM dan Swap dengan `free -h`.
* Menampilkan informasi process menggunakan `ps`.
* Melakukan monitoring process menggunakan `top` dan `htop`.
* Memahami perbedaan PID dan Job ID.
* Menjalankan background process menggunakan `&`.
* Mengelola job menggunakan `jobs`, `fg`, dan `bg`.
* Mengidentifikasi process menggunakan `pgrep`.
* Memahami `SIGTERM`, `SIGKILL`, dan `SIGTSTP`.
* Memahami hubungan process management dengan system administration dan cybersecurity.
* Memahami bahwa penggunaan resource tinggi tidak otomatis berarti adanya serangan.
* Memahami pentingnya prinsip Least Privilege dalam pengelolaan process.

---

# 🎯 Lab Status

**Status:** ✅ Completed

**Focus:** Process Management, Memory Management, Resource Monitoring, Job Control, dan Process Signals

**Next Topic:** Systemd Services
