
# 🔐 Lab 06 — Remote Access & Basic Hardening (SSH)

## 🎯 Objective

Memahami cara melakukan **remote access** menggunakan SSH, menggunakan autentikasi berbasis kunci, dan menerapkan **basic hardening** pada SSH Server.

Pada lab ini dipelajari dan dipraktikkan:

* Memahami cara kerja SSH sebagai remote access.
* Membuat SSH key pair menggunakan `ed25519`.
* Memahami perbedaan **public key** dan **private key**.
* Menggunakan `authorized_keys` untuk autentikasi.
* Melakukan koneksi dari Ubuntu Desktop ke Ubuntu Server.
* Memahami konfigurasi `sshd_config`.
* Menonaktifkan root login melalui SSH.
* Menonaktifkan password authentication.
* Memeriksa konfigurasi SSH sebelum menerapkannya.
* Melakukan verifikasi setelah hardening.
* Membaca log koneksi SSH.
* Melakukan troubleshooting ketika konfigurasi tidak sesuai hasil yang diharapkan.

---

# 📚 Teori

## Apa itu SSH?

**SSH (Secure Shell)** adalah protokol yang digunakan untuk melakukan akses jarak jauh ke sistem lain melalui jaringan secara aman.

SSH umum digunakan untuk:

* Remote administration
* Mengelola server
* Menjalankan command pada server
* Mengelola sistem dari jarak jauh

Pada lab ini digunakan dua mesin virtual:

```text
Ubuntu Desktop
SSH Client
      │
      │ SSH
      ▼
Ubuntu Server
SSH Server
```

Dengan setup ini, praktik lebih mendekati kondisi nyata dibandingkan menggunakan `localhost`.

---

## SSH Client dan SSH Server

### SSH Client

SSH Client digunakan untuk melakukan koneksi ke server.

Contoh:

```bash
ssh user@<SERVER_IP>
```

### SSH Server

SSH Server menerima koneksi SSH dari client.

Pada Ubuntu, SSH Server menggunakan **OpenSSH Server**.

Service SSH dapat diperiksa menggunakan:

```bash
systemctl status ssh
```

---

## Public Key dan Private Key

SSH dapat menggunakan autentikasi berbasis pasangan kunci.

Pasangan tersebut terdiri dari:

```text
Public Key  → disimpan pada server
Private Key → disimpan dan dirahasiakan oleh client
```

Contoh:

```text
Ubuntu Desktop
├── Private Key
└── Public Key
         │
         ▼
Ubuntu Server
└── authorized_keys
```

Private key **tidak boleh** dibagikan, diunggah ke GitHub, atau dimasukkan ke screenshot.

---

## `authorized_keys`

File:

```text
~/.ssh/authorized_keys
```

digunakan untuk menyimpan public key yang diizinkan melakukan autentikasi pada akun tersebut.

Secara sederhana:

```text
Private Key
     │
     ▼
Autentikasi
     │
     ▼
Public Key pada authorized_keys
     │
     ▼
Login SSH
```

---

## ED25519

Pada lab ini digunakan:

```text
ed25519
```

sebagai jenis key SSH.

Key pair dibuat menggunakan:

```bash
ssh-keygen -t ed25519
```

---

## Passphrase

Private key dapat dilindungi menggunakan **passphrase**.

Tujuannya adalah memberikan lapisan perlindungan tambahan apabila file private key berhasil diakses oleh pihak lain.

Dalam praktik ini, private key tetap disimpan pada client dan tidak dimasukkan ke repository.

---

## SSH Hardening

**Hardening** adalah proses mengurangi risiko keamanan dengan memperbaiki konfigurasi dan mengurangi akses yang tidak diperlukan.

Pada lab ini digunakan:

```text
PermitRootLogin no
PasswordAuthentication no
```

### `PermitRootLogin no`

Digunakan untuk mencegah akun `root` melakukan login langsung melalui SSH.

Jika membutuhkan hak administrator, pendekatan yang digunakan adalah login menggunakan user biasa lalu menggunakan:

```bash
sudo
```

sesuai hak akses yang dimiliki.

### `PasswordAuthentication no`

Digunakan untuk menonaktifkan autentikasi berbasis password pada SSH.

Setelah konfigurasi diterapkan, server diharapkan menggunakan metode autentikasi yang telah dikonfigurasi, yaitu public-key authentication pada lab ini.

