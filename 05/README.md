<h1>Fault Tolerance</h1>

<h3>A. TUJUAN PRAKTIKUM</h3>

1. Memahami penerapan Load Balancing untuk mendistribusikan permintaan pengguna ke beberapa instance aplikasi.
2. Memahami mekanisme Failure Detection untuk mengetahui kondisi suatu server.
3. Menerapkan metode Heartbeat, Retry, dan Circuit Breaker dalam menangani kegagalan koneksi.
4. Mengetahui peran Docker, Nginx, dan Python dalam membangun aplikasi yang lebih andal pada sistem terdistribusi.

<h3>B. DASAR TEORI</h3>

**1. Fault Tolerance**

Fault Tolerance merupakan kemampuan suatu sistem untuk tetap beroperasi ketika terjadi kesalahan atau gangguan pada salah satu komponennya. Dalam sistem terdistribusi, gangguan dapat terjadi karena server tidak aktif, koneksi jaringan terputus, atau layanan tidak dapat memberikan respons. Oleh karena itu, diperlukan mekanisme yang dapat mengurangi dampak kegagalan agar sistem tetap dapat digunakan.

**2. Load Balancing**

Load Balancing merupakan teknik untuk membagi permintaan pengguna ke beberapa server atau instance aplikasi. Pembagian ini bertujuan menghindari penumpukan beban pada satu server dan membantu meningkatkan ketersediaan layanan. Pada praktikum ini, Nginx digunakan sebagai reverse proxy yang meneruskan permintaan pengguna menuju aplikasi Blacksheep yang dijalankan menggunakan Docker. Docker Compose digunakan untuk menjalankan dua instance aplikasi secara bersamaan.

**3. Heartbeat**

Heartbeat merupakan mekanisme untuk memeriksa apakah suatu server masih aktif dan dapat dijangkau. Pemeriksaan dilakukan dengan mengirimkan atau mencoba koneksi ke alamat dan port server. Jika koneksi berhasil, server dianggap aktif. Sebaliknya, jika koneksi gagal, server dianggap tidak aktif. Pada praktikum ini, pemeriksaan dilakukan menggunakan library socket pada Python.

**4. Retry**

Retry merupakan mekanisme untuk mengulangi permintaan ketika terjadi kegagalan sementara. Mekanisme ini berguna ketika kegagalan koneksi hanya berlangsung sesaat sehingga permintaan dapat berhasil pada percobaan berikutnya. Pada praktikum ini, library Tenacity digunakan untuk mengatur jumlah percobaan dan jeda waktu sebelum mengulang permintaan.

**5. Circuit Breaker**

Circuit Breaker merupakan mekanisme yang menghentikan sementara permintaan ketika suatu layanan mengalami kegagalan berulang. Tujuannya adalah mencegah sistem terus mengirim permintaan kepada server yang sedang bermasalah. Circuit Breaker memiliki tiga kondisi, yaitu Closed, Open, dan Half-open. Closed berarti permintaan dapat dilakukan seperti biasa, Open berarti permintaan dihentikan sementara karena batas kegagalan tercapai, sedangkan Half-open berarti sistem mengizinkan percobaan kembali untuk memeriksa apakah server sudah pulih.

<h3>C. PEMBAHASAN PRAKTIKUM</h3>
<h4>4.1 Load Balancing Aplikasi</h4>

**4.1.1 Dockerfile Aplikasi Blacksheep**

file yang digunakan adalah: 
```
PS C:\Users\user\workspace-05\src\load-balancing\blacksheep_lb>
```

Pastikan virtual environment Windows sudah tersedia:
```
PS C:\Users\user\workspace-05\src\load-balancing\blacksheep_lb> Test-Path "$HOME\workspace-05\.venv\Scripts\python.exe"
True
```
Jika hasilnya True, jalankan:
```
uv pip install --python "$HOME\workspace-05\.venv\Scripts\python.exe" -r requirements.txt

      Built markupsafe==3.0.2                                                                                                        Prepared 28 packages in 11.57s
Installed 37 packages in 870ms
 + annotated-types==0.8.0
 + blacksheep==2.6.4
 + certifi==2026.10.23
 + cffi==2.1.1
 + click==8.5.0
 + colorama==0.4.6
 + cryptography==46.0.7
 + deepmerge==2.1.0
 + defusedxml==0.7.1
 + essentials==1.1.9
 + essentials-configuration==2.0.5
 + essentials-openapi==1.4.0
 + guardpost==1.1.0
 + h11==0.16.0
 + h2==4.4.1
 + hpack==4.2.0
 + httptools==0.9.0
 + hyperframe==6.1.0
 + itsdangerous==2.2.0
 + jinja2==3.1.6
 + markdown-it-py==4.2.0
 + markupsafe==3.0.2
 + mdurl==0.1.2
 + pycparser==3.12
 + pydantic==2.14.2
 + pydantic-core==2.50.2
 + pygments==2.21.0
 + pyjwt==2.15.1
 + python-dotenv==1.2.4
 + pyyaml==6.0.3
 + rich==15.0.0
 + rich-click==1.9.9
 + rodi==2.0.8
 + typing-extensions==4.16.0
 + typing-inspection==0.4.4
 + uvicorn==0.34.3
 + websockets==15.0.1
```
**Pembahasan :**

