
# Lab 02 — Linux Filesystem & File Operations

## Objective

Memahami struktur filesystem Linux dan melakukan operasi dasar terhadap file dan direktori melalui command-line interface (CLI).

Pada lab ini dilakukan praktik membuat direktori, membuat file, berpindah direktori, menyalin file, memindahkan file, menghapus file, mengidentifikasi hidden file, mencari file dan direktori, memeriksa metadata filesystem, membuat symbolic link, serta mengamati penggunaan disk.

---

## Environment

* **OS:** Ubuntu 22.04.5 LTS (Jammy Jellyfish)
* **Shell:** Bash
* **Working Directory:** `~/linux-cli-survival-lab/02-filesystem`

---

## Commands Used

```bash
ls
ls -la
pwd
cd
mkdir
touch
cat
cp
mv
rm -i
find
du
file
stat
ls -i
ln -s
history
```

---

## Lab Structure

Direktori latihan dibuat dengan struktur berikut:

```text
lab-data/
├── backup/
├── documents/
└── logs/
```

Struktur ini digunakan sebagai lingkungan latihan filesystem lokal.

---

# Practice & Results

## 1. Membuat Direktori Lab

Perintah:

```bash
mkdir lab-data
mkdir lab-data/documents
mkdir lab-data/logs
mkdir lab-data/backup
```

Kemudian struktur diperiksa menggunakan:

```bash
ls
```

Output:

```text
lab-data  README.md
```

### Observation

Direktori `lab-data` berhasil dibuat beserta tiga subdirektori:

* `documents`
* `logs`
* `backup`

Struktur tersebut digunakan sebagai filesystem latihan.

---

## 2. Memeriksa Detail Direktori

Perintah:

```bash
ls -la lab-data/
```

Output:

```text
total 20
drwxrwxr-x 5 user user 4096 Agu 27 13:25 .
drwxrwxr-x 3 user user 4096 Agu 27 13:24 ..
drwxrwxr-x 2 user user 4096 Agu 27 13:25 backup
drwxrwxr-x 2 user user 4096 Agu 27 13:24 documents
drwxrwxr-x 2 user user 4096 Agu 27 13:25 logs
```

### Observation

Option `-a` digunakan untuk menampilkan seluruh entry termasuk:

```text
.
..
```

Option `-l` menampilkan informasi detail seperti:

* permission
* owner
* group
* ukuran
* timestamp
* nama file atau direktori

---

## 3. Verifikasi Struktur Direktori dengan `find`

Perintah:

```bash
find lab-data -maxdepth 2 -type d
```

Output:

```text
lab-data
lab-data/logs
lab-data/backup
lab-data/documents
```

### Observation

Command `find` dapat digunakan untuk mencari objek filesystem berdasarkan kriteria tertentu.

Option:

```text
-type d
```

digunakan untuk membatasi hasil hanya pada direktori.

---

## 4. Navigasi Filesystem dengan `cd` dan `pwd`

Perintah:

```bash
cd ~/linux-cli-survival-lab/02-filesystem/lab-data/documents
pwd
```

Output:

```text
/home/user/linux-cli-survival-lab/02-filesystem/lab-data/documents
```

Kemudian:

```bash
cd ..
pwd
```

Output:

```text
/home/user/linux-cli-survival-lab/02-filesystem/lab-data
```

### Observation

`cd ..` digunakan untuk berpindah ke parent directory.

Praktik ini menunjukkan penggunaan:

* absolute path
* relative path
* current directory
* parent directory

---

## 5. Membuat File dengan `touch`

Perintah:

```bash
touch notes.txt
touch commands.txt
touch todo.txt
```

Kemudian:

```bash
ls -l
```

Output:

```text
-rw-rw-r-- 1 user user 0 Agu 27 13:28 commands.txt
-rw-rw-r-- 1 user user 0 Agu 27 13:28 notes.txt
-rw-rw-r-- 1 user user 0 Agu 27 13:28 todo.txt
```

### Observation

`touch` digunakan untuk membuat file kosong apabila file belum ada.

Ukuran file yang baru dibuat adalah `0` byte.

---

## 6. Menulis Data ke File

Perintah:

```bash
echo "Linux CLI practice" > notes.txt
echo "ls cd pwd cp mv rm" > commands.txt
echo "Study filesystem" > todo.txt
```

Kemudian isi file diperiksa menggunakan:

```bash
cat notes.txt
cat commands.txt
cat todo.txt
```

Output:

```text
Linux CLI practice
ls cd pwd cp mv rm
Study filesystem
```

