<h3>KONSISTENSI DAN REPLIKASI PADA SISTEM TERDISTRIBUSI</h3><br></br>

**TUJUAN PRAKTIKUM**

•	Memahami streaming replication PostgreSQL dengan satu primary dan satu replica.

•	Memverifikasi bahwa perubahan pada primary direplikasi dan dapat dibaca pada replica.

•	Menguji konsep promosi replica sebagai primary ketika primary awal tidak tersedia.

•	Membangun cluster Apache Ignite tiga node dan menguji penyebaran metadata/tabel melalui cluster.
<br></br>

<h2>4.1 Streaming Replication menggunakan PostgreSQL</h2>

**4.1.1 Persiapan Docker dan container PostgreSQL**
```bash
PS C:\Users\user> docker --version
Docker version 29.9.2, build a82e528
PS C:\Users\user> docker compose version
Docker Compose version v5.6.1
PS C:\Users\user>
```
```bash
docker compose ps

NAME                                       IMAGE                      COMMAND                  SERVICE            CREATED              STATUS                        PORTS
streaming-replication-postgres_primary-1   postgres:18.3-alpine3.23   "docker-entrypoint.s…"   postgres_primary   About a minute ago   Up About a minute (healthy)   0.0.0.0:5432->5432/tcp, [::]:5432->5432/tcp
streaming-replication-postgres_replica-1   postgres:18.3-alpine3.23   "docker-entrypoint.s…"   postgres_replica   About a minute ago   Up About a minute (healthy)   0.0.0.0:5433->5432/tcp, [::]:5433->5432/tcp
```
**Pembahasan :**

Pada tahap persiapan, Docker Desktop digunakan untuk menyediakan lingkungan yang dibutuhkan dalam menjalankan layanan sistem terdistribusi tanpa harus memasang setiap layanan secara manual pada Windows. Pemeriksaan versi Docker dan Docker Compose dilakukan untuk memastikan bahwa perintah yang diperlukan tersedia sebelum praktikum dimulai. Docker Compose nantinya membantu menjalankan beberapa layanan yang saling berhubungan melalui konfigurasi yang telah disediakan.<br></br>


**4.1.2 Pemeriksaan status primary dan replica**
```bash
docker compose exec postgres_primary psql -U zuser -d zdb -c "SELECT pg_is_in_recovery();"
 pg_is_in_recovery
-------------------
 f
(1 row)

docker compose exec postgres_replica psql -U zuser -d zdb -c "SELECT pg_is_in_recovery();"
 pg_is_in_recovery
-------------------
 t
(1 row)
```
**Pembahasan :**

Pada praktikum ini, PostgreSQL dijalankan menggunakan dua container, yaitu primary dan replica. Pemeriksaan dilakukan menggunakan fungsi pg_is_in_recovery() untuk mengetahui peran masing-masing server. Primary menghasilkan nilai f, yang menunjukkan bahwa server tidak berada dalam mode recovery, sedangkan replica menghasilkan nilai t, yang menunjukkan bahwa server masih berada dalam mode recovery sebagai standby. Hasil ini sesuai dengan peran kedua server sebelum dilakukan promosi replica.<br></br>


**4.1.3 Pengujian replikasi data**
```bash
docker compose exec postgres_primary psql -U zuser -d zdb -c "INSERT INTO uji_replikasi (nama) VALUES ('Andi'), ('Budi'), ('Citra');"
INSERT 0 3

*Primary
docker compose exec postgres_primary psql -U zuser -d zdb -c "SELECT * FROM uji_replikasi;"
 id | nama
----+-------
  1 | Andi
  2 | Budi
  3 | Citra
(3 rows)

*Replica
docker compose exec postgres_replica psql -U zuser -d zdb -c "SELECT * FROM uji_replikasi;"
 id | nama
----+-------
  1 | Andi
  2 | Budi
  3 | Citra
(3 rows)

```
**pembahasan :**