Dockerfile aplikasi menggunakan image dasar python:3.14.4-slim untuk membangun lingkungan aplikasi. Perintah WORKDIR menentukan direktori kerja, sedangkan COPY menyalin kode aplikasi ke dalam container. Selanjutnya, Python virtual environment dibuat dan dependensi dari requirements.txt dipasang. Pada tahap akhir, Uvicorn digunakan untuk menjalankan aplikasi Blacksheep melalui port 80 agar dapat menerima permintaan dari Nginx.<br></br>


**4.1.2 Dockerfile Nginx**

File yang digunakan adalah :
```
src/load-balancing/nginx/Dockerfile
```
```
PS C:\Users\user\workspace-05\src\load-balancing\blacksheep_lb> Get-ChildItem "$HOME\workspace-05\src\load-balancing\nginx" -Force


    Directory:
    C:\Users\user\workspace-05\src\load-balancing\nginx


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         4/19/2026   8:40 PM            136 Dockerfile
-a----         4/19/2026   8:40 PM            281 nginx.conf
```
**Pembahasan :**
Dockerfile Nginx menggunakan image bawaan Nginx sebagai dasar. Perintah RUN rm digunakan untuk menghapus konfigurasi default agar tidak bertabrakan dengan konfigurasi yang dibuat. Selanjutnya, perintah COPY menyalin file nginx.conf ke dalam direktori konfigurasi Nginx. Dengan demikian, Nginx akan menggunakan pengaturan reverse proxy yang telah disiapkan untuk meneruskan permintaan menuju aplikasi.<br></br>


**4.1.3 Konfigurasi Nginx**

File yang digunakan adalah:
```
src/load-balancing/nginx/nginx.conf
```
```
PS C:\Users\user\workspace-05\src\load-balancing\blacksheep_lb> Get-Content "$HOME\workspace-05\src\load-balancing\nginx\nginx.conf"
events {
  worker_connections 4096;
}

http {
  # Definisi grup, lihat di docker-compose.yml
  upstream backend {
    server bs_app:80;
  }
```
**Pembahasan :**

Konfigurasi Nginx menggunakan bagian events untuk menentukan jumlah koneksi yang dapat ditangani oleh worker. Bagian upstream backend menentukan tujuan layanan aplikasi, yaitu bs_app pada port 80. Selanjutnya, Nginx mendengarkan permintaan melalui port 80 dan meneruskannya menggunakan proxy_pass menuju backend. Pengaturan ini membuat Nginx berfungsi sebagai pintu masuk bagi permintaan pengguna ke aplikasi Blacksheep.<br></br>


**4.1.4 Konfigurasi Docker Compose**

File yang digunakan adalah:
```
src/load-balancing/docker-compose.yml
```
```
PS C:\Users\user\workspace-05\src\load-balancing\blacksheep_lb> Get-Content "$HOME\workspace-05\src\load-balancing\docker-compose.yml"
services:
  # aplikasi yang akan di-scale
  # ada pada direktori blacksheep_lb
  bs_app:
    build: ./blacksheep_lb
  # aktifkan nginx dengan menggunakan nginx/Dockerfile
  # lihat pada build
  nginx:
    container_name: nginx
    build: ./nginx
    ports:
      - 80:80
    depends_on:
      - bs_app
```
**Pembahasan :**

File Docker Compose mendefinisikan dua layanan, yaitu bs_app dan nginx. Layanan bs_app digunakan untuk membangun aplikasi Blacksheep, sedangkan layanan nginx digunakan sebagai reverse proxy. Pengaturan ports: 80:80 menghubungkan port 80 pada komputer dengan port 80 di container Nginx. Sementara itu, depends_on menentukan bahwa layanan Nginx bergantung pada layanan aplikasi.<br></br>


**4.1.5 Menjalankan Load Balancing**

