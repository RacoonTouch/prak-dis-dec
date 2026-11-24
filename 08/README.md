# Arsitektur Microservices untuk Sistem Terdistribusi

## 1. Menyiapkan Environtment

**1.1. Memeriksa Python:**

Dalam tahap ini, perlunya memeriksa lingkungan paket yang akan digunakan serta alat yang akan membantu proses dalam melakukan praktikum nanti. Pertama masuk pada windows Powersheel. *Disini saya menggunakan sistem operasi windows, sehingga akan berbeda cara memasang paket pada lingkungannya.

```
python --version
```

**1.2. Memeriksa uv:**
```
uv --version
```

**1.3. Memeriksa SQLite:**
```
sqlite3 --version
```

## 2. Menginstal Python 3.14.4

**2.1 Menjalankan perintah ini:**
```
uv python install 3.14.4
```
Tunggu sampai proses instalasi selesai.

**2.2 Memeriksa Python sudah berhasil dipasang:**
```
uv python list --only-installed
```
Output jika sudah terinstal:
```
cpython-3.14.8-windows-x86_64-none...
```




