Pengujian replikasi dilakukan dengan memasukkan data melalui primary, kemudian membaca data tersebut dari replica menggunakan perintah SELECT. PostgreSQL menggunakan mekanisme Write-Ahead Logging (WAL) untuk meneruskan perubahan data dari primary ke replica. Ketika data yang sama dapat dibaca dari kedua server, hal tersebut menjadi bukti bahwa proses streaming replication bekerja sesuai konfigurasi. Operasi penulisan dilakukan melalui primary sebelum promosi replica.<br></br>


**4.1.4 Pemeriksaan koneksi replikasi**
```bash
docker compose exec postgres_primary psql -U zuser -d zdb -c "SELECT application_name, state, sync_state FROM pg_stat_replication;"
 application_name |   state   | sync_state
------------------+-----------+------------
 walreceiver      | streaming | async
(1 row)
```
**pembahasan :**

Pemeriksaan pg_stat_replication dilakukan untuk melihat koneksi antara server primary dan replica. Informasi application_name, state, dan sync_state digunakan untuk mengetahui identitas koneksi, status pengiriman data, serta mode sinkronisasi replikasi. Jika status menunjukkan streaming, hal tersebut menandakan bahwa primary sedang mengirimkan perubahan data melalui mekanisme streaming replication.<br></br>


**4.1.5 Promosi replica menjadi primary**
```bash
docker compose exec postgres_replica psql -U zuser -d zdb -c "SELECT pg_promote();"

 pg_promote
------------
 t
(1 row)


docker compose exec postgres_replica psql -U zuser -d zdb -c "SELECT pg_is_in_recovery();"

 pg_is_in_recovery
-------------------
 f
(1 row)
```
**pembahasan :**

Pengujian ketersediaan dilakukan dengan menghentikan container primary, kemudian menjalankan fungsi pg_promote() pada replica. Perintah tersebut menghasilkan nilai t, sedangkan pemeriksaan pg_is_in_recovery() setelah promosi menghasilkan nilai f. Perubahan status ini menunjukkan bahwa replica telah keluar dari mode recovery dan dapat berperan sebagai server primary. Pengujian ini merupakan promosi manual, sehingga belum membuktikan adanya mekanisme failover otomatis.<br></br>


<h2>4.2 Replikasi Master-Master Menggunakan Apache Ignite</h2>