### Observation

Operator:

```text
>
```

digunakan untuk mengarahkan output command ke file.

Jika file sudah memiliki isi, operator `>` akan mengganti isi sebelumnya.

---

## 7. Menyalin File dengan `cp`

Perintah:

```bash
cp notes.txt ../backup/
```

Kemudian:

```bash
ls -l ../backup/
```

Output:

```text
-rw-rw-r-- 1 user user 19 Agu 27 13:30 notes.txt
```

Isi file diperiksa menggunakan:

```bash
cat ../backup/notes.txt
```

Output:

```text
Linux CLI practice
```

### Observation

`cp` membuat salinan file tanpa menghapus file sumber.

Setelah proses copy:

```text
documents/notes.txt
backup/notes.txt
```

keduanya tetap tersedia sebagai file terpisah.

---

## 8. Rename File dengan `mv`

Perintah:

```bash
mv todo.txt tasks.txt
```

Kemudian:

```bash
ls -l
```

Output menunjukkan:

```text
commands.txt
notes.txt
tasks.txt
```

### Observation

`mv` dapat digunakan untuk memindahkan file sekaligus melakukan rename.

Dalam praktik ini:

```text
todo.txt → tasks.txt
```

---

## 9. Memindahkan File ke Direktori Lain

Perintah:

```bash
mv commands.txt ../logs/
```

Kemudian:

```bash
ls -l ../logs/
```

Output:

```text
-rw-rw-r-- 1 user user 19 Agu 27 13:29 commands.txt
```

### Observation

File `commands.txt` berhasil dipindahkan dari:

```text
documents/
```

ke:

```text
logs/
```

File tersebut tidak lagi berada di direktori `documents`.

---

## 10. Menghapus File dengan Konfirmasi

File sementara dibuat menggunakan:

```bash
touch temporary.txt
```

Kemudian:

```bash
rm -i temporary.txt
```

Output:

```text
rm: remove regular empty file 'temporary.txt'? y
```

### Observation

Option `-i` meminta konfirmasi sebelum file dihapus.

Pendekatan ini lebih aman untuk latihan karena memberikan kesempatan untuk memeriksa kembali target sebelum penghapusan.

---

## 11. Hidden File

Membuat hidden file:

```bash
touch .hidden-file
```

Ketika menggunakan:

```bash
ls
```

file tidak terlihat.

Kemudian:

```bash
ls -la
```

Output menunjukkan:

```text
.hidden-file
notes.txt
tasks.txt
```

### Observation

Pada Linux, nama file yang diawali karakter `.` secara konvensi diperlakukan sebagai hidden file.

Hidden file tetap dapat diakses menggunakan nama atau path-nya.

---

## 12. Mencari File dengan `find`

Perintah:

```bash
find lab-data -type f
```

Output:

```text
lab-data/logs/commands.txt
lab-data/backup/notes.txt
lab-data/documents/notes.txt
lab-data/documents/.hidden-file
lab-data/documents/tasks.txt
```

### Observation

Option:

```text
-type f
```

digunakan untuk mencari file biasa.

Hasil menunjukkan seluruh file yang terdapat di dalam `lab-data`.

---

## 13. Mencari Direktori dengan `find`

Perintah:

```bash
find lab-data -type d
```

Output:

```text
lab-data
lab-data/logs
lab-data/backup
lab-data/documents
```

### Observation

Option:

```text
-type d
```

digunakan untuk mencari direktori.

---

## 14. Melihat Penggunaan Disk dengan `du`

Perintah:

```bash
du -h lab-data
```

Output:

```text
8,0K    lab-data/logs
8,0K    lab-data/backup
12K     lab-data/documents
32K     lab-data
```

Kemudian:

```bash
du -sh lab-data
```

Output:

```text
32K     lab-data
```

### Observation

`du` digunakan untuk melihat penggunaan ruang disk oleh file dan direktori.

Option:

```text
-h
```

menampilkan ukuran dalam format yang lebih mudah dibaca.

Option:

```text
-s
```

menampilkan total penggunaan untuk target yang diberikan.

Perbedaan ukuran direktori dengan ukuran isi file yang terlihat dapat terjadi karena filesystem menggunakan metadata dan block allocation.

---

## 15. Memeriksa Struktur Akhir Lab

Perintah:

```bash
find lab-data -maxdepth 2 -print
```

Output:

```text
lab-data
lab-data/logs
lab-data/logs/commands.txt
lab-data/backup
lab-data/backup/notes.txt
lab-data/documents
lab-data/documents/notes.txt
lab-data/documents/.hidden-file
lab-data/documents/tasks.txt
```

