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
**Pembahasan:**

Di bagian pertama ini dilakukan pemeriksaan terhadap lingkungan kerja yang akan digunakan untuk praktikum Arsitektur Microservices. Pemeriksaan dilakukan melalui Windows PowerShell dengan menggunakan perintah python --version, uv --version, dan sqlite3 --version. Berdasarkan hasil pemeriksaan, Python belum tersedia secara langsung pada sistem Windows, sedangkan uv telah berhasil terpasang dengan versi 0.13.1. Sementara itu, perintah SQLite belum dapat dikenali oleh sistem. Pemeriksaan ini bertujuan untuk mengetahui perangkat lunak yang sudah tersedia dan menentukan kebutuhan instalasi sebelum proses pengembangan REST API dilakukan.<br></br>


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
**Pembahasan:**

Bagian kedua ini dilakukan instalasi Python menggunakan bantuan pengelola paket uv melalui perintah uv python install 3.14.4. Penggunaan uv mempermudah proses pengunduhan dan pengelolaan versi Python tanpa harus melakukan instalasi secara manual melalui Windows. Setelah proses instalasi selesai, dilakukan pemeriksaan menggunakan perintah uv python list --only-installed untuk memastikan Python telah terpasang. Hasil pemeriksaan menunjukkan bahwa Python sudah tersedia dan dapat digunakan sebagai interpreter dalam menjalankan program.<br></br>


## 3. Membuat folder proyek

**3.1 Periksa Python**
```
uv python list --only-installed
```
Pastikan Python 3.14.4 muncul.

**3.2 Buat folder praktikum**
```
cd $HOME\Documents
```
Kemudian buat folder:
```
mkdir praktikum-modul-8
```
Masuk ke folder tersebut:
```
cd praktikum-modul-8
```
Periksa lokasinya:
```
pwd
```
Hasilnya kurang lebih:
```
C:\Users\user\Documents\praktikum-modul-8
```
**Pembahasan:**

Pembuatan direktori dilakukan menggunakan perintah mkdir, sedangkan perintah cd digunakan untuk berpindah ke lokasi direktori tersebut. Setelah itu, source code dari arsip src-08.tar.bz2 diekstrak menggunakan perintah tar -xjf sehingga menghasilkan folder src yang berisi berkas program, database, dan konfigurasi proyek. Pada folder tersebut kemudian dibuat virtual environment menggunakan uv venv --python 3.14.4 dan diaktifkan melalui perintah .venv\Scripts\Activate.ps1. Lingkungan virtual ini digunakan agar instalasi paket dan kebutuhan Python pada praktikum dapat dikelola secara terpisah dari lingkungan sistem utama.<br></br>


## 4. Mengekstrak source code

**4.1 Siapkan file**

Unduh lampiran src-08.tar.bz2, kemudian letakkan di folder Downloads Windows.

**4.2 Ekstrak file**
```
tar -xjf "$HOME\Downloads\src-08.tar.bz2"
```
Perintah tersebut akan mengekstrak arsip dan menghasilkan folder src.

**4.3 Masuk ke folder source code:**
```
cd src
```
**4.4 Kemudian periksa isinya:**
```
dir
```
**4.5 File yang seharusnya tersedia:**
```
.python-version
departemen-sdm.db
service.py
requirements.txt
```
**Pembahasan:**

Arsip src-08.tar.bz2 yang telah disiapkan perlu diekstrak terlebih dahulu agar seluruh berkas program dapat digunakan dalam praktikum. Proses ekstraksi dilakukan melalui PowerShell dengan perintah tar -xjf, yang berfungsi untuk membuka arsip dan menghasilkan folder src. Setelah proses tersebut selesai, perintah cd src digunakan untuk masuk ke direktori hasil ekstraksi, sedangkan dir digunakan untuk melihat daftar berkas di dalamnya. Hasil pemeriksaan menunjukkan adanya beberapa berkas penting, seperti service.py, departemen-sdm.db, requirements.txt, dan .python-version.<br></br>