**4.2.1 Menjalankan tiga node ignite**
```bash
docker compose config

WARN[0000] service "node1": configs.node_config.mode: mode is not supported outside Swarm mode and will be ignored
WARN[0000] service "node2": configs.node_config.mode: mode is not supported outside Swarm mode and will be ignored
WARN[0000] service "node3": configs.node_config.mode: mode is not supported outside Swarm mode and will be ignored
name: ignite3
services:
  node1:
    command:
      - --node-name
      - node1
    configs:
      - source: node_config
        target: /opt/ignite/etc/ignite-config.conf
        mode: "0444"
    environment:
      JVM_MAX_MEM: 2g
      JVM_MIN_MEM: 512m
    image: apacheignite/ignite:3.1.0
    networks:
      default: null
    ports:
      - mode: ingress
        target: 10300
        published: "10300"
        protocol: tcp
      - mode: ingress
        target: 10800
        published: "10800"
        protocol: tcp
  node2:
    command:
      - --node-name
      - node2
    configs:
      - source: node_config
        target: /opt/ignite/etc/ignite-config.conf
        mode: "0444"
    environment:
      JVM_MAX_MEM: 2g
      JVM_MIN_MEM: 512m
    image: apacheignite/ignite:3.1.0
    networks:
      default: null
    ports:
      - mode: ingress
        target: 10300
        published: "10301"
        protocol: tcp
      - mode: ingress
        target: 10800
        published: "10801"
        protocol: tcp
  node3:
    command:
      - --node-name
      - node3
    configs:
      - source: node_config
        target: /opt/ignite/etc/ignite-config.conf
        mode: "0444"
    environment:
      JVM_MAX_MEM: 2g
      JVM_MIN_MEM: 512m
    image: apacheignite/ignite:3.1.0
    networks:
      default: null
    ports:
      - mode: ingress
        target: 10300
        published: "10302"
        protocol: tcp
      - mode: ingress
        target: 10800
        published: "10802"
        protocol: tcp
networks:
  default:
    name: ignite3_default
configs:
  node_config:
    name: ignite3_node_config
    content: |
      ignite {
        network {
          port: 3344
          nodeFinder.netClusterNodes = ["node1:3344", "node2:3344", "node3:3344"]
        }
      }
x-ignite-def:
  configs:
    - mode: 292
      source: node_config
      target: /opt/ignite/etc/ignite-config.conf
  environment:
    JVM_MAX_MEM: 2g
    JVM_MIN_MEM: 512m
  image: apacheignite/ignite:3.1.0
```
```bash
docker compose up -d

WARN[0000] service "node1": configs.node_config.mode: mode is not supported outside Swarm mode and will be ignored
WARN[0000] service "node2": configs.node_config.mode: mode is not supported outside Swarm mode and will be ignored
WARN[0000] service "node3": configs.node_config.mode: mode is not supported outside Swarm mode and will be ignored
[+] up 12/12
 ✔ Image apacheignite/ignite:3.1.0 Pulled                              67.6s
 ✔ Network ignite3_default         Created                              0.1s
 ✔ Container ignite3-node3-1       Started                              3.4s
 ✔ Container ignite3-node2-1       Started                              4.2s
 ✔ Container ignite3-node1-1       Started                              2.8s

```
```bash
docker compose ps

NAME              IMAGE                       COMMAND                  SERVICE   CREATED              STATUS          PORTS
ignite3-node1-1   apacheignite/ignite:3.1.0   "docker-entrypoint.s…"   node1     About a minute ago   Up 58 seconds   0.0.0.0:10300->10300/tcp, [::]:10300->10300/tcp, 0.0.0.0:10800->10800/tcp, [::]:10800->10800/tcp
ignite3-node2-1   apacheignite/ignite:3.1.0   "docker-entrypoint.s…"   node2     About a minute ago   Up 57 seconds   0.0.0.0:10301->10300/tcp, [::]:10301->10300/tcp, 0.0.0.0:10801->10800/tcp, [::]:10801->10800/tcp
ignite3-node3-1   apacheignite/ignite:3.1.0   "docker-entrypoint.s…"   node3     About a minute ago   Up 58 seconds   0.0.0.0:10302->10300/tcp, [::]:10302->10300/tcp, 0.0.0.0:10802->10800/tcp, [::]:10802->10800/tcp
```
**pembahasan :**

Pada tahap ini, Apache Ignite 3.1.0 dijalankan menggunakan Docker Compose dengan konfigurasi tiga node, yaitu Node 1, Node 2, dan Node 3. Perintah docker compose config digunakan untuk memeriksa konfigurasi layanan sebelum dijalankan, sedangkan docker compose up -d digunakan untuk membuat dan menjalankan ketiga container di latar belakang. Berdasarkan hasil docker compose ps, seluruh container menunjukkan status Up. Node 1 menggunakan port host 10300 dan 10800, Node 2 menggunakan port 10301 dan 10801, sedangkan Node 3 menggunakan port 10302 dan 10802. Dengan demikian, ketiga layanan berhasil dijalankan dan memiliki endpoint masing-masing untuk pengujian cluster.<br></br>