Perintah yang digunakan:
```
cd "$HOME\workspace-05\src\load-balancing"

docker compose up --build -d --scale bs_app=2
```
**Output :**
```
[+] Building 199.3s (19/25)
 => => transferring context: 320B                                                                                               0.1s
 => [bs_app internal] load build context                                                                                        1.7s
 => => transferring context: 3.76MB                                                                                             1.5s
 => [bs_app server_builder 1/6] FROM docker.io/library/python:3.14.4-slim@sha256:2ca02f32b4d9d893863367ce07ec1972819f476dd38d  35.7s
 => => resolve docker.io/library/python:3.14.4-slim@sha256:2ca02f32b4d9d893863367ce07ec1972819f476dd38d8612f2a9cb6a41cbb727     0.2s
 => => sha256:765e5bc6b73e0d0f223f9d3c00a231903580b2233cf4beb9bba35041519fbd92 12.27MB / 12.27MB                                6.6s
 => => sha256:74cf12ae9d2089e998920c83152f6fd488418715b42de71a60029dee4d9fabfe 250B / 250B                                      1.8s
 => => sha256:6ab80c0b8b94e86367b7670f7b513efd102e283d9cd8de7aa4660999d1e30d97 1.29MB / 1.29MB                                  3.9s
 => => sha256:57fb71246055257a374deb7564ceca10f43c2352572b501efc08add5d24ebb61 29.78MB / 29.78MB                               25.1s
 => => extracting sha256:57fb71246055257a374deb7564ceca10f43c2352572b501efc08add5d24ebb61                                       6.0s
 => => extracting sha256:6ab80c0b8b94e86367b7670f7b513efd102e283d9cd8de7aa4660999d1e30d97                                       0.6s
 => => extracting sha256:765e5bc6b73e0d0f223f9d3c00a231903580b2233cf4beb9bba35041519fbd92                                       2.4s
 => => extracting sha256:74cf12ae9d2089e998920c83152f6fd488418715b42de71a60029dee4d9fabfe                                       0.1s
 => [bs_app server_builder 2/6] WORKDIR /home                                                                                   1.3s
 => [bs_app server_builder 3/6] RUN echo "APT::Get::Assume-Yes \"true\";" > /etc/apt/apt.conf.d/90assumeyes                     1.0s
 => [bs_app server_builder 4/6] RUN apt-get update && apt-get install make     apt-utils     lsb-release     apt-transport-h  151.9s
 => => # Setting up manpages-dev (6.9.1-1) ...
 => => # Setting up libodbcinst2:amd64 (2.3.12-2+deb13u1) ...
 => => # Setting up gpg-agent (2.4.7-21+deb13u1+b5) ...
 => => # Setting up gpgsm (2.4.7-21+deb13u1+b5) ...
 => => # Setting up libidn2-0:amd64 (2.3.8-2) ...
 => => # Setting up libnss3:amd64 (2:3.110-1+deb13u5) ...
 => [nginx 2/3] RUN rm /etc/nginx/conf.d/default.conf                                                                           1.0s
 => [nginx 3/3] COPY nginx.conf /etc/nginx/nginx.conf          [+] Building 199.5s (19/25)
 => => sha256:765e5bc6b73e0d0f223f9d3c 12.27MB / 12.27MB  6.6s
 => => sha256:74cf12ae9d2089e998920c83152f6f 250B / 250B  1.8s
 => => sha256:6ab80c0b8b94e86367b7670f7b 1.29MB / 1.29MB  3.9s
 => => sha256:57fb71246055257a374deb7 29.78MB / 29.78MB  25.1s
[+] Building 389.1s (27/27) FINISHED
 => [internal] load local bake definitions                0.0s
 => => reading from stdin 1.09kB                          0.0s
 => [nginx internal] load build definition from Dockerfi  0.2s7
 => => transferring dockerfile: 175B                      0.0s
 => [bs_app internal] load build definition from Dockerf  0.2s
 => => transferring dockerfile: 1.22kB                    0.1s7
 => [bs_app internal] load metadata for docker.io/librar  6.4s
 => [nginx internal] load metadata for docker.io/library  6.4s
 => [auth] library/python:pull token for registry-1.dock  0.0s1
 => [auth] library/nginx:pull token for registry-1.docke  0.0s
 => [bs_app internal] load .dockerignore                  0.3s
 => => transferring context: 105B                         0.1sc
 => [nginx internal] load .dockerignore                   0.2s
 => => transferring context: 2B                           0.0s
 => [nginx 1/3] FROM docker.io/library/nginx:latest@sha  43.7s
 => => resolve docker.io/library/nginx:latest@sha256:5ab  0.2s
 => => sha256:a670537bed7086aeb8b663aad5 1.40kB / 1.40kB  1.0s
 => => sha256:c139fe92686de21a8c49d9cb40415d 404B / 404B  0.8se
 => => sha256:6e2ab1f3ef4e0ea3bbe1d208dfa048 964B / 964B  0.7s
 => => sha256:d322f1bf41d8e99dc47f550cbf 1.21kB / 1.21kB  1.3s
 => => sha256:9f604dae09b216f6c87919d0e59d5a 628B / 628B  1.3s
 => => sha256:a071113ac575190a0fee0c0 34.05MB / 34.05MB  22.2s
 => => sha256:6fc242d221a673263eabd14 29.86MB / 29.86MB  27.0s
 => => extracting sha256:6fc242d221a673263eabd14a9ad9584  3.3s
 => => extracting sha256:a071113ac575190a0fee0c046bcf516  5.4s
 => => extracting sha256:9f604dae09b216f6c87919d0e59d5a4  0.1s
 => => extracting sha256:6e2ab1f3ef4e0ea3bbe1d208dfa0489  0.1s
 => => extracting sha256:c139fe92686de21a8c49d9cb40415d1  0.0s
 => => extracting sha256:d322f1bf41d8e99dc47f550cbf85e79  0.1s
 => => extracting sha256:a670537bed7086aeb8b663aad5e09cd  0.1s
 => [nginx internal] load build context                   0.3s
 => => transferring context: 320B                         0.1s
 => [bs_app internal] load build context                  1.7s
 => => transferring context: 3.76MB                       1.5s
 => [bs_app server_builder 1/6] FROM docker.io/library/  35.7s
 => => resolve docker.io/library/python:3.14.4-slim@sha2  0.2s
 => => sha256:765e5bc6b73e0d0f223f9d3c 12.27MB / 12.27MB  6.6s
 => => sha256:74cf12ae9d2089e998920c83152f6f 250B / 250B  1.8s
 => => sha256:6ab80c0b8b94e86367b7670f7b 1.29MB / 1.29MB  3.9s
 => => sha256:57fb71246055257a374deb7 29.78MB / 29.78MB  25.1s
 => => extracting sha256:57fb71246055257a374deb7564ceca1  6.0s
 => => extracting sha256:6ab80c0b8b94e86367b7670f7b513ef  0.6s
 => => extracting sha256:765e5bc6b73e0d0f223f9d3c00a2319  2.4s
 => => extracting sha256:74cf12ae9d2089e998920c83152f6fd  0.1s
 => [bs_app server_builder 2/6] WORKDIR /home             1.3s
 => [bs_app server_builder 3/6] RUN echo "APT::Get::Assu  1.0s
 => [bs_app server_builder 4/6] RUN apt-get update &&   167.3s
 => [nginx 2/3] RUN rm /etc/nginx/conf.d/default.conf     1.0s
 => [nginx 3/3] COPY nginx.conf /etc/nginx/nginx.conf     0.2s
 => [nginx] exporting to image                            6.5s
 => => exporting layers                                   0.4s
 => => exporting manifest sha256:af4319cf20d23d26f17c234  0.0s
 => => exporting config sha256:9c7d6b608d6034cabf801e86d  0.1s
 => => exporting attestation manifest sha256:58d52f6e0a1  0.1s
 => => exporting manifest list sha256:ef1967fd73d0bc2dca  0.1s
 => => naming to docker.io/library/load-balancing-nginx:  0.0s
 => => unpacking to docker.io/library/load-balancing-ngi  5.6s
 => [nginx] resolving provenance for metadata file        0.1s
 => [bs_app server_builder 5/6] COPY . /home              0.5s
 => [bs_app server_builder 6/6] RUN python -m venv venv  88.5s
 => [bs_app stage-1 3/5] COPY --from=server_builder /hom  2.7s
 => [bs_app stage-1 4/5] RUN echo "APT::Get::Assume-Yes   0.8s
 => [bs_app stage-1 5/5] RUN apt-get update && apt-get   51.1s
 => [bs_app] exporting to image                          28.0s
 => => exporting layers                                  13.8s
 => => exporting manifest sha256:a8c044a076f5b3f5be6e715  0.1s
 => => exporting config sha256:e8e1224f906c52d006395e9ce  0.1s
 => => exporting attestation manifest sha256:d4d47e51306  0.1s
 => => exporting manifest list sha256:eb7dd5d120a1978311  0.1s
 => => naming to docker.io/library/load-balancing-bs_app  0.0s
 => => unpacking to docker.io/library/load-balancing-bs  13.7s
 => [bs_app] resolving provenance for metadata file       0.1s
[+] up 6/6
 ✔ Image load-balancing-nginx        Built               389.8s
 ✔ Image load-balancing-bs_app       Built               389.7s
 ✔ Network load-balancing_default    Created               0.1s
 ✔ Container load-balancing-bs_app-2 Started               3.4s
 ✔ Container load-balancing-bs_app-1 Started               4.4s
 ✔ Container nginx                   Started               3.5s
```
**Pembahasan :**

