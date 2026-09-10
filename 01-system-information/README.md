
# Lab 01 — System Information

## Objective

Memahami cara mengidentifikasi informasi dasar sistem Linux melalui
command-line interface (CLI).

Pada lab ini, informasi sistem dikumpulkan secara langsung dari
Ubuntu menggunakan beberapa command Linux.

## Environment

- OS: Ubuntu 22.04.5 LTS (Jammy Jellyfish)
- Shell: Bash
- Hostname: ubuntu-lab
- Hardware Vendor: HP
- Hardware Model: HP Laptop (Redacted)
- Architecture: x86-64

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

### 1. `uname -a`

`uname` digunakan untuk menampilkan informasi mengenai sistem,
termasuk informasi kernel dan arsitektur.

Command:

```bash
uname -a
```

Output:

```text
Linux ubuntu-lab 6.8.0-124-generic #124~22.04.1-Ubuntu SMP PREEMPT_DYNAMIC Tue May 26 21:05:19 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux
```

Observation:

Sistem menjalankan kernel Linux `6.8.0-124-generic` dan menggunakan
arsitektur `x86_64`.

---

### 2. `cat /etc/os-release`

File `/etc/os-release` berisi informasi identifikasi mengenai sistem
operasi.

Command:

```bash
cat /etc/os-release
```

Output:

```text
PRETTY_NAME="Ubuntu 22.04.5 LTS"
NAME="Ubuntu"
VERSION_ID="22.04"
VERSION="22.04.5 LTS (Jammy Jellyfish)"
VERSION_CODENAME=jammy
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=jammy
```

Observation:

Sistem yang digunakan adalah Ubuntu `22.04.5 LTS` dengan codename
`jammy`.

Field `ID_LIKE=debian` menunjukkan bahwa Ubuntu mengidentifikasi
dirinya memiliki karakteristik yang serupa dengan Debian untuk tujuan
identifikasi distribusi.

---

### 3. `hostnamectl`

`hostnamectl` digunakan untuk melihat hostname dan informasi sistem
yang tersedia.

Command:

```bash
hostnamectl
```

Output:

```text
Static hostname: ubuntu-lab
      Icon name: computer-laptop
        Chassis: laptop
     Machine ID: 1730919...............
        Boot ID: a4aecbf...............
Operating System: Ubuntu 22.04.5 LTS
          Kernel: Linux 6.8.0-124-generic
    Architecture: x86-64
 Hardware Vendor: HP
  Hardware Model: HP Laptop (Redacted)
```

Observation:

Static hostname pada sistem adalah `ubuntu-lab`.

Informasi yang ditampilkan juga menunjukkan bahwa sistem menggunakan
Ubuntu 22.04.5 LTS, kernel Linux `6.8.0-124-generic`, arsitektur
`x86-64`, serta hardware dari HP dengan model `HP Laptop (Redacted)`.

---

### 4. `whoami`

`whoami` digunakan untuk mengetahui user yang sedang menjalankan
shell saat ini.

Command:

```bash
whoami
```

Output:

```text
user
```

Observation:

Shell saat ini dijalankan oleh user `user`.

---

### 5. `id`

Command `id` digunakan untuk menampilkan informasi identitas user,
termasuk UID, GID, dan group membership.

Command:

```bash
id
```

Output:

```text
uid=1000(user) gid=1000(user) groups=1000(user),27(sudo),999(docker)
```

Observation:

User `user` memiliki:

- UID: `1000`
- Primary GID: `1000`
- Group membership: `user`, `sudo`, `dan` `docker`.

Keanggotaan group menunjukkan bahwa user memiliki membership pada
berbagai group sistem yang memberikan akses terhadap fungsi atau
resource tertentu.

---

### 6. `pwd`

`pwd` digunakan untuk menampilkan current working directory atau
direktori kerja saat ini.

Command:

```bash
pwd
```

Output:

```text
/home/user/linux-cli-survival-lab/01-system-information
```

Observation:

Direktori kerja saat ini berada di dalam folder
`01-system-information` pada repository project
`linux-cli-survival-lab`.

---

## Analysis

Praktik ini menunjukkan bahwa informasi dasar sistem Linux dapat
dikumpulkan melalui command-line interface tanpa bergantung pada
antarmuka grafis (GUI).

Berdasarkan hasil praktik:

- Sistem menggunakan Ubuntu 22.04.5 LTS.
- Codename Ubuntu adalah `jammy`.
- Kernel yang digunakan adalah Linux `6.8.0-124-generic`.
- Arsitektur sistem adalah `x86-64`.
- Hostname sistem adalah `ubuntu-lab`.
- Hardware vendor adalah HP.
- Hardware model yang terdeteksi adalah `HP Laptop (Redacted)`.
- User yang sedang aktif adalah `user`.
- User memiliki UID `1000`.
- Primary GID user adalah `1000`.
- User memiliki membership pada beberapa system group.
- Current working directory berada di dalam project
  `linux-cli-survival-lab`.

Informasi tersebut dapat menjadi dasar untuk memahami kondisi sistem
sebelum melakukan administrasi, troubleshooting, maupun analisis
keamanan pada sistem Linux.

---

## Security Relevance

Informasi sistem dan user merupakan bagian penting dalam proses
enumeration dan system assessment.

Dalam lingkungan administrasi atau security assessment yang
terotorisasi, informasi seperti:

- sistem operasi,
- kernel,
- arsitektur,
- hostname,
- user aktif,
- UID/GID,
- dan group membership

dapat membantu memahami konfigurasi dan konteks sistem.

Pada tahap ini, lab hanya melakukan **local system information
enumeration** pada mesin Ubuntu milik sendiri.

---

## Lessons Learned

Dari lab ini saya mempelajari cara:

- Mengidentifikasi kernel Linux menggunakan `uname`.
- Mengidentifikasi sistem operasi menggunakan `/etc/os-release`.
- Melihat hostname dan informasi sistem menggunakan `hostnamectl`.
- Mengidentifikasi user aktif menggunakan `whoami`.
- Memeriksa UID, GID, dan group membership menggunakan `id`.
- Mengetahui current working directory menggunakan `pwd`.
- Mengumpulkan informasi dasar sistem Linux melalui CLI.

## Conclusion

Lab ini menjadi langkah awal untuk memahami sistem Linux melalui
command-line interface.

Informasi yang dikumpulkan pada lab berikutnya akan digunakan sebagai
dasar untuk mempelajari filesystem, user dan group, permission,
process, memory, networking, service, dan system logs.