**4.2.2 Inisialisasi cluster dan pembuatan tabel**
```bash
PS C:\Users\user\Downloads\praktikum dis-dec untunk tutor modul 4\Modul4_Windows_Starter\Modul4_Windows_Starter\ignite-master-master> dir .\sql


    Directory: C:\Users\user\Downloads\praktikum dis-dec untunk tutor
    modul 4\Modul4_Windows_Starter\Modul4_Windows_Starter\ignite-master-mas
    ter\sql


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        10/29/2026   2:04 AM          24387 current_catalog.sql
-a----        10/29/2026   2:04 AM          11321 ee_and_cust.sql
-a----        10/29/2026   2:04 AM         240684 invoices.sql
-a----        10/29/2026   2:04 AM            824 media_and_genre.sql
-a----        10/29/2026   2:04 AM           3222 schema.sql
-a----        10/29/2026   2:04 AM         309577 tracks.sql


PS C:\Users\user\Downloads\praktikum dis-dec untunk tutor modul 4\Modul4_Windows_Starter\Modul4_Windows_Starter\ignite-master-master> docker run --rm -it -v "${PWD}\sql:/opt/ignite/sql" apacheignite/ignite:3.1.0 cli


           #              ___                         __
         ###             /   |   ____   ____ _ _____ / /_   ___
     #  #####           / /| |  / __ \ / __ `// ___// __ \ / _ \
   ###  ######         / ___ | / /_/ // /_/ // /__ / / / // ___/
  #####  #######      /_/  |_|/ .___/ \__,_/ \___//_/ /_/ \___/
  #######  ######            /_/
    ########  ####        ____               _  __           _____
   #  ########  ##       /  _/____ _ ____   (_)/ /_ ___     |__  /
  ####  #######  #       / / / __ `// __ \ / // __// _ \     /_ <
   #####  #####        _/ / / /_/ // / / // // /_ / ___/   ___/ /
     ####  ##         /___/ \__, //_/ /_//_/ \__/ \___/   /____/
       ##                  /____/

                      Apache Ignite CLI version 3.1.0


You appear to have not connected to any node yet. Do you want to connect to the default node http://localhost:10300? [Y/n] n
[disconnected]> connect http://host.docker.internal:10300
Connected to http://host.docker.internal:10300
```
**pembahasan**

Apache Ignite 3 dijalankan menggunakan tiga node melalui Docker Compose. Setelah cluster diinisialisasi, file SQL digunakan untuk membuat skema dan mengisi tabel seperti ARTIST, ALBUM, CUSTOMER, INVOICE, dan TRACK. Pemeriksaan daftar tabel melalui SYSTEM.TABLES menunjukkan bahwa tabel-tabel tersebut berhasil dibuat. Penggunaan beberapa file SQL memudahkan proses pembuatan struktur database dan pemuatan data sebelum pengujian dilakukan.<br></br>


**4.2.3 Memuat data dan memeriksa tabel**
```bash
[node1]> sql --file=/opt/ignite/sql/schema.sql
Updated 0 rows.
Updated 0 rows.
Updated 0 rows.
Updated 0 rows.
Updated 0 rows.
Updated 0 rows.
Updated 0 rows.
Updated 0 rows.
Updated 0 rows.
Updated 0 rows.
Updated 0 rows.
Updated 0 rows.
Updated 0 rows.
Updated 0 rows.
Updated 0 rows.
Updated 0 rows.
Updated 0 rows.
Updated 0 rows.
Updated 0 rows.
Updated 0 rows.

[node1]> sql --file=/opt/ignite/sql/current_catalog.sql
Updated 275 rows.
Updated 347 rows.

[node1]> sql --file=/opt/ignite/sql/ee_and_cust.sql
Updated 8 rows.
Updated 59 rows.

[node1]> sql --file=/opt/ignite/sql/invoices.sql
Updated 412 rows.
Updated 1000 rows.
Updated 1000 rows.
Updated 240 rows.
Updated 18 rows.
Updated 1000 rows.
Updated 1000 rows.
Updated 1000 rows.
Updated 1000 rows.
Updated 1000 rows.
Updated 1000 rows.
Updated 1000 rows.
Updated 1000 rows.
Updated 715 rows.

[node1]> sql --file=/opt/ignite/sql/media_and_genre.sql
Updated 25 rows.
Updated 5 rows.

[node1]> sql --file=/opt/ignite/sql/tracks.sql
Updated 1000 rows.
Updated 1000 rows.
Updated 1000 rows.
Updated 503 rows.