## 5. Membuat virtual environment

**5.1 Menjalankan venv. python:**
```
uv venv --python 3.14.4
```
Perintah tersebut membuat lingkungan Python terpisah di dalam folder .venv.

**5.2 mengaktifkan virtual environment:**
```
.venv\Scripts\Activate.ps1
```
Perintah tersebut akan mengekstrak arsip dan menghasilkan folder src.

*catatan: jika terjadi error atau tidak dapat menjalankan lingkungan venv nya gunakan perintah ini, setelah itu jalankan kembali di tahap 5.2. Jika ada permintaan kondirmasi, pilih: Y*
```
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
```
Output:
```
(.venv) PS C:\Users\user\Documents\praktikum-modul-8\src>
```
**Pembahasan:**

Agar kebutuhan Python pada proyek dapat dikelola secara terpisah, dibuat lingkungan virtual menggunakan perintah uv venv --python 3.14.4. Perintah tersebut menghasilkan direktori .venv yang berfungsi sebagai tempat penyimpanan lingkungan Python khusus untuk proyek Modul 8. Selanjutnya, lingkungan tersebut diaktifkan menggunakan perintah .venv\Scripts\Activate.ps1 melalui Windows PowerShell. Ketika proses aktivasi berhasil, muncul tanda (src) pada bagian awal terminal yang menunjukkan bahwa lingkungan virtual telah aktif.<br></br>


## 6. Instalasi FastAPI dan SQLModel

**6.1 Menjalankan instalasi:**
```
uv pip install "fastapi[standard]" sqlmodel
```
Tunggu sampai instalasi selesai.

**Output :**
```
Resolved 61 packages in 8.55s
Prepared 44 packages in 24.34s
Installed 61 packages in 3.04s
 + agent-detector==2.0.0
 + annotated-doc==0.0.7
 + annotated-types==0.8.0
 + anyio==4.16.0
 + certifi==2026.11.20
 + charset-normalizer==3.5.3
 + click==8.5.1
 + colorama==0.4.6
 + detect-installer==0.2.1
 + dnspython==2.9.2
 + email-validator==2.4.0
 + fastapi==0.165.0
 + fastapi-cli==0.0.34
 + fastapi-cloud-cli==0.28.0
 + fastar==0.12.0
 + googleapis-common-protos==1.76.0
 + h11==0.16.0
 + httpcore==1.0.9
 + httpcore2==2.16.2
 + httptools==0.9.0
 + httpx==0.28.1
 + httpx2==2.16.2
 + idna==3.20
 + jinja2==3.1.6
 + markdown-it-py==4.3.0
 + markupsafe==3.0.4
 + mdurl==0.1.2
 + opentelemetry-api==1.45.2
 + opentelemetry-exporter-http-transport==0.66b2
 + opentelemetry-exporter-otlp-common==0.66b2
 + opentelemetry-exporter-otlp-proto-common==1.45.2
 + opentelemetry-exporter-otlp-proto-http==1.45.2
 + opentelemetry-proto==1.45.2
 + opentelemetry-sdk==1.45.2
 + opentelemetry-semantic-conventions==0.66b2
 + protobuf==7.36.2
 + pydantic==2.14.4
 + pydantic-core==2.50.5
 + pydantic-extra-types==2.11.1
 + pydantic-settings==2.16.3
 + pygments==2.21.0
 + python-dotenv==1.2.4
 + python-multipart==0.0.34
 + pyyaml==6.0.4
 + requests==2.34.2
 + rich==15.0.0
 + rich-toolkit==0.20.6
 + rignore==0.8.1
 + sentry-sdk==3.0.0
 + shellingham==1.5.4
 + sqlalchemy==2.1.9
 + sqlmodel==0.0.50
 + starlette==1.10.0
 + truststore==0.10.5
 + typer==0.29.2
 + typing-extensions==4.16.0
 + typing-inspection==0.4.5
 + urllib3==2.8.0
 + uvicorn==0.54.0
 + watchfiles==1.3.0
 + websockets==17.4
```

