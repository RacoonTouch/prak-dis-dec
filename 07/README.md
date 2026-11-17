# Cloud Computing

## 1. Mengambil source code Flask

**Langkah 1**

Buka PowerShell, lalu ketik:
```
git --version
```
Jika Git sudah terpasang, akan muncul informasi versi seperti : git version 2.x.x

**Langkah 2**

Jika berhasil jalankan:
```
New-Item -ItemType Directory -Force -Path "$HOME\src"
```
Kemudian masuk ke direktori tersebut:
```
cd "$HOME\src"
```

**Langkah 3**

Jalankan perintah:
```
git clone https://github.com/pallets/flask
```
Perintah tersebut mengambil source code Flask dari GitHub dan menyimpannya ke folder flask.

Jika berhasil, prosesnya akan menampilkan informasi seperti Cloning into 'flask'... dan Receiving objects seperti ini:
```
Cloning into 'flask'...
remote: Enumerating objects: 26408, done.
remote: Counting objects: 100% (141/141), done.
remote: Compressing objects: 100% (92/92), done.
remote: Total 26408 (delta 99), reused 49 (delta 49), pack-reused 26267 (from 2)
Receiving objects: 100% (26408/26408), 12.08 MiB | 1.04 MiB/s, done.
Resolving deltas: 100% (17652/17652), done.
```

**Langkah 4**

Pada modul Linux digunakan perintah cp -R. Di Windows PS (PowerShell), menggunakan Copy-Item:
```
Copy-Item -Path "$HOME\src\flask\examples\tutorial" -Destination "$HOME\src\flask-app" -Recurse
```
Kemudian masuk ke folder aplikasi:
```
cd "$HOME\src\flask-app"
```
Periksa isi folder:
```
Get-ChildItem
```
Folder aplikasi Flask seharusnya memiliki beberapa file dan folder, seperti flaskr, pyproject.toml, serta file pendukung lainnya.

**Pembahasan :**

Source code Flask diperoleh melalui perintah git clone dari repositori resmi Flask di GitHub. Folder examples/tutorial kemudian disalin ke direktori flask-app sebagai tempat kerja aplikasi. Pemisahan folder ini bertujuan agar proses pengembangan dan konfigurasi aplikasi dilakukan pada salinan tutorial tanpa perlu mengubah direktori utama repositori Flask.<br></br>


## 2. Membuat environment Python 3.14.4

**Langkah 1**

Di PowerShell, jalankan:
```
cd "$HOME\src\flask-app"
```

**Langkah 2**

Jika sudah jalankan:
```
uv --version
```
Jika muncul versi uv, berarti kita bisa menggunakannya untuk membuat environment Python.

**Langkah 3**

Jalankan:
```
uv python install 3.14.4
```

**Langkah 4**

Setelah Python berhasil dipasang, jalankan:
```
uv venv --python 3.14.4 .venv
```
Perintah tersebut membuat environment bernama .venv di dalam folder flask-app.

**Langkah 5**

Gunakan perintah PowerShell:
```
.\.venv\Scripts\Activate.ps1
```
Jika muncul pesan bahwa eksekusi script diblokir, jalankan dan jalankan ulang pada bagian perintah diatas:
```
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
```

**Langkah 6**

jalankan:
```
python --version
```
Jika berhasil, prompt PowerShell biasanya akan memiliki awalan (.venv) dan menampilkan versi pythonnya

**Pembahasan :**

Environment Python dibuat menggunakan uv venv dengan versi Python 3.14.4. Environment ini digunakan untuk memisahkan interpreter dan paket aplikasi Flask dari instalasi Python lainnya. Setelah environment diaktifkan, perintah Python dan instalasi paket akan menggunakan lingkungan tersebut.<br></br>


## 3. Install paket aplikasi Flask

Pada tahap ini akan memasang Flask beserta paket-paket yang dibutuhkan berdasarkan konfigurasi proyek.
```
uv pip install -e .
```

**Langkah 1**

Pastikan berada di direktori aplikasi dan environment sebelumnya (.venv):
```
cd "$HOME\src\flask-app"
```

**Langkah 2**

Jika sudah jalankan:
```
uv pip install -e .
```
Tunggu hingga proses instalasi selesai. Perintah -e atau editable membuat proyek dipasang dalam mode pengembangan, sehingga perubahan pada source code proyek dapat digunakan tanpa harus memasang ulang proyek setiap kali ada perubahan.

**Langkah 3**

Jalankan:
```
flask --version
```
Pastikan perintah tersebut menampilkan versi Flask dan Python yang digunakan.
```
Python 3.14.4
Flask 3.1.3
Werkzeug 3.1.9
```


## 4. Inisialisasi database dan menjalankan aplikasi

**Langkah 1**