[node1]> sql
```
```bash
sql-cli> SELECT SCHEMA, NAME
> FROM SYSTEM.TABLES;
╔════════╤═══════════════╗
║ SCHEMA │ NAME          ║
╠════════╪═══════════════╣
║ PUBLIC │ ALBUM         ║
╟────────┼───────────────╢
║ PUBLIC │ PLAYLISTTRACK ║
╟────────┼───────────────╢
║ PUBLIC │ TRACK         ║
╟────────┼───────────────╢
║ PUBLIC │ CUSTOMER      ║
╟────────┼───────────────╢
║ PUBLIC │ MEDIATYPE     ║
╟────────┼───────────────╢
║ PUBLIC │ PLAYLIST      ║
╟────────┼───────────────╢
║ PUBLIC │ ARTIST        ║
╟────────┼───────────────╢
║ PUBLIC │ INVOICELINE   ║
╟────────┼───────────────╢
║ PUBLIC │ EMPLOYEE      ║
╟────────┼───────────────╢
║ PUBLIC │ GENRE         ║
╟────────┼───────────────╢
║ PUBLIC │ INVOICE       ║
╚════════╧═══════════════╝
```
**pembahasan**

Pada tahap ini, file SQL dijalankan melalui CLI Apache Ignite untuk membuat struktur database dan memasukkan data ke dalam tabel. File schema.sql digunakan untuk membuat skema dan struktur tabel, sedangkan current_catalog.sql, ee_and_cust.sql, invoices.sql, media_and_genre.sql, dan tracks.sql digunakan untuk memuat data sesuai kelompoknya. Output berupa Updated ... rows. menunjukkan bahwa perintah SQL berhasil memproses sejumlah baris pada masing-masing file. Setelah proses pemuatan selesai, pemeriksaan melalui SYSTEM.TABLES menampilkan tabel seperti ARTIST, ALBUM, CUSTOMER, INVOICE, INVOICELINE, TRACK, GENRE, dan tabel lainnya. Hasil ini menunjukkan bahwa struktur tabel berhasil dibuat dan data berhasil dimuat sehingga dapat digunakan untuk pengujian query berikutnya.<br></br>


**4.2.4 Verifikasi Node 1, Node 2, dan Node 3**
```bash
sql-cli> SELECT * FROM PUBLIC.ARTIST LIMIT 5;
╔══════════╤═══════════════════════════════════════════╗
║ ARTISTID │ NAME                                      ║
╠══════════╪═══════════════════════════════════════════╣
║ 149      │ Lost                                      ║
╟──────────┼───────────────────────────────────────────╢
║ 247      │ The King's Singers                        ║
╟──────────┼───────────────────────────────────────────╢
║ 64       │ Santana Feat. The Project G&B             ║
╟──────────┼───────────────────────────────────────────╢
║ 198      │ Habib Koité and Bamada                    ║
╟──────────┼───────────────────────────────────────────╢
║ 233      │ Chicago Symphony Orchestra & Fritz Reiner ║
╚══════════╧═══════════════════════════════════════════╝

sql-cli>
sql-cli> SELECT COUNT(*) AS TOTAL_ARTIST
> FROM PUBLIC.ARTIST;
╔══════════════╗
║ TOTAL_ARTIST ║
╠══════════════╣
║ 275          ║
╚══════════════╝

