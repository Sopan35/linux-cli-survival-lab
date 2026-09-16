
# Modul 03 — Users, Groups, & Permissions

## 🎯 Objective

Memahami konsep manajemen pengguna (*users*), grup (*groups*), dan hak akses (*permissions*) pada sistem operasi Linux melalui **Command Line Interface (CLI)**.

Pada lab ini dilakukan praktik:

* Membaca file konfigurasi user dan group (`/etc/passwd` dan `/etc/shadow`).
* Mengelola pembuatan user, group, dan password menggunakan `useradd`, `groupadd`, dan `passwd`.
* Berpindah antar akun pengguna menggunakan `su`.
* Mengubah kepemilikan file/direktori menggunakan `chown`.
* Memanipulasi hak akses **Read, Write, Execute** menggunakan **Mode Oktal (4-2-1)**.
* Memanipulasi hak akses menggunakan **Mode Simbolik (`+x`, `-w`, `=`)**.
* Memahami batasan keamanan pada file sensitif sistem.
* Memahami hubungan permissions dan ownership dengan keamanan Linux.

---

## 💻 Environment

* **OS:** Ubuntu 22.04.5 LTS (Jammy Jellyfish)
* **Shell:** Bash
* **Working Directory:** `~/linux-cli-survival-lab/03-Users-and-Permissions`

---

## 🛠️ Commands Used

```bash
cat
sudo
useradd
groupadd
usermod
passwd
su
chown
chmod
ls -l
```

---

# 📖 Theoretical Fundamentals: Linux File Permissions

## 1. Struktur Hak Akses (`rwx`)

Saat menjalankan perintah `ls -l`, informasi tipe objek dan izin akses ditampilkan pada bagian awal output.

Contoh:

```text
-rwxr-xr--
```

Struktur sederhananya:

```text
-  rwx  r-x  r--
│   │    │    │
│   │    │    └── Others
│   │    └─────── Group Owner
│   └──────────── User Owner
└──────────────── Tipe objek
```

### Tipe Objek

Karakter pertama menunjukkan jenis objek filesystem:

| Karakter | Arti          |
| -------- | ------------- |
| `-`      | Regular file  |
| `d`      | Directory     |
| `l`      | Symbolic link |

### Permission

Tiga kelompok permission berikutnya masing-masing terdiri dari `r`, `w`, dan `x`:

```text
rwx
│││
││└── Execute
│└─── Write
└──── Read
```

* **`r` (Read):** Hak untuk membaca isi file atau melihat daftar isi direktori.
* **`w` (Write):** Hak untuk mengubah isi file. Pada direktori, write memungkinkan pembuatan, penghapusan, atau rename entry apabila permission lain yang diperlukan terpenuhi.
* **`x` (Execute):** Hak untuk mengeksekusi file sebagai program/script. Pada direktori, `x` memungkinkan pengguna melakukan traversal atau memasuki direktori apabila permission yang relevan tersedia.

---

# 2. Metode 1: Mode Oktal

Mode oktal menggunakan nilai numerik untuk merepresentasikan permission.

Nilai dasar:

| Permission      | Nilai |
| --------------- | ----: |
| `r` (Read)      |     4 |
| `w` (Write)     |     2 |
| `x` (Execute)   |     1 |
| `-` (No Access) |     0 |

## Penjumlahan Nilai

Contoh permission:

```text
User (Owner)       Group Owner       Others
[ r w x ]          [ r - - ]        [ - - - ]

4 + 2 + 1          4 + 0 + 0        0 + 0 + 0
    = 7                = 4              = 0
```

Maka permission tersebut dapat ditulis sebagai:

```bash
chmod 740 namafile
```

### Permission Oktal

| Nilai | Kombinasi | Deskripsi                 |
| ----: | --------- | ------------------------- |
| **7** | `rwx`     | Read + Write + Execute    |
| **6** | `rw-`     | Read + Write              |
| **5** | `r-x`     | Read + Execute            |
| **4** | `r--`     | Read only                 |
| **0** | `---`     | Tidak memiliki permission |