Perintah docker compose up --build -d --scale bs_app=2 digunakan untuk membangun image sekaligus menjalankan layanan dalam mode background. Opsi --scale bs_app=2 membuat dua instance aplikasi Blacksheep berjalan secara bersamaan. Berdasarkan output, proses build berhasil diselesaikan dan seluruh container berhasil dijalankan. Hal ini menunjukkan bahwa konfigurasi Docker Compose, Dockerfile aplikasi, dan Nginx dapat digunakan dengan baik.<br></br>


**4.1.6 Memeriksa Status Container**
Perintah yang digunakan:
```
PS C:\Users\user\workspace-05\src\load-balancing> docker ps
```
```
CONTAINER ID   IMAGE                   COMMAND                  CREATED         STATUS         PORTS                                 NAMES
ab9955651ec9   load-balancing-nginx    "/docker-entrypoint.…"   7 minutes ago   Up 7 minutes   0.0.0.0:80->80/tcp, [::]:80->80/tcp   nginx
93ae9d436648   load-balancing-bs_app   "sh -c '. venv/bin/a…"   7 minutes ago   Up 7 minutes                                         load-balancing-bs_app-2
dded2530e646   load-balancing-bs_app   "sh -c '. venv/bin/a…"   7 minutes ago   Up 7 minutes                                         load-balancing-bs_app-1
```
**Pembahasan :**

