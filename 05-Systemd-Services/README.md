
# ⚙️  Lab 05 — Systemd Services & Service Management

## 🎯 Objective

Memahami konsep **systemd**, **service**, dan **daemon** pada Linux serta cara mengelola layanan sistem menggunakan `systemctl`.

Pada lab ini dipelajari dan dipraktikkan:

* Memahami fungsi `systemd` sebagai init system dan service manager.
* Memahami konsep **service**, **daemon**, dan **unit file**.
* Memeriksa status service.
* Menjalankan dan menghentikan service.
* Melakukan restart service.
* Memeriksa apakah service aktif.
* Memeriksa konfigurasi service saat boot.
* Melihat unit file service.
* Membaca log service menggunakan `journalctl`.
* Menganalisis service dari perspektif administrasi sistem dan cybersecurity.

---

# 📚 Theory

## Apa itu systemd?

**systemd** adalah init system dan service manager yang digunakan oleh banyak distribusi Linux modern.

systemd digunakan untuk mengelola berbagai resource sistem, termasuk:

* Service
* Process
* Socket
* Mount
* Timer
* Target
* Unit lainnya

Administrator dapat berinteraksi dengan systemd menggunakan:

```bash
systemctl
```

---

## Apa itu Service?

**Service** adalah unit yang menyediakan fungsi tertentu pada sistem dan biasanya berjalan di background.

Contoh service pada Linux:

```text
cron
ssh
systemd-logind
```

Service dapat berjalan terus-menerus atau diaktifkan sesuai kebutuhan sistem.

---

## Apa itu Daemon?

**Daemon** adalah process yang berjalan di background untuk menyediakan fungsi tertentu pada sistem.

Contohnya:

```text
cron daemon
SSH daemon
logging daemon
```

Secara sederhana:

```text
systemd
   │
   └── mengelola service
             │
             └── menjalankan daemon/process
```

---

## Apa itu Unit File?

systemd menggunakan konsep **unit** untuk merepresentasikan resource yang dikelolanya.

Salah satu jenis unit yang umum adalah:

```text
.service
```

Contoh:

```text
cron.service
```

Unit file berisi informasi mengenai bagaimana suatu service didefinisikan dan dijalankan.

Informasi yang dapat terdapat di dalam unit file antara lain:

* Description
* Dependency
* Command yang dijalankan
* Environment
* Restart behavior
* Install configuration

Untuk melihat unit file:

```bash
systemctl cat cron
```

---

## `start` dan `stop`

Perintah:

```bash
sudo systemctl start <service>
```

digunakan untuk menjalankan service.

Sedangkan:

```bash
sudo systemctl stop <service>
```

digunakan untuk menghentikan service.

Keduanya berfokus pada **state service saat ini**.

---

## `restart`

Perintah:

```bash
sudo systemctl restart <service>
```

digunakan untuk menjalankan ulang service.

Restart sering digunakan ketika sebuah service perlu dimulai ulang, misalnya setelah perubahan konfigurasi tertentu.

---

## `enable` dan `disable`

Perintah:

```bash
sudo systemctl enable <service>
```

mengatur service agar diaktifkan secara otomatis saat boot sesuai konfigurasi systemd.

Sedangkan:

```bash
sudo systemctl disable <service>
```

membatalkan konfigurasi aktivasi otomatis tersebut.

Perbedaannya:

```text
start   → menjalankan service sekarang
stop    → menghentikan service sekarang

enable  → mengatur aktivasi otomatis saat boot
disable → membatalkan aktivasi otomatis saat boot
```

Jadi:

```text
enable ≠ start
disable ≠ stop
```

---

## `is-active`

Untuk memeriksa apakah service sedang aktif:

```bash
systemctl is-active <service>
```

Contoh:

```bash
systemctl is-active cron
```

Output:

```text
active
```

menunjukkan bahwa service sedang aktif.

---

## `is-enabled`

Untuk memeriksa apakah service dikonfigurasi untuk diaktifkan saat boot:

```bash
systemctl is-enabled <service>
```

Contoh:

```bash
systemctl is-enabled cron
```

Output:

```text
enabled
```

menunjukkan bahwa service dikonfigurasi untuk aktivasi otomatis saat boot.

---

## `journalctl`

`journalctl` digunakan untuk membaca log yang dikelola oleh **systemd journal**.

Untuk melihat log service tertentu:

```bash
sudo journalctl -u <service>
```

Contoh:

```bash
sudo journalctl -u cron
```

Untuk membatasi waktu:

```bash
sudo journalctl -u cron --since "1 hour ago"
```

Perintah ini berguna untuk:

* Troubleshooting
* Analisis error
* Monitoring service
* Investigasi aktivitas sistem

---

# 💻 Environment

* **OS:** Ubuntu 22.04.5 LTS
* **Shell:** Bash
* **System:** Linux
* **Working Directory:** `~/linux-cli-survival-lab/05-Systemd-Services`

---

# 🛠️ Commands Used

```text
systemctl status
systemctl start
systemctl stop
systemctl restart
systemctl enable
systemctl disable
systemctl is-active
systemctl is-enabled
systemctl cat
journalctl -u
```

---

# 🔬 Practice & Results

> **Catatan Praktikum:**
> Service `cron` digunakan sebagai objek eksperimen karena tersedia pada sistem dan sesuai untuk mempraktikkan dasar service management. Perubahan service dilakukan hanya pada lingkungan lab dan konfigurasi dikembalikan setelah pengujian.

---

## 1. Memeriksa Status Service

### Command

```bash
systemctl status cron
```

### Result

Hasil praktik menunjukkan:

```text
● cron.service - Regular background program processing daemon
     Loaded: loaded (/lib/systemd/system/cron.service; enabled)
     Active: active (running)
       Main PID: <PID>
     Tasks: 1
     Memory: <memory>
     CPU: <cpu-time>
     CGroup: /system.slice/cron.service
```

### Observation

Informasi penting:

* **Loaded** → menunjukkan status unit dan lokasi unit file.
* **Active** → menunjukkan state service saat ini.
* **Main PID** → PID utama service.
* **Tasks** → jumlah task yang terkait dengan service.
* **Memory** → penggunaan memory service.
* **CGroup** → control group yang digunakan systemd untuk mengelola service.

Pada praktik, `cron` berada pada state:

```text
active (running)
```

---

## 2. Memeriksa Status dengan `is-active`

### Command

```bash
systemctl is-active cron
```

### Result

```text
active
```

### Observation

Service `cron` sedang aktif dan berjalan.

---

## 3. Memeriksa Konfigurasi Boot dengan `is-enabled`

### Command

```bash
systemctl is-enabled cron
```

### Result

```text
enabled
```

### Observation

Service `cron` dikonfigurasi untuk diaktifkan secara otomatis saat boot.

---

## 4. Menghentikan Service

### Command

```bash
sudo systemctl stop cron
```

Kemudian:

```bash
systemctl is-active cron
```

### Result

```text
inactive
```

### Observation

`systemctl stop` berhasil menghentikan service `cron`.

Perubahan ini memengaruhi state service saat ini, bukan konfigurasi boot.

---

## 5. Menjalankan Service Kembali

### Command

```bash
sudo systemctl start cron
```

Kemudian:

```bash
systemctl is-active cron
```

### Result

```text
active
```

### Observation

`systemctl start` berhasil menjalankan kembali service `cron`.

---

## 6. Mengatur Service dengan `disable`

### Command

```bash
sudo systemctl disable cron
```

### Result

Systemd menghapus konfigurasi aktivasi service pada target boot:

```text
Removed /etc/systemd/system/multi-user.target.wants/cron.service.
```

Kemudian:

```bash
systemctl is-enabled cron
```

menghasilkan:

```text
disabled
```

### Observation

`disable` membatalkan aktivasi otomatis service saat boot.

---