> Nilai permission untuk file atau direktori harus dipahami berdasarkan konteks objeknya. Misalnya, `x` pada direktori memiliki fungsi traversal yang berbeda dari `x` pada executable file.

---

# 3. Metode 2: Mode Simbolik

Mode simbolik digunakan untuk memodifikasi permission secara lebih spesifik tanpa harus menuliskan seluruh nilai oktal.

### Aktor

| Simbol | Arti       |
| ------ | ---------- |
| `u`    | User/Owner |
| `g`    | Group      |
| `o`    | Others     |
| `a`    | All        |

### Operator

| Operator | Fungsi                             |
| -------- | ---------------------------------- |
| `+`      | Menambahkan permission             |
| `-`      | Menghapus permission               |
| `=`      | Menetapkan permission secara tepat |

### Permission

* `r` → Read
* `w` → Write
* `x` → Execute

## Contoh

Menambahkan permission execute:

```bash
chmod +x script.sh
```

Menambahkan execute khusus untuk owner:

```bash
chmod u+x script.sh
```

Menghapus write permission dari group dan others:

```bash
chmod go-w file.txt
```

Menetapkan permission owner menjadi `rw`:

```bash
chmod u=rw file.txt
```

---

# 🔬 Practice & Results

## 1. Memeriksa File Konfigurasi User (`/etc/passwd`)

### Command

```bash
cat /etc/passwd | tail -n 5
```

### Output

```text
_flatpak:x:130:138:Flatpak system-wide installation helper,,,:/nonexistent:/usr/sbin/nologin
swtpm:x:131:142:virtual TPM software stack,,,:/var/lib/swtpm:/bin/false
libvirt-qemu:x:64055:109:Libvirt Qemu,,,:/var/lib/libvirt:/usr/sbin/nologin
libvirt-dnsmasq:x:132:144:Libvirt Dnsmasq,,,:/var/lib/libvirt/dnsmasq:/usr/sbin/nologin
mysql:x:999:1001::/home/mysql:/bin/sh
```

### Observation

File `/etc/passwd` menyimpan informasi akun pengguna dan akun sistem.

Format dasar setiap entry:

```text
username:password:UID:GID:GECOS:home:shell
```

Pada sistem modern, field password biasanya berisi `x`, yang menunjukkan bahwa password hash disimpan di `/etc/shadow`.

Contoh:

```text
mysql:x:999:1001::/home/mysql:/bin/sh
```

Informasi tersebut menunjukkan:

* **Username:** `mysql`
* **Password field:** `x`
* **UID:** `999`
* **GID:** `1001`
* **Home Directory:** `/home/mysql`
* **Login Shell:** `/bin/sh`

---

# 2. Membaca Password Hash (`/etc/shadow`)

### Command

```bash
sudo cat /etc/shadow | tail -n 5
```

### Output

```text
_flatpak:*:20582:0:99999:7:::
swtpm:*:20592:0:99999:7:::
libvirt-qemu:!:20592:0:99999:7:::
libvirt-dnsmasq:!:20592:0:99999:7:::
mysql:!:20645::::::
```

### Observation

File `/etc/shadow` menyimpan informasi autentikasi yang lebih sensitif, termasuk **password hash** dan data terkait kebijakan password.

File ini biasanya memiliki permission yang jauh lebih ketat dibandingkan `/etc/passwd`.

Pada sistem Ubuntu, akses terhadap `/etc/shadow` umumnya dibatasi kepada `root` dan group tertentu.

Karakter khusus pada field password dapat menunjukkan status tertentu. Contohnya:

```text
!
*
```

sering digunakan untuk menandakan bahwa password authentication untuk akun tersebut tidak dapat digunakan secara normal.

> Arti tepat field dan status akun tetap bergantung pada konfigurasi sistem dan mekanisme autentikasi yang digunakan.

Perintah `cat /etc/shadow` tanpa privilege yang sesuai biasanya menghasilkan:

```text
Permission denied
```

---

# 3. Membuat Group dan User Baru

### Command

```bash
sudo groupadd security_team
sudo useradd -m -g security_team -s /bin/bash auditor
sudo passwd auditor
```

### Output

```text
New password:
Retype new password:
passwd: password updated successfully
```

### Observation

* `groupadd` membuat group baru bernama `security_team`.
* `useradd` membuat akun `auditor`.
* Option `-m` membuat home directory untuk user.
* Option `-g` menentukan primary group.
* Option `-s` menentukan login shell.
* `passwd` digunakan untuk mengatur password user.

Home directory user yang dibuat:

```text
/home/auditor
```

Primary group:

```text
security_team
```

Login shell:

```text
/bin/bash
```

---

# 4. Mengubah Kepemilikan File dengan `chown`

File latihan dibuat terlebih dahulu:

```bash
touch audit_log.txt
ls -l audit_log.txt
```

### Output

```text
-rw-rw-r-- 1 user user 0 Sep 12 22:03 audit_log.txt
```

Kemudian ownership diubah:

```bash
sudo chown auditor:security_team audit_log.txt
ls -l audit_log.txt
```

### Output

```text
-rw-rw-r-- 1 auditor security_team 0 Sep 12 22:03 audit_log.txt
```

### Observation

`chown` merupakan singkatan dari **Change Owner**.

Command:

```bash
sudo chown auditor:security_team audit_log.txt
```

mengubah:

```text
Owner : user
Group : user
```

menjadi:

```text
Owner : auditor
Group : security_team
```

---

# 5. Memodifikasi Hak Akses dengan `chmod`

## Mode Oktal `740`

### Command

```bash
sudo chmod 740 audit_log.txt
ls -l audit_log.txt
```

### Output

```text
-rwxr----- 1 auditor security_team 0 Sep 12 22:03 audit_log.txt
```

### Observation

Permission `740` terdiri dari tiga bagian:

```text
7   4   0
│   │   │
│   │   └── Others
│   └────── Group
└────────── Owner
```

### Owner — `7`

```text
rwx = 4 + 2 + 1 = 7
```

User `auditor` memiliki:

* Read
* Write
* Execute

### Group — `4`

```text
r-- = 4 + 0 + 0 = 4
```

Group `security_team` memiliki:

* Read

### Others — `0`

```text
--- = 0
```

User lain tidak memiliki permission.

Jadi:

```text
740 = rwx r-- ---
```

---

# 6. Berpindah Pengguna (*Switch User*)

### Command

```bash
su - auditor
whoami
```

### Output

```text
auditor
```

### Observation

Perintah:

```bash
su - auditor
```

digunakan untuk berpindah ke akun `auditor` dan membuat **login shell environment** untuk user tersebut.

Command:

```bash
whoami
```

digunakan untuk memverifikasi identitas user aktif.

Output:

```text
auditor
```

menunjukkan bahwa shell saat ini berjalan dalam konteks user `auditor`.

---

# 🛠️ Troubleshooting / Mistake Encountered

## 1. Sudo Warning — Hostname Resolution

Saat mengeksekusi `sudo`, terminal mengembalikan peringatan:

```text
sudo: unable to resolve host dimas: Name or service not known
```

### Analysis

Hostname lokal `dimas` belum dipetakan dengan benar pada konfigurasi hostname resolution lokal.

### Solution

Konfigurasi `/etc/hosts` diperbaiki dengan menambahkan mapping:

```text
127.0.1.1 dimas
```

Setelah hostname dapat di-resolve secara lokal, warning tersebut tidak lagi muncul.

> Konfigurasi hostname dapat berbeda pada setiap sistem. Sebelum mengubah `/etc/hosts`, sebaiknya periksa hostname aktif dengan `hostnamectl` atau `hostname`.

---

## 2. Permission Denied saat Membuat User

Mengeksekusi:

```bash
useradd
```

atau membaca:

```bash
/etc/shadow
```