---

# 💻 Lingkungan Lab

| Komponen            | Lingkungan                 |
| ------------------- | -------------------------- |
| SSH Client          | Ubuntu Desktop 22.04.5 LTS |
| SSH Server          | Ubuntu Server 22.04.5 LTS  |
| SSH Server Software | OpenSSH Server             |
| Virtualisasi        | VirtualBox                 |
| Shell               | Bash                       |
| Target              | Server lab milik sendiri   |

Topologi:

```text
Ubuntu Desktop
     │
     │ SSH
     ▼
Ubuntu Server
```

---

# 🛠️ Perintah yang Digunakan

```text
ssh
ssh-keygen
ssh-copy-id
cat
ls
cp
nano
systemctl status
systemctl reload
sshd -t
sshd -T
journalctl
```

---

# 🔬 Praktik dan Hasil

> **Catatan:**
> Praktik dilakukan menggunakan Ubuntu Server sebagai target SSH dan Ubuntu Desktop sebagai client. IP address, hostname, username, dan informasi environment tertentu pada hasil dokumentasi telah disanitasi untuk kebutuhan publikasi.

---

## 1. Memastikan SSH Server Aktif

Sebelum melakukan konfigurasi, status SSH Server diperiksa:

```bash
systemctl status ssh --no-pager
```

### Hasil

Service menunjukkan:

```text
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (...; enabled)
     Active: active (running)
```

SSH Server juga terlihat melakukan listening pada:

```text
0.0.0.0 port 22
:: port 22
```

### Analisis

Hasil tersebut menunjukkan bahwa SSH Server sudah berjalan dan siap menerima koneksi.

---

## 2. Menguji Login SSH Menggunakan Password

Dari Ubuntu Desktop dilakukan koneksi ke Ubuntu Server:

```bash
ssh dimas@<SERVER_IP>
```

> `<SERVER_IP>` digunakan sebagai pengganti IP asli pada dokumentasi publik.

### Hasil

Login berhasil dan shell Ubuntu Server dapat diakses.

```text
Welcome to Ubuntu 22.04.5 LTS
```

### Analisis

Pada kondisi awal, server masih menerima autentikasi menggunakan password.

Ini menjadi **baseline** sebelum menerapkan SSH hardening.

---

## 3. Membuat SSH Key Pair

Pada Ubuntu Desktop dibuat pasangan key:

```bash
ssh-keygen -t ed25519 -C "lab-key"
```

### Hasil

Key pair berhasil dibuat:

```text
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```

### Analisis

Kedua file memiliki fungsi berbeda:

```text
id_ed25519
→ Private Key
→ harus dirahasiakan

id_ed25519.pub
→ Public Key
→ dapat dipasang pada server
```

Fingerprint SSH yang dihasilkan **tidak dimasukkan ke dokumentasi publik** karena tidak diperlukan untuk menjelaskan proses lab.

---

## 4. Memasang Public Key ke Server

Public key dikirim ke Ubuntu Server menggunakan:

```bash
ssh-copy-id dimas@<SERVER_IP>
```

### Hasil

Perintah memberikan hasil:

```text
Number of key(s) added: 1
```

### Analisis

Public key berhasil ditambahkan ke konfigurasi autentikasi user pada server.

---

## 5. Menguji Login Menggunakan SSH Key

Setelah public key dipasang, dilakukan koneksi kembali:

```bash
ssh dimas@<SERVER_IP>
```

### Hasil

Login berhasil **tanpa memasukkan password akun server**.

### Analisis

Hal ini menunjukkan bahwa public-key authentication sudah berhasil.

Alurnya:

```text
Ubuntu Desktop
     │
     │ Private Key
     ▼
SSH Authentication
     │
     ▼
Public Key pada Server
     │
     ▼
Login Berhasil
```

Ini merupakan langkah penting sebelum menonaktifkan password authentication.

---

## 6. Membuat Backup Konfigurasi SSH

Sebelum mengubah konfigurasi, dibuat backup:

```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup
```

### Analisis

Backup dibuat agar konfigurasi lama tetap tersedia jika terjadi kesalahan saat proses hardening.

---

## 7. Mengubah Konfigurasi SSH

Konfigurasi utama dibuka menggunakan:

```bash
sudo nano /etc/ssh/sshd_config
```