Perintah docker ps digunakan untuk melihat container yang sedang berjalan. Hasilnya memperlihatkan tiga container dengan status Up, yaitu satu container Nginx dan dua container aplikasi Blacksheep. Nginx menggunakan port 80 sebagai jalur akses dari komputer, sedangkan dua instance aplikasi berjalan di dalam jaringan Docker. Hasil ini menunjukkan bahwa layanan Load Balancing berhasil dijalankan sesuai konfigurasi.<br></br>


**4.1.7 Mengakses Aplikasi Melalui Browser**
Alamat yang digunakan:
```
http://localhost
```

<img width="685" height="585" alt="image" src="https://github.com/user-attachments/assets/b97c796f-a762-47ac-a9e0-59d04a4e4057" />

**Pembahasan :**

Aplikasi Blacksheep dapat diakses melalui browser menggunakan alamat http://localhost. Hal ini menunjukkan bahwa permintaan dari komputer dapat diterima oleh Nginx melalui port 80 dan diteruskan menuju layanan aplikasi. Dengan menggunakan Nginx sebagai reverse proxy, pengguna tidak perlu mengakses masing-masing instance aplikasi secara langsung.<br></br>


**4.1.8 Menguji Distribusi Permintaan**

Untuk menguji distribusi permintaan, halaman http://localhost dibuka dan dilakukan refresh beberapa kali. Setelah itu, log dari kedua instance aplikasi diperiksa menggunakan perintah.

Log aplikasi pertama:
```
PS C:\Users\user\workspace-05\src\load-balancing> docker logs load-balancing-bs_app-1
```
```
INFO:     Started server process [8]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://0.0.0.0:80 (Press CTRL+C to quit)
INFO:     172.18.0.4:60736 - "GET /vendor/fontawesome-free/css/all.min.css HTTP/1.1" 200 OK
INFO:     172.18.0.4:60736 - "GET /images/blacksheep.png HTTP/1.1" 200 OK
INFO:     172.18.0.4:60736 - "GET /vendor/jquery-easing/jquery.easing.js HTTP/1.1" 200 OK
INFO:     172.18.0.4:60736 - "GET /scripts/freelancer.js HTTP/1.1" 200 OK
INFO:     172.18.0.4:60736 - "GET /vendor/fontawesome-free/webfonts/fa-brands-400.woff2 HTTP/1.1" 200 OK
INFO:     172.18.0.4:49168 - "GET / HTTP/1.1" 200 OK
INFO:     172.18.0.4:49168 - "GET / HTTP/1.1" 200 OK
INFO:     172.18.0.4:49168 - "GET / HTTP/1.1" 200 OK
INFO:     172.18.0.4:49168 - "GET / HTTP/1.1" 200 OK
INFO:     172.18.0.4:49168 - "GET / HTTP/1.1" 200 OK
INFO:     172.18.0.4:54956 - "GET / HTTP/1.1" 200 OK
INFO:     172.18.0.4:54956 - "GET / HTTP/1.1" 200 OK
INFO:     172.18.0.4:54956 - "GET / HTTP/1.1" 200 OK
INFO:     172.18.0.4:54956 - "GET / HTTP/1.1" 200 OK
INFO:     172.18.0.4:54956 - "GET / HTTP/1.1" 200 OK
INFO:     172.18.0.4:54956 - "GET / HTTP/1.1" 200 OK
INFO:     172.18.0.4:54956 - "GET / HTTP/1.1" 200 OK
INFO:     172.18.0.4:54956 - "GET / HTTP/1.1" 200 OK
```