sql-cli> exit;
[node1]> connect http://host.docker.internal:10301
You are already connected to the http://host.docker.internal:10300, do you want to connect to the http://host.docker.internal:10301? [Y/n] Y
Connected to http://host.docker.internal:10301
[node2]> sql
sql-cli> SELECT SCHEMA, NAME FROM SYSTEM.TABLES;
╔════════╤═══════════════╗
║ SCHEMA │ NAME          ║
╠════════╪═══════════════╣
║ PUBLIC │ ALBUM         ║
╟────────┼───────────────╢
║ PUBLIC │ PLAYLISTTRACK ║
╟────────┼───────────────╢
║ PUBLIC │ TRACK         ║
╟────────┼───────────────╢
║ PUBLIC │ CUSTOMER      ║
╟────────┼───────────────╢
║ PUBLIC │ MEDIATYPE     ║
╟────────┼───────────────╢
║ PUBLIC │ PLAYLIST      ║
╟────────┼───────────────╢
║ PUBLIC │ ARTIST        ║
╟────────┼───────────────╢
║ PUBLIC │ INVOICELINE   ║
╟────────┼───────────────╢
║ PUBLIC │ EMPLOYEE      ║
╟────────┼───────────────╢
║ PUBLIC │ GENRE         ║
╟────────┼───────────────╢
║ PUBLIC │ INVOICE       ║
╚════════╧═══════════════╝

sql-cli> SELECT COUNT(*) AS TOTAL_ARTIST FROM PUBLIC.ARTIST;
╔══════════════╗
║ TOTAL_ARTIST ║
╠══════════════╣
║ 275          ║
╚══════════════╝

sql-cli> exit;
[node2]> connect http://host.docker.internal:10302
You are already connected to the http://host.docker.internal:10301, do you want to connect to the http://host.docker.internal:10302? [Y/n] Y
Connected to http://host.docker.internal:10302
[node3]> sql
sql-cli> SELECT COUNT(*) AS TOTAL_ARTIST FROM PUBLIC.ARTIST;
╔══════════════╗
║ TOTAL_ARTIST ║
╠══════════════╣
║ 275          ║
╚══════════════╝