Struktur akhir:

```text
lab-data/
├── backup/
│   └── notes.txt
├── documents/
│   ├── .hidden-file
│   ├── notes.txt
│   └── tasks.txt
└── logs/
    └── commands.txt
```

---

## 16. Filesystem Metadata dengan `file`, `stat`, dan `ls -i`

Selain operasi file dasar, dilakukan pemeriksaan metadata terhadap file `notes.txt`.

### 16.1 Identifikasi Tipe File dengan `file`

Command:

```bash
file lab-data/documents/notes.txt
```

Output:

```text
lab-data/documents/notes.txt: ASCII text
```

### Observation

Command `file` digunakan untuk mengidentifikasi tipe data suatu file berdasarkan isi dan karakteristik file tersebut.

Pada praktik ini, `notes.txt` teridentifikasi sebagai:

```text
ASCII text
```

---

### 16.2 Melihat Metadata File dengan `stat`

Command:

```bash
stat lab-data/documents/notes.txt
```

Output:

```text
File: lab-data/documents/notes.txt
Size: 19
Blocks: 8
IO Block: 4096
regular file
Device: 10306h/66310d
Inode: 1573649
Links: 1
Access: (0664/-rw-rw-r--)
Uid: (1000/user)
Gid: (1000/user)
```

Output juga menampilkan informasi waktu seperti:

* Access
* Modify
* Change
* Birth

### Observation

`stat` memberikan informasi metadata filesystem yang lebih lengkap dibandingkan `ls -l`.

Beberapa informasi penting:

* `Size` — ukuran file dalam byte.
* `Inode` — nomor inode yang digunakan filesystem untuk mengidentifikasi file.
* `Links` — jumlah hard link.
* `Uid` — user owner.
* `Gid` — group owner.
* `Access` — permission file.
* Timestamp — waktu akses, modifikasi, perubahan metadata, dan informasi birth apabila tersedia.

---

### 16.3 Melihat Inode dengan `ls -i`

Command:

```bash
ls -li lab-data/documents/notes.txt
```

Output:

```text
1573649 -rw-rw-r-- 1 user user 19 Agu 27 13:29 lab-data/documents/notes.txt
```

### Observation

Nomor:

```text
1573649
```

merupakan inode file `notes.txt`.

Inode digunakan filesystem Unix/Linux untuk menyimpan metadata mengenai file.

---

## 17. Symbolic Link

Selanjutnya dibuat symbolic link menuju `notes.txt`.

Command:

```bash
ln -s ../documents/notes.txt lab-data/backup/notes-link.txt
```

Kemudian diperiksa menggunakan:

```bash
ls -l lab-data/backup/
```

Output:

```text
lrwxrwxrwx 1 user user 22 Agu 27 13:42 notes-link.txt -> ../documents/notes.txt
-rw-rw-r-- 1 user user 19 Agu 27 13:30 notes.txt
```

### Observation

`notes-link.txt` merupakan symbolic link yang menunjuk ke:

```text
../documents/notes.txt
```

Karakter `l` pada awal permission:

```text
lrwxrwxrwx
```

menunjukkan bahwa objek tersebut adalah symbolic link.

Isi target dapat diakses melalui symbolic link menggunakan:

```bash
cat lab-data/backup/notes-link.txt
```

Output:

```text
Linux CLI practice
```

### Troubleshooting

Pada percobaan pertama, symbolic link dibuat menggunakan target:

```bash
lab-data/documents/notes.txt
```

dari lokasi:

```text
lab-data/backup/
```

Akibatnya path tersebut ditafsirkan relatif terhadap lokasi symbolic link dan menghasilkan target yang tidak ditemukan.

Symbolic link kemudian diperbaiki menggunakan relative path:

```bash
ln -s ../documents/notes.txt lab-data/backup/notes-link.txt
```

Setelah diperbaiki, symbolic link berhasil digunakan untuk membaca file target.

### Verification

File asli:

```bash
ls -li lab-data/documents/notes.txt
```

Output:

```text
1573649 -rw-rw-r-- 1 user user 19 Agu 27 13:29 lab-data/documents/notes.txt
```

Symbolic link:

```bash
ls -li lab-data/backup/notes-link.txt
```

Output:

```text
1573644 lrwxrwxrwx 1 user user 22 Agu 27 13:42 lab-data/backup/notes-link.txt -> ../documents/notes.txt
```

### Analysis