**6.2 periksa paket:**
```
uv pip list
```
Pastikan terdapat:
- fastapi
- sqlmodel
- sqlalchemy
- uvicorn

**Pembahasan:**

Bagian enam ini kebutuhan pustaka untuk membangun layanan backend dipenuhi dengan memasang FastAPI dan SQLModel melalui perintah uv pip install "fastapi[standard]" sqlmodel. Proses instalasi menghasilkan sejumlah paket pendukung, termasuk FastAPI, SQLModel, SQLAlchemy, dan Uvicorn. Setelah pemasangan selesai, perintah uv pip list digunakan untuk memeriksa daftar paket yang telah terinstal di lingkungan virtual.<br></br>


## 7. Menginstal SQLite di Windows

**7.1 Download SQLite**

a. Buka halaman resmi SQLite: [SQLite Download.](https://www.sqlite.org/download.html)

b. Cari bagian Precompiled Binaries for Windows.

c. Unduh file bernama sqlite-tools-win-x64-....zip.

<img width="621" height="558" alt="image" src="https://github.com/user-attachments/assets/f411e5d0-c7d1-477b-9e29-9e2e8562688f" />


Memilih versi x64 karena Windows yang digunakan adalah arsitektur x86-64.

**7.2 Ekstrak file**

1. Buka File Explorer.

2. Masuk ke folder Downloads.

3. Klik kanan ZIP SQLite yang sudah diunduh.

4. Pilih Extract All.

5. Buat folder baru bernama sqlite di drive C:.
```
C:\sqlite
```
Pindahkan isi hasil ekstraksi ke folder tersebut.

**7.3 Hubungkan SQLite dengan PowerShell**

Jalankan:
```
$env:Path += ";C:\sqlite"
```
Kemudian periksa:
```
sqlite3 --version
```
Seharusnya muncul informasi versi SQLite. Contoh:
```
3.54.1 2026-10-16 14:45:20 555e07ef33f69a0957d24d8e555b702464a131d6f1607f4c3c1e4551e8e6ca76 (64-bit)
```
**Pembahasan:**

Bagian ini berbeda dengan Python yang dikelola menggunakan uv, SQLite Command Line Tools perlu disiapkan secara terpisah agar dapat digunakan melalui Windows PowerShell. Instalasi dilakukan dengan mengunduh paket sqlite-tools-win-x64 dari situs resmi SQLite, kemudian mengekstrak isinya ke direktori C:\sqlite. Setelah itu, lokasi SQLite ditambahkan ke variabel PATH sementara menggunakan perintah $env:Path += ";C:\sqlite". Pemeriksaan dilakukan melalui perintah sqlite3 --version dan menghasilkan informasi versi SQLite yang terpasang.<br></br>


## 8. Memeriksa database contoh

**8.1 Buka database**
```
sqlite3 departemen-sdm.db
```
Setelah muncul prompt sqlite>, jalankan:
```
.tables
```
Kemudian:
```
SELECT * FROM sdm;
```
Hasilnya menampilkan tiga data karyawan, yaitu Karyawan 1, Karyawan 2, dan Karyawan 3.

**Output:**

<img width="770" height="270" alt="image" src="https://github.com/user-attachments/assets/1f5acc0f-de8c-43c4-9907-c5ef65d2f28a" />

**Pembahasan:**

Database contoh yang disediakan dalam source code diperiksa menggunakan SQLite melalui perintah sqlite3 departemen-sdm.db. Setelah berhasil masuk ke lingkungan SQLite, perintah .tables digunakan untuk mengetahui tabel yang tersedia di dalam database. Selanjutnya, perintah SELECT * FROM sdm; dijalankan untuk mengambil seluruh isi tabel SDM. Berdasarkan hasil yang ditampilkan, terdapat tiga data karyawan dengan informasi NPP dan nama masing-masing. Hasil tersebut menunjukkan bahwa database berhasil dibuka dan data yang telah disediakan dapat dibaca menggunakan perintah SQL.<br></br>


## 9. Menjalankan FastAPI

**9.1 Pastikan kamu sudah keluar dari SQLite dengan:**
```
.quit
```
Pastikan terminal kembali ke PowerShell dengan awalan (.venv).

**9.2. Jalankan source code**
```
uv run fastapi dev service.py
```
Tunggu sampai muncul:
```
Application startup complete.
```

**9.3 Buka API melalui browser**

Buka Chrome, kemudian akses:
```
http://127.0.0.1:8000/sdm/
```
Seharusnya muncul tiga data karyawan dalam format JSON.
contohnya:
```
[{"npp":"112233","nama":"Karyawan 1","id":1},{"npp":"223344","nama":"Karyawan 2","id":2},{"npp":"334455","nama":"Karyawan 3","id":3}]
```

**9.4 Uji menggunakan curl**

Buka PowerShell baru (jangan tutup terminal server yang sedang berjalan), lalu jalankan:
```
curl.exe http://127.0.0.1:8000/sdm/
```
Output:
```
[{"npp":"112233","nama":"Karyawan 1","id":1},{"npp":"223344","nama":"Karyawan 2","id":2},{"npp":"334455","nama":"Karyawan 3","id":3}]
```

**Pembahasan:**

Pada tahap ini, source code service.py dijalankan menggunakan FastAPI untuk menyediakan layanan REST API. Setelah perintah dijalankan, server FastAPI berhasil aktif pada alamat http://127.0.0.1:8000 dan menampilkan pesan Application startup complete. sebagai tanda bahwa aplikasi telah selesai melakukan proses inisialisasi. Pengujian kemudian dilakukan dengan mengakses endpoint /sdm/ melalui browser dan perintah curl.exe http://127.0.0.1:8000/sdm/. Hasil pengujian menunjukkan tiga data karyawan berhasil ditampilkan dalam format JSON, yang mencakup ID, NPP, dan nama karyawan.<br></br>


## TUGAS
1. Buat satu tabel baru menggunakan SQLite, tabel berbeda dari yang ada pada contoh. Tabel tersebut mempunyai 1 primary key dan setidaknya berisi data dengan tipe INT, CHAR, VARCHAR, BOOLEAN, dan FLOAT.
2. Isikan 5 data menggunakan script Python.
3. Buat RESTful API endpoint untuk menampilkan semua data yang telah diisikan.
4. Tampilkan hasil RESTful API endpoint tersebut menggunakan curl.

## Membuat database dan tabel baru

**1. Masuk pada folder proyek praktikum**
```
cd "$HOME\Documents\praktikum-modul-8\src"
```

**2. Mengaktifkan virtual environment:**
```
.venv\Scripts\Activate.ps1
```

**3. Buat file**

menjalankan perintah notepad untuk membuka aplikasi notepad dalam menyimpan kode:
```
notepad tugas.py
```
gunakan kode ini:
```
Python

from typing import List
from fastapi import FastAPI, Depends
from sqlmodel import Field, Session, SQLModel, create_engine, select
from sqlalchemy import CHAR, VARCHAR


class Produk(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    kode: str = Field(sa_type=CHAR(5))
    nama: str = Field(sa_type=VARCHAR(100))
    tersedia: bool
    harga: float


engine = create_engine("sqlite:///produk.db")

SQLModel.metadata.create_all(engine)


def get_session():
    with Session(engine) as session:
        yield session


app = FastAPI()


@app.get("/produk/", response_model=List[Produk])
def read_produk(session: Session = Depends(get_session)):
    statement = select(Produk)
    results = session.exec(statement).all()
    return results

```
Setelah menyimpan kode tersebut simpab datanya. *bisa menggunakan Ctrl + S*

**4. memuat script untuk mengisi data**
```
notepad isi_produk.py
```

lalu isi notepad ini dengan kode python ini:
```
Python

from sqlmodel import Session, SQLModel
from tugas import Produk, engine

SQLModel.metadata.create_all(engine)

data_produk = [
    Produk(kode="P001", nama="Laptop", tersedia=True, harga=7500000.0),
    Produk(kode="P002", nama="Keyboard", tersedia=True, harga=250000.0),
    Produk(kode="P003", nama="Mouse", tersedia=True, harga=150000.0),
    Produk(kode="P004", nama="Monitor", tersedia=False, harga=1800000.0),
    Produk(kode="P005", nama="Printer", tersedia=True, harga=2200000.0),
]

with Session(engine) as session:
    session.add_all(data_produk)
    session.commit()

print("Berhasil memasukkan 5 data produk!")
```

**5. Menjalankan script Python**
```
uv run python isi_produk.py
```
Output:
```
Berhasil memasukkan 5 data produk!
```

**Pembahasan:**

Pada tahap ini dibuat tabel produk menggunakan SQLModel dengan lima jenis atribut, yaitu integer sebagai primary key, CHAR untuk kode produk, VARCHAR untuk nama produk, BOOLEAN untuk status ketersediaan, dan FLOAT untuk harga produk. Selanjutnya, lima data produk dimasukkan menggunakan script Python melalui SQLModel. Data tersebut disimpan ke dalam database SQLite bernama produk.db sehingga dapat digunakan oleh REST API.<br></br>


**6. Menjalankan REST API Produk**

Membuat PowerShell baru atau gunakan terminal yang virtual environment-nya sudah aktif (yang ada *(src) PS C:* di belakangnya menggunankan kode: .venv\Scripts\Activate.ps1).

Pastikan berada di folder src, kemudian jalankan:
```
uv run fastapi dev tugas.py --port 8001
```
Jika berhasil, akan muncul:
```
Server started at http://127.0.0.1:8001
Application startup complete.
```

**7. Akses endpoint melalui browser**
```
http://127.0.0.1:8001/produk/
```
Output:
```
[{"id":1,"tersedia":true,"kode":"P001","nama":"Laptop","harga":7500000.0},{"id":2,"tersedia":true,"kode":"P002","nama":"Keyboard","harga":250000.0},{"id":3,"tersedia":true,"kode":"P003","nama":"Mouse","harga":150000.0},{"id":4,"tersedia":false,"kode":"P004","nama":"Monitor","harga":1800000.0},{"id":5,"tersedia":true,"kode":"P005","nama":"Printer","harga":2200000.0}]
```

**8. Pengujian menggunakan curl**

Pada tahap ini membuat powershell baru untuk melakukan pengujian nya berhasil atau tidak menggunakan curl. ini perintahnya:
```
curl.exe http://127.0.0.1:8001/produk/
```
Output:
```
[{"id":1,"tersedia":true,"kode":"P001","nama":"Laptop","harga":7500000.0},{"id":2,"tersedia":true,"kode":"P002","nama":"Keyboard","harga":250000.0},{"id":3,"tersedia":true,"kode":"P003","nama":"Mouse","harga":150000.0},{"id":4,"tersedia":false,"kode":"P004","nama":"Monitor","harga":1800000.0},{"id":5,"tersedia":true,"kode":"P005","nama":"Printer","harga":2200000.0}]
```
muncul lima data JSON dengan informasi seperti ini dia: ID Produk, Kode produk, Nama produk, status ketersediaan, dan  harga produk.

**Pembahasan :**

Pada tahap ini dilakukan implementasi RESTful API menggunakan FastAPI untuk menampilkan seluruh data produk yang tersimpan dalam database SQLite. Endpoint GET /produk/ dibuat dengan SQLModel untuk mengambil data menggunakan perintah select(Produk). Selanjutnya, pengujian dilakukan menggunakan curl.exe untuk mengirimkan HTTP GET request ke server. Hasil pengujian ditampilkan dalam format JSON yang berisi lima data produk beserta atributnya. Sehingga, menampilkan seluruh data dari tabel baru melalui REST API.<br></br>