Log aplikasi kedua
```
PS C:\Users\user\workspace-05\src\load-balancing> docker logs load-balancing-bs_app-2
```
```
INFO:     Started server process [8]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://0.0.0.0:80 (Press CTRL+C to quit)
INFO:     172.18.0.4:56224 - "GET / HTTP/1.1" 200 OK
INFO:     172.18.0.4:56224 - "GET /styles/public.css HTTP/1.1" 200 OK
INFO:     172.18.0.4:56224 - "GET /vendor/jquery/jquery.min.js HTTP/1.1" 200 OK
INFO:     172.18.0.4:56224 - "GET /vendor/bootstrap/js/bootstrap.bundle.min.js HTTP/1.1" 200 OK
INFO:     172.18.0.4:56224 - "GET /vendor/fontawesome-free/webfonts/fa-solid-900.woff2 HTTP/1.1" 200 OK
INFO:     172.18.0.4:56224 - "GET /blacksheep-favicon-white.png HTTP/1.1" 200 OK
INFO:     172.18.0.4:42842 - "GET / HTTP/1.1" 200 OK
INFO:     172.18.0.4:42842 - "GET / HTTP/1.1" 200 OK
INFO:     172.18.0.4:42842 - "GET / HTTP/1.1" 200 OK
INFO:     172.18.0.4:42842 - "GET / HTTP/1.1" 200 OK
INFO:     172.18.0.4:42842 - "GET / HTTP/1.1" 200 OK
INFO:     172.18.0.4:47510 - "GET / HTTP/1.1" 200 OK
```
**Pembahasan :**

Berdasarkan hasil pemeriksaan log, kedua instance aplikasi berhasil menerima permintaan HTTP dan memberikan respons 200 OK. Hal ini menunjukkan bahwa Nginx dapat meneruskan permintaan menuju kedua instance aplikasi yang dijalankan menggunakan Docker Compose. Dengan demikian, penerapan Load Balancing berhasil dilakukan karena layanan tidak hanya dijalankan melalui satu instance aplikasi.<br></br>



<h4>Heartbeat</h4>

**4.2.1 Program heartbeat**

File yang digunakan adalah:
```
PS C:\Users\user\workspace-05\src\load-balancing> Get-Content "$HOME\workspace-05\src\heartbeat\check-server.py"
```
```
# taken from:
# https://stackoverflow.com/questions/20867184/how-to-check-whether-my-server-is-up-and-running-using-python

import sys
import socket

def servertest(argv):
    host = argv[1]
    port = int(argv[2])

    args = socket.getaddrinfo(host, port, socket.AF_INET, socket.SOCK_STREAM)
    for family, socktype, proto, canonname, sockaddr in args:
        s = socket.socket(family, socktype, proto)
        try:
            s.connect(sockaddr)
        except socket.error:
            return False
        else:
            s.close()
            return True


if __name__ == "__main__":
    if servertest(sys.argv):
        print("Server up")
    else:
        print("Server down")
```
**Pembahasan :**

Program Heartbeat menggunakan library socket untuk memeriksa apakah server dapat menerima koneksi melalui alamat dan port tertentu. Variabel host digunakan untuk menyimpan alamat server, sedangkan port menyimpan nomor port yang akan diperiksa. Fungsi socket.getaddrinfo() mengambil informasi alamat koneksi, kemudian s.connect() mencoba menghubungkan program ke server. Jika koneksi gagal, fungsi mengembalikan nilai False; jika berhasil, fungsi mengembalikan nilai True.<br></br>


**4.2.2 Pengujian Pertama – Server Tidak Aktif**

Perintah yang digunakan:
```
& "$HOME\workspace-05\.venv\Scripts\python.exe" "$HOME\workspace-05\src\heartbeat\check-server.py" localhost 44777
```
**Output :**
```
Server down
```
**Pembahasan :**

Pada pengujian pertama, aplikasi Blacksheep belum dijalankan sehingga tidak ada layanan yang menerima koneksi pada port 44777. Ketika program Heartbeat mencoba menghubungkan diri ke localhost:44777, koneksi gagal dan program menampilkan Server down. Hasil tersebut menunjukkan bahwa mekanisme Heartbeat dapat mendeteksi kondisi server yang tidak aktif melalui pemeriksaan koneksi.<br></br>


**4.2.3 Pengujian Kedua – Server Aktif**

Aplikasi Blacksheep dijalankan menggunakan perintah berikut:
```
cd "$HOME\workspace-05\src\load-balancing\blacksheep_lb"

& "$HOME\workspace-05\.venv\Scripts\python.exe" dev.py
```
**Output saat server dijalankan :**
```
Running for local development
Visit http://localhost:44777/

INFO: Started server process [10896]
INFO: Waiting for application startup.
INFO: Application startup complete.
INFO: Uvicorn running on http://localhost:44777 (Press CTRL+C to quit)
```
Setelah server aktif, jalankan kembali program Heartbeat pada PowerShell lain:
```
& "$HOME\workspace-05\.venv\Scripts\python.exe" "$HOME\workspace-05\src\heartbeat\check-server.py" localhost 44777
```
**Output :**
```
Server up
```

**Pembahasan :**

Pengujian pertama – Server tidak aktif