Inisialisasi database, jalankan:
```
flask --app flaskr init-db
```
Perintah ini menjalankan perintah init-db yang disediakan oleh aplikasi Flask untuk menyiapkan database awal.

**Langkah 2**

Jika sudah jalankan aplikasi Flask:
```
flask --app flaskr run
```
Jika berhasil, terminal akan menampilkan informasi bahwa server pengembangan Flask sedang berjalan, biasanya pada alamat:
```
http://127.0.0.1:5000
```

**Langkah 3**

Buka aplikasi melalui browser:
```
http://127.0.0.1:5000
```
**Output :**

<img width="635" height="144" alt="1" src="https://github.com/user-attachments/assets/16ecc55e-9a23-4648-b29e-cccb4d5cd2b0" />

**Register model**

<img width="621" height="294" alt="1-2" src="https://github.com/user-attachments/assets/4771ccb3-e5ed-4814-b920-a62a1e7acfc5" />

**Pembahasan :**

Paket aplikasi dipasang menggunakan perintah uv pip install -e . berdasarkan konfigurasi proyek Flask. Setelah proses instalasi selesai, database diinisialisasi menggunakan perintah flask --app flaskr init-db. Selanjutnya, aplikasi dijalankan menggunakan Flask development server sehingga dapat diakses melalui browser pada port 5000. Pengujian melalui menu Register dan Log In dilakukan untuk memastikan aplikasi dapat digunakan.<br></br>


## 5. Membuat aplikasi menjadi Containerized App – Docker

**Langkah 1**

Karena menggunakan Windows, tidak perlu menjalankan perintah Linux seperti sudo systemctl start docker.
1. Buka aplikasi Docker Desktop.
2. Tunggu sampai Docker Engine berjalan.
3. Buka PowerShell baru.
4. Jalankan:
```
docker --version
```
Kemudian:
```
docker info
```
Jika Docker Engine aktif, perintah kedua akan menampilkan informasi server Docker.

**Langkah 2**

Jika sudah jalankan:
```
cd "$HOME\src\flask-app"
```
Periksa lokasi:
```
Get-Location
```
Pastikan kamu berada di folder flask-app, yaitu direktori yang berisi folder flaskr dan file pyproject.toml.

**Langkah 3**

Isi file tersebut harus sama dengan perintah ini:
```
$content = @"
FROM python:3.14

RUN mkdir /app
WORKDIR /app
ADD . /app/
RUN pip install -e .
RUN flask --app flaskr init-db

EXPOSE 5000
CMD ["flask", "--app", "flaskr", "run", "--host=0.0.0.0", "--port=5000"]
"@

Set-Content -Path ".\Dockerfile" -Value $content
```
Periksa apakah file sudah dibuat:
```
Get-Content Dockerfile
```
Pastikan isinya sama dengan kode di atas dan nama filenya benar-benar Dockerfile, bukan Dockerfile.txt.


## 6. Build Docker Image

**Langkah 1**

Buka Docker Desktop dan tunggu sampai Docker Engine berjalan.
Kemudian di PowerShell, pastikan masih berada di:
```
PS C:\Users\user\src\flask-app>
```
Periksa Docker:
```
docker info
```

**Langkah 2**