Kemudian konfigurasi yang digunakan untuk hardening adalah:

```text
PermitRootLogin no
PasswordAuthentication no
```

### Analisis

Konfigurasi tersebut bertujuan untuk:

```text
PermitRootLogin no
→ mencegah root login langsung melalui SSH

PasswordAuthentication no
→ menonaktifkan autentikasi berbasis password
```

---

## 8. Memeriksa Konfigurasi Sebelum Diterapkan

Sebelum melakukan reload, konfigurasi diperiksa:

```bash
sudo sshd -t
```

### Hasil

Command selesai tanpa menampilkan error.

### Analisis

Ini menunjukkan konfigurasi berhasil melewati pemeriksaan sintaks.

Langkah ini penting agar kesalahan konfigurasi tidak langsung diterapkan pada SSH Server.

Pola yang digunakan:

```text
Edit
  ↓
Test
  ↓
Tidak ada error
  ↓
Reload
```

---

## 9. Menerapkan Konfigurasi

Setelah konfigurasi lolos pemeriksaan:

```bash
sudo systemctl reload ssh
```

Kemudian status service diperiksa:

```bash
systemctl status ssh --no-pager
```

### Hasil

Service tetap berada pada kondisi:

```text
Active: active (running)
```

dan proses reload berhasil:

```text
ExecReload=/usr/sbin/sshd -t
status=0/SUCCESS
```

### Analisis

Konfigurasi baru berhasil dimuat tanpa membuat SSH Server berhenti.

---

# ⚠️ Troubleshooting / Problem Solving

## Masalah: Password Authentication Masih Bisa Digunakan

Setelah konfigurasi:

```text
PasswordAuthentication no
```

dilakukan pengujian dengan menonaktifkan public-key authentication dari sisi client:

```bash
ssh -o PubkeyAuthentication=no dimas@<SERVER_IP>
```

Awalnya server masih menerima login menggunakan password.

### Analisis

Daripada langsung mengubah konfigurasi secara acak, dilakukan pemeriksaan terhadap konfigurasi SSH tambahan.

Directory:

```bash
ls -l /etc/ssh/sshd_config.d/
```

menunjukkan adanya file:

```text
50-cloud-init.conf
```

Kemudian isi file diperiksa:

```bash
sudo cat /etc/ssh/sshd_config.d/50-cloud-init.conf
```

Ditemukan konfigurasi:

```text
PasswordAuthentication yes
```

### Solusi

Konfigurasi tambahan tersebut diperiksa dan disesuaikan agar password authentication benar-benar dinonaktifkan.

Setelah itu dilakukan verifikasi menggunakan:

```bash
sudo sshd -T | grep -Ei 'permitrootlogin|passwordauthentication|kbdinteractiveauthentication|pubkeyauthentication'
```

### Hasil Verifikasi

```text
permitrootlogin no
pubkeyauthentication yes
passwordauthentication no
kbdinteractiveauthentication no
```

Kemudian dilakukan reload:

```bash
sudo systemctl reload ssh
```

Pengujian password-only dilakukan kembali:

```bash
ssh -o PubkeyAuthentication=no dimas@<SERVER_IP>
```

### Hasil Akhir

```text
Permission denied (publickey).
```

### Kesimpulan Troubleshooting

Masalah tidak diselesaikan dengan sekadar mengubah satu baris konfigurasi.

Proses yang dilakukan:

```text
Konfigurasi tidak sesuai hasil yang diharapkan
                ↓
Periksa konfigurasi tambahan
                ↓
Temukan 50-cloud-init.conf
                ↓
Temukan PasswordAuthentication yes
                ↓
Perbaiki konfigurasi
                ↓
sshd -T
                ↓
Verifikasi hasil
                ↓
Reload SSH
                ↓
Tes kembali
                ↓
Password-only login ditolak
```

Ini menjadi salah satu bagian penting dari lab karena menunjukkan proses **analisis → mencari penyebab → memperbaiki → verifikasi**.

---

# 🔐 Analysis & Security Relevance

## 1. Key-Based Authentication

Public-key authentication memungkinkan server melakukan autentikasi menggunakan pasangan key.

Konsep dasarnya:

```text
Public Key  → Server
Private Key → Client
```

Private key harus tetap dirahasiakan.

---

## 2. Root Login