Program Heartbeat menggunakan library socket untuk memeriksa apakah server dapat menerima koneksi melalui alamat dan port yang ditentukan. Ketika aplikasi Blacksheep tidak dijalankan, program menampilkan Server down karena koneksi ke localhost:44777 gagal dilakukan. Hasil tersebut menunjukkan bahwa program dapat mendeteksi kondisi server yang tidak aktif.

Pengujian kedua – Server aktif

Ketika aplikasi Blacksheep dijalankan menggunakan Uvicorn pada port 44777, program Heartbeat berhasil terhubung ke server dan menampilkan Server up. Hal ini menunjukkan bahwa pemeriksaan koneksi berhasil dilakukan ketika layanan tersedia. Dengan demikian, mekanisme Heartbeat dapat digunakan untuk mengetahui apakah server dapat dijangkau atau tidak.<br></br>


<h4>4.3 Retry</h4>

**4.3.1 Program Retry**

File yang digunakan adalah:
```
src\failure-detection\check-retry.py"
```
```
from tenacity import retry, stop_after_attempt, wait_exponential, retry_if_exception_type

import requests

@retry(
    stop=stop_after_attempt(5),  # Stop after 5 attempts
    wait=wait_exponential(multiplier=1, min=4, max=10), # Exponential backoff with min 4s, max 10s
    retry=retry_if_exception_type(requests.exceptions.ConnectionError) # Retry only on connection errors
)

def make_http_request(url):
    print(f"Attempting to access {url}...")
    response = requests.get(url)
    response.raise_for_status()  # Raise an exception for bad status codes (4xx or 5xx)
    print(f"Successfully accessed {url}")
    return response.text

try:
    content = make_http_request("http://localhost:44777")
    # Process the content
except Exception as e:
    print(f"Failed to access URL after multiple retries: {e}")
```

**Pembahasan :**

Program Retry menggunakan library Tenacity dan Requests untuk mengatur pengulangan permintaan HTTP. Pengaturan stop_after_attempt(5) membatasi jumlah percobaan maksimal sebanyak lima kali. Fungsi wait_exponential() memberikan jeda bertahap dengan waktu minimum empat detik dan maksimum sepuluh detik. Sementara itu, retry_if_exception_type() menentukan bahwa pengulangan dilakukan ketika terjadi ConnectionError. Permintaan HTTP dikirim menggunakan requests.get(), sedangkan response.raise_for_status() digunakan untuk memeriksa apakah respons HTTP menunjukkan kesalahan.<br></br>


**4.3.2 Pengujian Pertama – Server Aktif**

Pastikan aplikasi Blacksheep berjalan pada port 44777. Kemudian jalankan program Retry melalui PowerShell lain:
```
& "$HOME\workspace-05\.venv\Scripts\python.exe" "$HOME\workspace-05\src\failure-detection\check-retry.py"
```
**Output :**
```
Attempting to access http://localhost:44777...
Successfully accessed http://localhost:44777
```

**Pembahasan :**

Pada pengujian ketika server aktif, program berhasil mengirim permintaan HTTP ke alamat localhost:44777. Output Successfully accessed menunjukkan bahwa server memberikan respons yang berhasil diterima oleh program. Mekanisme Retry tidak perlu melakukan pengulangan karena permintaan pertama sudah berhasil. Hal ini memperlihatkan bahwa Retry tetap dapat menjalankan permintaan secara normal ketika layanan tersedia.<br></br>


**4.3.3 Pengujian Kedua – Server Tidak Aktif**

Pastikan aplikasi Blacksheep telah dihentikan dengan menekan CTRL + C pada terminal yang menjalankan dev.py. Setelah itu, jalankan kembali program Retry:
```
& "$HOME\workspace-05\.venv\Scripts\python.exe" "$HOME\workspace-05\src\failure-detection\check-retry.py"
```
**Output :**
```
Attempting to access http://localhost:44777...
Attempting to access http://localhost:44777...
Attempting to access http://localhost:44777...
Attempting to access http://localhost:44777...
Attempting to access http://localhost:44777...

Failed to access URL after multiple retries: RetryError[<Future at 0x2ce20d25480 state=finished raised ConnectionError>]
```

**Pembahasan :**

Pada pengujian ketika server tidak aktif, program melakukan lima kali percobaan untuk mengakses alamat localhost:44777. Setiap percobaan mengalami ConnectionError karena tidak ada server yang menerima koneksi pada port tersebut. Library Tenacity kemudian menjalankan mekanisme retry dengan jeda bertahap hingga mencapai batas percobaan yang ditentukan. Setelah seluruh percobaan gagal, program menampilkan pesan Failed to access URL after multiple retries. Hasil ini menunjukkan bahwa mekanisme Retry dapat memberikan kesempatan kepada sistem untuk mencoba kembali permintaan sebelum menyatakan proses gagal.<br></br>