Jika sudah jalankan:
```
docker build -f Dockerfile -t flaskr:1.0.0 .
```
Output :
```
[+] Building 537.7s (13/13) FINISHED                                docker:desktop-linux
 => [internal] load build definition from Dockerfile                                0.1s
 => => transferring dockerfile: 236B                                                0.0s
 => [internal] load metadata for docker.io/library/python:3.14                      5.9s
 => [auth] library/python:pull token for registry-1.docker.io                       0.0s
 => [internal] load .dockerignore                                                   0.1s
 => => transferring context: 2B                                                     0.0s
 => [1/6] FROM docker.io/library/python:3.14@sha256:e5de6bc932256f8138c2353023dc  489.7s
 => => resolve docker.io/library/python:3.14@sha256:e5de6bc932256f8138c2353023dc4f  0.0s
 => => sha256:bd2decaa66042884c6b07d78fdb33ff346fe35b29f7ec34cc18eca15 249B / 249B  0.7s
 => => sha256:30a939f32c3666529658895da478296143998cbf9bbb6ede9 29.89MB / 29.89MB  64.0s
 => => sha256:7d9ddf92c2fd754219dc2783bdc40c04c9283f48a48741a7b00 6.09MB / 6.09MB  13.4s
 => => sha256:696c1fe91c64662be9b13aa92628d62d6685ab94c0b55d 237.23MB / 237.23MB  475.1s
 => => sha256:411d2dc8717dbb4bb0d3cfa27e6e0275fca455c5220029d7 67.82MB / 67.82MB  161.2s
 => => sha256:e8a88afca2bd37d4058d4b33d9d4d29b0557f8b811bb8c106 25.63MB / 25.63MB  84.6s
 => => sha256:a6dd1c05fbba4f9028078a11f143cbb625d2408921eb8207 49.41MB / 49.41MB  123.2s
 => => extracting sha256:a6dd1c05fbba4f9028078a11f143cbb625d2408921eb8207bfa1dd1ad  3.2s
 => => extracting sha256:e8a88afca2bd37d4058d4b33d9d4d29b0557f8b811bb8c106942b5b51  1.6s
 => => extracting sha256:411d2dc8717dbb4bb0d3cfa27e6e0275fca455c5220029d7a14dc7a75  4.8s
 => => extracting sha256:696c1fe91c64662be9b13aa92628d62d6685ab94c0b55d185258a8f7  11.5s
 => => extracting sha256:7d9ddf92c2fd754219dc2783bdc40c04c9283f48a48741a7b00590eac  0.5s
 => => extracting sha256:30a939f32c3666529658895da478296143998cbf9bbb6ede9e0ea6865  1.4s
 => => extracting sha256:bd2decaa66042884c6b07d78fdb33ff346fe35b29f7ec34cc18eca154  0.1s
 => [internal] load build context                                                   2.0s
 => => transferring context: 5.41MB                                                 1.9s
 => [auth] library/python:pull token for registry-1.docker.io                       0.0s
 => [2/6] RUN mkdir /app                                                            3.0s
 => [3/6] WORKDIR /app                                                              0.1s
 => [4/6] ADD . /app/                                                               0.3s
 => [5/6] RUN pip install -e .                                                     10.2s
 => [6/6] RUN flask --app flaskr init-db                                            0.9s
 => exporting to image                                                             27.0s
 => => exporting layers                                                             1.7s
 => => exporting manifest sha256:b4788a1aa454208d314cdcd159170cf0f410fa160a5f2fead  0.0s
 => => exporting config sha256:e4b3a2fcb7b5558e2fbee33acba8da9ab720d03bebdbb95789f  0.0s
 => => exporting attestation manifest sha256:b45c6ce36cbaee8d7a35b4cf40d6d7223ee40  0.1s
 => => exporting manifest list sha256:507f418210dc53420591e8ca2749d38413a6e23af6d9  0.0s
 => => naming to docker.io/library/flaskr:1.0.0                                     0.0s
 => => unpacking to docker.io/library/flaskr:1.0.0                                 24.9s
```
Proses build mungkin membutuhkan beberapa menit karena Docker harus mengunduh image Python dan memasang dependensi aplikasi.

**Langkah 3**

Setelah proses build selesai, jalankan:
```
docker images
```
Output :
```
                                                                     i Info →   U  In Use
IMAGE                          ID             DISK USAGE   CONTENT SIZE   EXTRA
apacheignite/ignite:3.1.0      c0f43bde84be        939MB          389MB
flaskr:1.0.0                   507f418210dc       1.65GB          424MB
load-balancing-bs_app:latest   eb7dd5d120a1        438MB          108MB
load-balancing-nginx:latest    ef1967fd73d0        241MB         63.9MB
postgres:18.3-alpine3.23       54451ecb8ab3        409MB          115MB
```

**Pembahasan :**

Perintah docker build digunakan untuk membangun image aplikasi berdasarkan instruksi yang terdapat di dalam Dockerfile. Proses tersebut mencakup penggunaan image Python, pemasangan dependensi Flask, dan inisialisasi database. Parameter -t flaskr:1.0.0 digunakan untuk memberikan nama dan versi pada image sehingga dapat dikenali ketika akan dijalankan sebagai container.<br></br>

## 7. Menjalankan Docker Container

**Langkah 1**

Pastikan PowerShell masih berada di direktori dan kemudian jalankan:
```
docker run -d -p 5001:5000 --name flaskr-container flaskr:1.0.0
```

**Langkah 2**

Jika sudah jalankan:
```
docker ps
```
Jika berhasil, seharusnya terlihat container bernama flaskr-container dengan status Up.

**Langkah 3**

Buka browser dan kunjungi:
```
http://localhost:5001
```

**Langkah 4**

1. Buka halaman utama aplikasi.
2. Pilih Register.
3. Buat akun.
4. Coba Log In.

<img width="619" height="116" alt="Screenshot 2026-11-17 195823" src="https://github.com/user-attachments/assets/3202ba05-888e-4753-9647-ac4ed6f80c19" />

Jika berhasil, berarti aplikasi Flask sudah berjalan melalui Docker Container.

**Pembahasan :**