File asli dan symbolic link memiliki inode yang berbeda:

```text
notes.txt      → inode 1573649
notes-link.txt → inode 1573644
```

Hal ini menunjukkan bahwa symbolic link merupakan objek filesystem tersendiri yang menyimpan referensi menuju path target, bukan salinan isi file.

---

# Troubleshooting / Mistake Encountered

Selama praktik terdapat percobaan menjalankan:

```bash
tasks.txt
notes.txt
```

Shell memberikan pesan:

```text
tasks.txt: command not found
notes.txt: command not found
```

### Analysis

Shell menginterpretasikan input tersebut sebagai nama command.

File teks biasa bukan command yang dapat langsung dijalankan.

Untuk melihat isi file digunakan command seperti:

```bash
cat tasks.txt
cat notes.txt
```

atau:

```bash
less tasks.txt
```

Jika sebuah file memang merupakan script yang dapat dieksekusi, cara menjalankannya bergantung pada jenis script dan permission file tersebut.

Contohnya:

```bash
./script.sh
```

dengan permission executable yang sesuai.

---

# Analysis

Praktik ini menunjukkan operasi dasar filesystem Linux secara langsung melalui CLI.

Beberapa operasi yang berhasil dilakukan meliputi:

* Membuat direktori menggunakan `mkdir`.
* Membuat file menggunakan `touch`.
* Menulis data menggunakan `echo` dan redirection.
* Membaca file menggunakan `cat`.
* Menyalin file menggunakan `cp`.
* Memindahkan dan rename file menggunakan `mv`.
* Menghapus file menggunakan `rm -i`.
* Mengidentifikasi hidden file menggunakan `ls -la`.
* Mencari file dan direktori menggunakan `find`.
* Mengamati penggunaan disk menggunakan `du`.
* Mengidentifikasi tipe file menggunakan `file`.
* Memeriksa metadata filesystem menggunakan `stat`.
* Memeriksa inode menggunakan `ls -i`.
* Membuat symbolic link menggunakan `ln -s`.
* Melakukan navigasi filesystem menggunakan `cd` dan `pwd`.

Praktik ini memberikan dasar untuk memahami bagaimana data diorganisasikan, disimpan, dan dikelola pada filesystem Linux.

---

# Security Relevance

Filesystem merupakan bagian penting dalam administrasi sistem dan keamanan Linux.

Kemampuan melakukan enumeration terhadap file dan direktori diperlukan untuk memahami:

* lokasi file konfigurasi,
* struktur direktori,
* file yang tersembunyi,
* lokasi data,
* penggunaan storage,
* ownership,
* permission,
* metadata filesystem,
* inode,
* serta symbolic link.

Pada security assessment yang terotorisasi, kemampuan seperti `find`, `ls`, `cat`, `stat`, dan `file` dapat membantu proses local system enumeration.

Lab ini dilakukan hanya pada sistem Ubuntu milik sendiri.

---

# Lessons Learned

Dari lab ini saya mempelajari:

* Struktur dasar direktori pada filesystem Linux.
* Perbedaan absolute path dan relative path.
* Navigasi filesystem menggunakan `cd` dan `pwd`.
* Pembuatan file dan direktori menggunakan `touch` dan `mkdir`.
* Operasi copy menggunakan `cp`.
* Operasi move dan rename menggunakan `mv`.
* Penghapusan file menggunakan `rm -i`.
* Konsep hidden file pada Linux.
* Pencarian file dan direktori menggunakan `find`.
* Pemeriksaan penggunaan disk menggunakan `du`.
* Identifikasi tipe file menggunakan `file`.
* Pemeriksaan metadata menggunakan `stat`.
* Konsep inode pada filesystem Linux.
* Perbedaan file biasa dengan symbolic link.
* Pentingnya memahami bagaimana shell menginterpretasikan input sebagai command.

---

# Conclusion

Lab 02 memberikan pengalaman hands-on dalam mengelola filesystem Linux melalui CLI.

Selain memahami command dasar, praktik ini menunjukkan bahwa operasi filesystem harus dilakukan dengan hati-hati karena command seperti `mv`, `cp`, dan terutama `rm` dapat mengubah atau menghapus data.

Lab ini juga memberikan pemahaman awal mengenai metadata filesystem, inode, dan symbolic link yang akan berguna untuk administrasi sistem dan pembelajaran keamanan Linux.

Pemahaman filesystem ini akan menjadi dasar untuk lab berikutnya, khususnya ketika mempelajari user, group, ownership, permission, process, service, dan system logs.