## 7. Mengembalikan Service dengan `enable`

Setelah pengujian selesai, konfigurasi dikembalikan:

```bash
sudo systemctl enable cron
```

Kemudian:

```bash
systemctl is-enabled cron
```

### Result

```text
enabled
```

Systemd kembali membuat konfigurasi aktivasi pada target boot:

```text
Created symlink /etc/systemd/system/multi-user.target.wants/cron.service
```

### Observation

Service berhasil dikembalikan ke kondisi semula.

---

## 8. Melihat Unit File Service

### Command

```bash
systemctl cat cron
```

### Result

Bagian utama unit file yang diamati:

```text
[Unit]
Description=Regular background program processing daemon
Documentation=man:cron(8)
After=remote-fs.target nss-user-lookup.target

[Service]
EnvironmentFile=-/etc/default/cron
ExecStart=/usr/sbin/cron -f -P $EXTRA_OPTS
IgnoreSIGPIPE=false
KillMode=process
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

### Observation

Beberapa bagian penting:

#### `[Unit]`

Berisi metadata dan dependency service.

Contohnya:

```text
Description
Documentation
After
```

#### `[Service]`

Mendefinisikan bagaimana service dijalankan.

Contohnya:

```text
ExecStart
Restart
KillMode
```

#### `[Install]`

Berisi konfigurasi yang digunakan ketika service diaktifkan dengan `enable`.

Contohnya:

```text
WantedBy=multi-user.target
```

---

## 9. Membaca Log Service

### Command

```bash
sudo journalctl -u cron --since "1 hour ago"
```

### Result

Hasil praktik menunjukkan berbagai aktivitas service, termasuk:

```text
<timestamp> lab-host CRON[<PID>]: pam_unix(cron:session): session opened for user root
<timestamp> lab-host CRON[<PID>]: (root) CMD (...)
<timestamp> lab-host CRON[<PID>]: pam_unix(cron:session): session closed for user root
<timestamp> lab-host systemd[1]: Stopping Regular background program processing daemon...
<timestamp> lab-host systemd[1]: cron.service: Deactivated successfully.
<timestamp> lab-host systemd[1]: Stopped Regular background program processing daemon.
<timestamp> lab-host systemd[1]: Started Regular background program processing daemon.
```

### Observation

Log menunjukkan aktivitas normal `cron`, termasuk:

* Pembukaan session cron
* Eksekusi scheduled task
* Penutupan session
* Service dihentikan
* Service dijalankan kembali

Pada akhir pengujian, log menunjukkan bahwa `cron` berhasil dijalankan kembali.

Output telah disanitasi untuk menghapus hostname, username, dan PID asli dari environment lokal.

---

# ⚠️ Troubleshooting / Mistake Encountered

## 1. Membutuhkan Hak Administratif

Ketika mengubah state system service, user biasa umumnya membutuhkan hak administratif.

Contoh:

```bash
sudo systemctl stop cron
```

Penggunaan `sudo` memungkinkan command dijalankan dengan hak administratif yang diperlukan.

### Analysis

Perubahan terhadap system service dapat memengaruhi seluruh sistem sehingga akses tersebut dibatasi.

Gunakan hak administratif hanya ketika diperlukan.

---

## 2. Memahami Perbedaan `disable` dan `stop`

Salah satu konsep penting pada lab ini adalah membedakan:

```text
stop
```

dengan:

```text
disable
```

`stop` menghentikan service saat ini.

Sedangkan `disable` mengubah konfigurasi aktivasi otomatis saat boot.

Keduanya dapat digunakan secara terpisah.

---

# 🔐 Analysis & Security Relevance

Pemahaman systemd memiliki hubungan langsung dengan keamanan sistem Linux.

## 1. Service Enumeration

Service yang aktif dapat diperiksa menggunakan:

```bash
systemctl list-units --type=service --state=running
```

Informasi ini membantu administrator atau security analyst memahami:

* Service yang aktif
* Komponen yang sedang berjalan
* Potensi attack surface
* Service yang tidak diperlukan

Service yang tidak dikenal tidak otomatis berarti malicious dan perlu dianalisis lebih lanjut.

---

## 2. Attack Surface Reduction

Service yang tidak diperlukan dapat menambah attack surface.

Dalam hardening, administrator dapat mengevaluasi service yang aktif kemudian menonaktifkan service yang memang tidak dibutuhkan.

Secara umum:

```text
Service tidak diperlukan
        ↓