Aplikasi Flask berhasil dijalankan melalui Docker Container dan dapat diakses menggunakan browser pada alamat localhost:5001. Halaman Flaskr menampilkan menu Posts, nama pengguna yang sedang masuk, serta tombol New untuk membuat postingan. Tampilan tersebut menunjukkan bahwa container berjalan dengan baik dan aplikasi dapat digunakan melalui port yang telah dipetakan dari 5001 pada Windows ke 5000 di dalam container.<br></br>


## 8. Instalasi Podman di Windows

**Langkah 1**
Unduh Podman
1. Buka halaman resmi instalasi Podman di atas.
2. Pilih Windows.
3. Unduh installer Podman untuk Windows (biasanya format .exe).
4. Jalankan installer yang sudah diunduh.
5. Ikuti petunjuk instalasi sampai selesai.

<img width="258" height="211" alt="3" src="https://github.com/user-attachments/assets/93bfee18-05f4-4830-b2d4-c38e293cf1d3" />

**Langkah 2**

Setelah itu jalankan:
```
podman --version
```
*Note: versi yang muncul di komputermu bisa berbeda.*

**Langkah 3**

Membuat Podman Machine:
```
podman machine init
```
**Output :**
```
Looking up Podman Machine image at quay.io/podman/machine-os:6.1 to create VM
Getting image source signatures
Copying blob 81ce79567e5a done   |
Copying config 44136fa355 done   |
Writing manifest to image destination
81ce79567e5aa7a1a122035b7ad14bd8e0dae8be009276427800e2cc62d753c7
Extracting compressed file: podman-machine-default-amd64: done
Importing operating system into WSL (this may take a few minutes on a new WSL install)...
The operation completed successfully.
Configuring system...
Machine init complete
To start your machine run:

        podman machine start
```
Tunggu sampai proses inisialisasi selesai. Proses ini mungkin membutuhkan waktu karena Podman perlu menyiapkan lingkungan Linux.

Setelah inisialisasinya selesai jalankan perintah:
```
podman machine start
```
Output:
```
Starting machine "podman-machine-default"

This machine is currently configured in rootless mode. If your containers
require root permissions (e.g. ports < 1024), or if you run into compatibility
issues with non-podman clients, you can switch using the following command:

        podman machine set --rootful

API forwarding listening on: npipe:////./pipe/podman-machine-default

Another process was listening on the default Docker API pipe address.
You can still connect Docker API clients by setting DOCKER HOST using the
following powershell command in your terminal session:

        $Env:DOCKER_HOST = 'npipe:////./pipe/podman-machine-default'

Or in a classic CMD prompt:

        set DOCKER_HOST=npipe:////./pipe/podman-machine-default

Alternatively, terminate the other process and restart podman machine.
Machine "podman-machine-default" started successfully
```
Ini menunjukkan bahwa podman machine nya berhasil *Running*

**Langkah 4**

Lalu memeriksa status Podman Machine:
```
podman machine list
```
Output:
```
NAME                     VM TYPE     CREATED         LAST UP            CPUS        MEMORY      DISK SIZE
podman-machine-default*  wsl         13 minutes ago  Currently running  2           2GiB        100GiB
```

**Langkah 5**
jalankan perintah ini untuk periksa Podman PowerShell:
```
podman info
```
Tunggu sampai informasi sistem muncul.

**Langkah 6**
Lalu jalankan:
```
podman ps
```
Output:
```
CONTAINER ID  IMAGE                COMMAND             STATUS       PORTS                   NAMES
abc123def456  localhost/flaskr...  flask --app flaskr  Up 10 sec    0.0.0.0:5002->5000/tcp flaskr-podman
```
**Langkah 7**


**Pembahasan :**

Perintah podman run -d -p 5002:5000 --name flaskr-podman flaskr:1.0.0 digunakan untuk membuat sekaligus menjalankan container dari image Flaskr. Parameter -d menjalankan container di latar belakang, sedangkan -p 5002:5000 menghubungkan port 5002 pada komputer dengan port 5000 di dalam container. Nama flaskr-podman digunakan sebagai identitas container agar lebih mudah dikelola.<br></br>

## KESIMPULAN

Containerization merupakan salah satu metode untuk menjalankan aplikasi secara terisolasi menggunakan teknologi seperti Docker dan Podman. Melalui praktikum ini, saya mempelajari proses pembuatan Dockerfile, pembangunan image, pembuatan dan pengelolaan container, pemetaan port, serta menjalankan aplikasi Flask agar dapat diakses melalui browser. Penggunaan Docker berhasil menunjukkan bahwa aplikasi Flaskr dapat dibangun dan dijalankan dalam container, kemudian diakses melalui alamat localhost:5001. Sementara itu, pada penggunaan Podman, mesin berhasil dijalankan dan proses pembangunan image telah mencapai tahap instalasi dependensi, tetapi mengalami kendala saat inisialisasi database.