<h4>4.4 Circuit breaker</h4>

**4.4.1 Program Circuit Breaker**

File yang digunakan adalah src/failure-detection/check-circuit-breaker.py. Untuk memperlihatkan perubahan kondisi Circuit Breaker saat server tidak aktif, bagian akhir program menggunakan perulangan sebanyak lima kali. Program ini menggunakan Python.
```
import pybreaker
import requests

breaker = pybreaker.CircuitBreaker(fail_max=4, reset_timeout=20)

@breaker
def make_http_request(url):
    print(f"Attempting to access {url}...")
    response = requests.get(url)
    response.raise_for_status()
    print(f"Successfully accessed {url}")
    return response.text

for i in range(5):
    try:
        content = make_http_request("http://localhost:44777")
    except Exception as e:
        print(e)
```
**Pembahasan :**

Program Circuit Breaker menggunakan library pybreaker untuk menghentikan sementara permintaan ketika server mengalami kegagalan berulang. Pengaturan fail_max=4 menentukan batas kegagalan sebanyak empat kali, sedangkan reset_timeout=20 menentukan waktu tunggu selama 20 detik sebelum sistem dapat mencoba kembali. Dekorator @breaker menerapkan mekanisme Circuit Breaker pada fungsi make_http_request(). Perulangan for i in range(5) digunakan agar program melakukan lima kali percobaan sehingga perubahan kondisi Circuit Breaker dapat terlihat ketika server tidak aktif.

Kondisi Circuit Breaker:
Kondisi:      Penjelasan
Closed:       Permintaan diizinkan seperti biasa selama belum mencapai batas kegagalan.
Open:         Permintaan dihentikan sementara setelah batas kegagalan tercapai.
Half-open:    Setelah waktu tunggu, sistem mengizinkan percobaan untuk memeriksa apakah server sudah pulih.
<br></br>


**4.4.2 Pengujian Pertama – Server Aktif**

Jalankan aplikasi Blacksheep:
```
cd "$HOME\workspace-05\src\load-balancing\blacksheep_lb"

& "$HOME\workspace-05\.venv\Scripts\python.exe" dev.py
```
**Output saat server dijalankan:**
```
Running for local development
Visit http://localhost:44777/

INFO: Started server process [10952]
INFO: Waiting for application startup.
INFO: Application startup complete.
INFO: Uvicorn running on http://localhost:44777 (Press CTRL+C to quit)
```

Setelah server aktif, buka PowerShell lain dan jalankan program Circuit Breaker:
```
& "$HOME\workspace-05\.venv\Scripts\python.exe" "$HOME\workspace-05\src\failure-detection\check-circuit-breaker.py"
```
**Output :**
```
Attempting to access http://localhost:44777...
Successfully accessed http://localhost:44777
```
**Pembahasan :**

ada pengujian ketika server aktif, program berhasil mengirim permintaan HTTP dan memperoleh respons dari aplikasi Blacksheep. Output Successfully accessed menunjukkan bahwa layanan dapat diakses tanpa mengalami kegagalan koneksi. Karena permintaan berhasil dilakukan, Circuit Breaker tetap berada dalam kondisi Closed sehingga permintaan berikutnya masih dapat diproses seperti biasa.<br></br>


**4.4.3 Pengujian Kedua – Server Tidak Aktif**

Pertama, hentikan aplikasi Blacksheep dengan menekan CTRL + C pada terminal yang menjalankan dev.py. Kemudian jalankan program Circuit Breaker yang sudah menggunakan perulangan lima kali.

Perintah yang digunakan:
```
& "$HOME\workspace-05\.venv\Scripts\python.exe" "$HOME\workspace-05\src\failure-detection\check-circuit-breaker.py"
```
**Output :**
```
Attempting to access http://localhost:44777...

HTTPConnectionPool(host='localhost', port=44777): Max retries exceeded with url: / (Caused by NewConnectionError("HTTPConnection(host='localhost', port=44777): Failed to establish a new connection: [WinError 10061] No connection could be made because the target machine actively refused it"))

Attempting to access http://localhost:44777...

HTTPConnectionPool(host='localhost', port=44777): Max retries exceeded with url: / (Caused by NewConnectionError("HTTPConnection(host='localhost', port=44777): Failed to establish a new connection: [WinError 10061] No connection could be made because the target machine actively refused it"))

Attempting to access http://localhost:44777...

HTTPConnectionPool(host='localhost', port=44777): Max retries exceeded with url: / (Caused by NewConnectionError("HTTPConnection(host='localhost', port=44777): Failed to establish a new connection: [WinError 10061] No connection could be made because the target machine actively refused it"))

Attempting to access http://localhost:44777...

Failures threshold reached, circuit breaker opened

Timeout not elapsed yet, circuit breaker still open
```

**Analisis Output :**




**Pembahasan :**