Evaluasi
        ↓
Stop
        ↓
Disable bila sesuai
        ↓
Attack surface berkurang
```

Penghapusan atau penonaktifan service harus mempertimbangkan dependency dan kebutuhan sistem.

---

## 3. Unit File Review

Unit file dapat diperiksa menggunakan:

```bash
systemctl cat <service>
```

Review dapat membantu melihat:

* Command yang dijalankan
* Dependency
* Environment
* Restart behavior
* User atau privilege yang digunakan jika didefinisikan

Hal ini berguna dalam troubleshooting dan security assessment.

---

## 4. Persistence Awareness

systemd juga merupakan salah satu area yang dapat diperiksa ketika menganalisis **persistence** pada Linux.

Analyst dapat melakukan review terhadap unit service yang tidak dikenal, termasuk unit yang berada di lokasi seperti:

```text
/etc/systemd/system/
```

Tujuannya adalah memahami apakah terdapat service yang tidak seharusnya aktif atau konfigurasi yang perlu diselidiki.

**Lab ini tidak melakukan pembuatan malicious service.**

---

## 5. Log Monitoring

`journalctl` dapat digunakan untuk membantu menganalisis:

* Service failure
* Service restart
* Perubahan state service
* Error
* Aktivitas service

Contoh:

```bash
sudo journalctl -u <service>
```

Log sebaiknya dikorelasikan dengan informasi lain karena satu event log tidak selalu cukup untuk menyimpulkan adanya insiden keamanan.

---

# 📊 Concepts Summary

| Konsep       | Fungsi                                   |
| ------------ | ---------------------------------------- |
| `systemd`    | Init system dan service manager          |
| `systemctl`  | Antarmuka command-line untuk systemd     |
| Service      | Unit yang menyediakan fungsi tertentu    |
| Daemon       | Process yang berjalan di background      |
| Unit         | Resource yang dikelola systemd           |
| `.service`   | Jenis unit untuk service                 |
| `start`      | Menjalankan service sekarang             |
| `stop`       | Menghentikan service sekarang            |
| `restart`    | Menjalankan ulang service                |
| `enable`     | Mengatur aktivasi otomatis saat boot     |
| `disable`    | Membatalkan aktivasi otomatis saat boot  |
| `is-active`  | Memeriksa state service saat ini         |
| `is-enabled` | Memeriksa konfigurasi aktivasi saat boot |
| `journalctl` | Membaca systemd journal                  |

---

# 📚 Lessons Learned

Dari lab ini dipelajari:

* Memahami fungsi `systemd` sebagai service manager.
* Memahami hubungan systemd, service, daemon, dan process.
* Memeriksa status service menggunakan `systemctl status`.
* Memeriksa state service menggunakan `systemctl is-active`.
* Memeriksa konfigurasi boot menggunakan `systemctl is-enabled`.
* Menggunakan `start`, `stop`, dan `restart`.
* Memahami perbedaan `start/stop` dengan `enable/disable`.
* Melihat unit file menggunakan `systemctl cat`.
* Membaca log service menggunakan `journalctl`.
* Memahami hubungan service management dengan hardening dan security monitoring.
* Memahami pentingnya review service untuk mengurangi attack surface.

---

# 🎯 Lab Status

**Status:** ✅ Completed

**Focus:** Systemd, Service Management, Daemon, Unit File, Boot Configuration, dan Journal Logging

**Next Topic:** Akses Jarak Jauh & Keamanan Dasar (SSH & Hardening)