```
**pembahasan**

Verifikasi dilakukan dengan menjalankan query SELECT COUNT(*) AS TOTAL_ARTIST FROM PUBLIC.ARTIST; melalui endpoint ketiga node. Hasil pengujian pada Node 1, Node 2, dan Node 3 masing-masing menunjukkan 275 baris pada tabel ARTIST. Kesamaan jumlah tersebut menunjukkan bahwa tabel dapat diakses dengan hasil yang konsisten melalui ketiga endpoint yang diuji. Pengujian ini mendukung pemahaman mengenai penggunaan cluster Apache Ignite, tetapi pemeriksaan jumlah baris saja belum membuktikan seluruh kemungkinan perubahan data telah direplikasi.<br></br>


**4.2.5 Analisis penjualan bulanan**
```bash
sql-cli> SELECT
>     CAST(EXTRACT(YEAR FROM i.InvoiceDate) AS VARCHAR) || '-' ||
>     CASE
>         WHEN EXTRACT(MONTH FROM i.InvoiceDate) < 10
>         THEN '0' || CAST(EXTRACT(MONTH FROM i.InvoiceDate) AS VARCHAR)
>         ELSE CAST(EXTRACT(MONTH FROM i.InvoiceDate) AS VARCHAR)
>     END AS YearMonth,
>     COUNT(DISTINCT i.InvoiceId) AS InvoiceCount,
>     COUNT(DISTINCT i.CustomerId) AS CustomerCount,
>     SUM(i.Total) AS MonthlyRevenue,
>     AVG(i.Total) AS AverageOrderValue
> FROM Invoice i
> GROUP BY
>     EXTRACT(YEAR FROM i.InvoiceDate),
>     EXTRACT(MONTH FROM i.InvoiceDate)
> ORDER BY YearMonth DESC;
╔═══════════╤══════════════╤═══════════════╤════════════════╤════════════════════╗
║ YEARMONTH │ INVOICECOUNT │ CUSTOMERCOUNT │ MONTHLYREVENUE │ AVERAGEORDERVALUE  ║
╠═══════════╪══════════════╪═══════════════╪════════════════╪════════════════════╣
║ 2024-12   │ 7            │ 7             │ 38.62          │ 5.5171428571428571 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2024-11   │ 7            │ 7             │ 49.62          │ 7.0885714285714285 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2024-10   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2024-09   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2024-08   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2024-07   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2024-06   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2024-05   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2024-04   │ 5            │ 5             │ 33.66          │ 6.7320000000000000 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2024-03   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2024-02   │ 5            │ 5             │ 27.72          │ 5.5440000000000000 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2024-01   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2023-12   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2023-11   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2023-10   │ 7            │ 7             │ 42.62          │ 6.0885714285714285 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2023-09   │ 6            │ 6             │ 46.71          │ 7.7850000000000000 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2023-08   │ 7            │ 7             │ 47.62          │ 6.8028571428571428 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2023-07   │ 7            │ 7             │ 39.62          │ 5.6600000000000000 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2023-06   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2023-05   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2023-04   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2023-03   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2023-02   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2023-01   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2022-12   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2022-11   │ 6            │ 6             │ 23.76          │ 3.9600000000000000 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2022-10   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2022-09   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2022-08   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2022-07   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2022-06   │ 7            │ 7             │ 50.62          │ 7.2314285714285714 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2022-05   │ 7            │ 7             │ 42.62          │ 6.0885714285714285 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2022-04   │ 7            │ 7             │ 51.62          │ 7.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2022-03   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2022-02   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2022-01   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2021-12   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2021-11   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2021-10   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2021-09   │ 6            │ 6             │ 36.63          │ 6.1050000000000000 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2021-08   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2021-07   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2021-06   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2021-05   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2021-04   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2021-03   │ 7            │ 7             │ 44.62          │ 6.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2021-02   │ 7            │ 7             │ 46.62          │ 6.6600000000000000 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2021-01   │ 7            │ 7             │ 52.62          │ 7.5171428571428571 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2020-12   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2020-11   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2020-10   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2020-09   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2020-08   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2020-07   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2020-06   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2020-05   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2020-04   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2020-03   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2020-02   │ 7            │ 7             │ 37.62          │ 5.3742857142857142 ║
╟───────────┼──────────────┼───────────────┼────────────────┼────────────────────╢
║ 2020-01   │ 6            │ 6             │ 35.64          │ 5.9400000000000000 ║
╚═══════════╧══════════════╧═══════════════╧════════════════╧════════════════════╝
```
**pembahasan**

Query analisis digunakan untuk merangkum transaksi berdasarkan tahun dan bulan. Fungsi COUNT(DISTINCT ...) menghitung jumlah invoice dan pelanggan, SUM() menghitung total pendapatan, sedangkan AVG() menghitung rata-rata nilai transaksi. Hasil yang diperoleh mencakup periode Januari 2020 hingga Desember 2024 dan diurutkan dari bulan terbaru. Sebagai contoh, pada Desember 2024 terdapat 7 invoice dari 7 pelanggan, total pendapatan sebesar 38,62, dan rata-rata nilai transaksi sekitar 5,5171. Hasil tersebut menunjukkan bahwa data invoice berhasil diproses menjadi ringkasan bulanan yang dapat digunakan untuk membantu analisis transaksi.<br></br>


<h3>Kesimpulan</h3>

Praktikum ini memberikan pemahaman mengenai konsistensi dan replikasi data pada sistem terdistribusi melalui PostgreSQL dan Apache Ignite. Pada PostgreSQL, streaming replication diuji dengan memeriksa status primary dan replica, membaca data dari kedua server, serta mempromosikan replica secara manual setelah primary dihentikan. Pada Apache Ignite, cluster tiga node berhasil diinisialisasi, tabel dan data berhasil dimuat, serta query ARTIST menghasilkan jumlah 275 pada setiap node yang diuji. Query analisis invoice juga berhasil menghasilkan ringkasan transaksi bulanan. Berdasarkan hasil tersebut, praktikum menunjukkan bagaimana replikasi membantu ketersediaan dan akses data pada sistem terdistribusi. Namun, pengujian promosi PostgreSQL masih dilakukan secara manual, sementara verifikasi Ignite yang dilakukan berfokus pada kesamaan jumlah data dan keberhasilan query analitik.