Login langsung menggunakan `root` melalui SSH meningkatkan risiko terhadap akun dengan hak akses tertinggi.

Dalam lab digunakan:

```text
PermitRootLogin no
```

sebagai bagian dari basic hardening.

---

## 3. Password Authentication

Password authentication dinonaktifkan:

```text
PasswordAuthentication no
```

Setelah konfigurasi diverifikasi, pengujian:

```bash
ssh -o PubkeyAuthentication=no dimas@<SERVER_IP>
```

berhasil ditolak:

```text
Permission denied (publickey).
```

Hal ini menunjukkan bahwa login tidak lagi dapat dilakukan menggunakan metode password pada konfigurasi lab ini.

---

## 4. Private Key Protection

Private key merupakan informasi rahasia.

Jangan:

```text
❌ Upload private key ke GitHub
❌ Masukkan private key ke README
❌ Kirim private key melalui chat
❌ Masukkan private key ke screenshot
```

Pada repository ini hanya konsep dan public key yang dibahas. Isi private key tidak didokumentasikan.

---

## 5. Configuration Validation

Salah satu pelajaran penting dari lab ini adalah membiasakan diri melakukan validasi sebelum menerapkan perubahan.

Pola yang digunakan:

```text
Backup
  ↓
Edit
  ↓
sshd -t
  ↓
sshd -T
  ↓
Reload
  ↓
Test Again
```

Pendekatan ini membantu mengurangi risiko salah konfigurasi dan kehilangan akses SSH.

---

## 6. Log Monitoring

Log SSH dapat digunakan untuk melihat aktivitas autentikasi.

Contoh:

```bash
sudo journalctl -u ssh --since "30 minutes ago"
```

Pada praktik, log menunjukkan perbedaan antara:

```text
Accepted password
```

pada kondisi awal dan:

```text
Accepted publickey
```

setelah public-key authentication berhasil.

Log juga menunjukkan proses reload service SSH.

---

# 📊 Ringkasan Konsep

| Konsep                   | Fungsi                                  |
| ------------------------ | --------------------------------------- |
| SSH                      | Remote access secara aman               |
| SSH Client               | Melakukan koneksi ke server             |
| SSH Server               | Menerima koneksi SSH                    |
| `sshd`                   | Daemon/server OpenSSH                   |
| Public Key               | Kunci yang dipasang pada server         |
| Private Key              | Kunci rahasia pada client               |
| `authorized_keys`        | Daftar public key yang diizinkan        |
| `ed25519`                | Jenis SSH key                           |
| Passphrase               | Perlindungan tambahan untuk private key |
| `sshd_config`            | Konfigurasi SSH Server                  |
| `PermitRootLogin`        | Mengatur login root melalui SSH         |
| `PasswordAuthentication` | Mengatur autentikasi password           |
| `sshd -t`                | Memeriksa sintaks konfigurasi           |
| `sshd -T`                | Melihat konfigurasi efektif             |
| `systemctl reload`       | Memuat ulang konfigurasi service        |
| `journalctl`             | Membaca log service                     |

---

# 📚 Lessons Learned

Dari lab ini dipelajari:

* Memahami fungsi SSH untuk remote access.
* Memahami hubungan antara SSH Client dan SSH Server.
* Membuat SSH key pair menggunakan `ed25519`.
* Memahami public key dan private key.
* Menggunakan `ssh-copy-id` untuk memasang public key.
* Menguji login menggunakan SSH key.
* Membuat backup sebelum mengubah konfigurasi SSH.
* Menerapkan `PermitRootLogin no`.
* Menerapkan `PasswordAuthentication no`.
* Menemukan konfigurasi tambahan pada `sshd_config.d`.
* Memahami pentingnya memeriksa konfigurasi efektif menggunakan `sshd -T`.
* Memvalidasi konfigurasi menggunakan `sshd -t`.
* Melakukan reload SSH setelah konfigurasi diperbaiki.
* Melakukan pengujian ulang setelah hardening.
* Menggunakan log SSH untuk melihat aktivitas autentikasi.
* Memahami pentingnya problem solving dan verifikasi dalam administrasi sistem Linux.

---

# 🎯 Lab Status

**Status:** ✅ Completed

**Focus:** SSH, Public-Key Authentication, Remote Access, Configuration Validation, Basic Hardening, dan Troubleshooting

**Next Topic:** System Log Analysis