tanpa privilege yang sesuai dapat menghasilkan error:

```text
Permission denied
```

### Solution

Gunakan `sudo` ketika operasi membutuhkan privilege administrator:

```bash
sudo useradd ...
```

dan:

```bash
sudo cat /etc/shadow
```

### Lesson

Tidak semua operasi Linux dapat dilakukan oleh unprivileged user.

Memahami perbedaan antara:

* normal user
* privileged user
* root

merupakan bagian penting dari administrasi dan security Linux.

---

# 🔐 Analysis & Security Relevance

Pengelolaan **Users, Groups, & Permissions** merupakan fondasi dari **Identity & Access Control (IAM)** dalam keamanan sistem Linux.

## 1. Risiko `/etc/shadow`

Password hash pada `/etc/shadow` merupakan data sensitif.

Jika hash password berhasil diperoleh oleh pihak yang tidak berwenang, hash tersebut dapat menjadi target **offline password cracking**.

Dalam security assessment yang terotorisasi, password hash dapat dianalisis menggunakan tools seperti John the Ripper atau Hashcat sesuai ruang lingkup pengujian.

---

## 2. Bahaya Permission `777`

Permission:

```text
777 = rwx rwx rwx
```

memberikan read, write, dan execute kepada:

* Owner
* Group
* Others

Permission yang terlalu terbuka dapat meningkatkan risiko modifikasi atau eksekusi file oleh user yang tidak seharusnya memiliki akses.

Karena itu, permission harus mengikuti prinsip **Least Privilege**.

---

## 3. Principle of Least Privilege

**Least Privilege** berarti user atau proses hanya diberikan permission minimum yang dibutuhkan untuk menjalankan tugasnya.

Contohnya, private key SSH biasanya harus memiliki permission yang ketat, misalnya:

```bash
chmod 600 ~/.ssh/id_rsa
```

sehingga hanya owner yang memiliki akses read/write.

---

## 4. Hubungan dengan Linux Privilege Escalation

Pemahaman terhadap:

* ownership
* permissions
* users
* groups
* SUID
* SGID
* world-writable files

sangat penting ketika melakukan **Linux Privilege Escalation Assessment**.

Misconfiguration pada permission atau ownership dapat menyebabkan user memperoleh akses yang seharusnya tidak dimiliki.

Contoh area yang nantinya perlu dipelajari:

```text
SUID / SGID
        ↓
World-writable files
        ↓
Weak permissions
        ↓
Misconfigured sudo
        ↓
Service misconfiguration
        ↓
Privilege escalation
```

---

# 📚 Lessons Learned

Dari lab ini saya mempelajari:

* Struktur informasi akun pada `/etc/passwd`.
* Fungsi `/etc/shadow` dalam menyimpan password hash dan informasi autentikasi.
* Perbedaan user dan group pada Linux.
* Pembuatan user menggunakan `useradd`.
* Pembuatan group menggunakan `groupadd`.
* Pengaturan password menggunakan `passwd`.
* Perubahan ownership menggunakan `chown`.
* Perubahan permission menggunakan `chmod`.
* Perhitungan **Mode Oktal (4-2-1)**.
* Penggunaan **Mode Simbolik (`+`, `-`, `=`)**.
* Perbedaan permission `r`, `w`, dan `x`.
* Perbedaan akses Owner, Group, dan Others.
* Perpindahan user menggunakan `su -`.
* Pentingnya privilege dalam menjalankan operasi administrasi Linux.
* Troubleshooting hostname resolution menggunakan `/etc/hosts`.
* Hubungan filesystem permissions dengan Linux security.
* Dasar konsep **Least Privilege**.
* Hubungan permissions dan ownership dengan **Linux Privilege Escalation**.

---

# 🎯 Lab Status

**Status:** ✅ Completed

**Focus:** Users, Groups & File Permissions

**Security Focus:** Identity & Access Control, File Permissions & Linux Privilege Escalation Fundamentals

**Next Topic:** Process, Service & Memory Management
