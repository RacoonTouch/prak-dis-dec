<h3> KOMUNIKASI ANTAR PROSES PADA SISTEM TERDISTRIBUSI </h3>



Pada tugas pertama diperintahkan untuk melihat daftar proses yang terdiri dari penggunaan CPU, Memory, Disk dan Network. Setiap komputer akan berbeda-beda tampilan dan prosesnya.
contoh task manager:

<img width="677" height="434" alt="app-1" src="https://github.com/user-attachments/assets/16db7f79-694b-40d0-bcdb-1767f94ef276" />
<img width="490" height="329" alt="app-2" src="https://github.com/user-attachments/assets/8dd80380-d216-4a6b-bb43-510b631f433a" />
<img width="494" height="365" alt="app-3" src="https://github.com/user-attachments/assets/418c7e10-f4bc-499b-9959-fc220073db2b" /><br></br>
Tujuan di atas adalah melihat aplikasi yang dijalankan seperti notepad.exe ketika digunakan dan dihentikan secara paksa dapat menutup proses aplikasi yang tengah berlangsung dan itu dapat kehilangan data yang belum disimpan. Jika aplikasi notepad dibuka kembali maka sistem biasanya membuat proses baru.<br></br>

<h3>Menggunakan Strawberry untuk GraphQL server</h3>

Pada sesi praktik ini diperintahkan untuk menginstal uv dan python untuk menggunakan paket Strawberry pada GraphQL server.
Dalam menginstal uv perlu menyesuaikan sistem operasi yang digunakan seperti contoh saya menggunakan Windows:
<img width="714" height="135" alt="task-1" src="https://github.com/user-attachments/assets/94617db2-adf1-482b-9942-1437de5398bd" />

<b>**Selanjutnya menjalankan uv sebagai berikut:**</b>

```bash
PS C:\Users\user> uv
An extremely fast Python package manager.

Usage: uv.exe [OPTIONS] <COMMAND>

Commands:
  auth       Manage authentication
  run        Run a command or script
  init       Create a new project
  add        Add dependencies to the project
  remove     Remove dependencies from the project
  version    Read or update the project's version
  sync       Update the project's environment
  lock       Update the project's lockfile
  export     Export the project's lockfile to an alternate format
  tree       Display the project's dependency tree
  format     Format Python code in the project
  check      Run checks on the project
  audit      Audit the project's dependencies
  tool       Run and install commands provided by Python packages
  python     Manage Python versions and installations
  pip        Manage Python packages with a pip-compatible interface
  venv       Create a virtual environment
  build      Build Python packages into source distributions and wheels
  publish    Upload distributions to an index
  workspace  Inspect uv workspaces
  cache      Manage uv's cache
  self       Manage the uv executable
  help       Display documentation for a command

Cache options:
  -n, --no-cache               Avoid reading from or writing to the cache, instead using a temporary directory for the
                               duration of the operation [env: UV_NO_CACHE=]
      --cache-dir <CACHE_DIR>  Path to the cache directory [env: UV_CACHE_DIR=]

Python options:
      --managed-python       Require use of uv-managed Python versions [env: UV_MANAGED_PYTHON=]
      --no-managed-python    Disable use of uv-managed Python versions [env: UV_NO_MANAGED_PYTHON=]
      --no-python-downloads  Disable automatic downloads of Python. [env: "UV_PYTHON_DOWNLOADS=never"]

Global options:
  -q, --quiet...
          Use quiet output
  -v, --verbose...
          Use verbose output
      --color <COLOR_CHOICE>
          Control the use of color in output [possible values: auto, always, never]
      --system-certs
          Whether to load TLS certificates from the platform's native certificate store [env: UV_SYSTEM_CERTS=]
      --offline
          Disable network access [env: UV_OFFLINE=]
      --allow-insecure-host <ALLOW_INSECURE_HOST>
          Allow insecure connections to a host [env: UV_INSECURE_HOST=]
      --no-progress
          Hide all progress outputs [env: UV_NO_PROGRESS=]
      --directory <DIRECTORY>
          Change to the given directory prior to running the command [env: UV_WORKING_DIR=]
      --project <PROJECT>
          Discover a project in the given directory [env: UV_PROJECT=]
      --config-file <CONFIG_FILE>
          The path to a `uv.toml` file to use for configuration [env: UV_CONFIG_FILE=]
      --no-config
          Avoid discovering configuration files (`pyproject.toml`, `uv.toml`) [env: UV_NO_CONFIG=]
  -h, --help
          Display the concise help for this command
  -V, --version
          Display the uv version

Use `uv help` for more details.
```

**update uv version terbaru.**
```bash
PS C:\Users\user> uv self update
info: Checking for updates...
success: You're already on version v0.13.1 of uv (the latest version).
PS C:\Users\user>
```

**Membuat workspace untuk python**
Ini akan sedikit berbeda tampilannya pada saat mengikuti panduan menginstal uv dikarenakan sistem operasi tersebut menggunakan Windows.
```bash
PS C:\Users\user> mkdir workspace-01


    Directory: C:\Users\user


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        10/14/2026  11:53 PM                workspace-01


PS C:\Users\user> cd workspace-01
PS C:\Users\user\workspace-01> uv python install 3.14
Installed Python 3.14.8 in 19.91s
 + cpython-3.14.8-windows-x86_64-none (python3.14.exe)
```

**Membuat environment**
```bash
PS C:\Users\user\workspace-01> uv venv --python 3.14
Using CPython 3.14.8
Creating virtual environment at: .venv
Activate with: .venv\Scripts\activate
PS C:\Users\user\workspace-01>
```


**Kelola paket**
Dalam mengelola paket, saya menggunakan uv pip install "strawberry-graphql[cli]" untuk mengelola workspacenya.
```bash
PS C:\Users\user\workspace-01> uv pip install "strawberry-graphql[cli]"
Resolved 25 packages in 2.70s
Prepared 25 packages in 13.32s
Installed 25 packages in 913ms
 + annotated-doc==0.0.5
 + anyio==4.15.1
 + click==8.5.0
 + colorama==0.4.6
 + cross-web==0.7.0
 + graphql-core==3.3.0
 + h11==0.16.0
 + idna==3.20
 + libcst==1.9.0
 + markdown-it-py==4.2.0
 + mdurl==0.1.2
 + packaging==26.3
 + pygments==2.21.0
 + python-dateutil==2.9.0.post0
 + python-multipart==0.0.32
 + pyyaml==6.0.3
 + rich==15.0.0
 + shellingham==1.5.4
 + six==1.17.0
 + starlette==1.8.0
 + strawberry-graphql==0.335.0
 + typer==0.27.3
 + typing-extensions==4.16.0
 + uvicorn==0.54.0
 + websockets==16.1.1
```

**Buat file schema.py**

<img width="285" height="317" alt="task-3" src="https://github.com/user-attachments/assets/a3471552-1577-4a4e-9364-683db44e8c08" /><br></br>

**Jalankan Server**

Perintah uv run menjalankan program menggunakan environment proyek tanpa perlu mengaktifkan .venv secara manual. strawberry dev schema menjalankan server GraphQL berdasarkan schema yang telah kita buat. Server ini bertugas menerima permintaan data dari client.
<img width="422" height="36" alt="task-4" src="https://github.com/user-attachments/assets/d799f792-b5ec-4046-a1a3-930a9fb0bb7d" /><br></br>

**Mengambil data buku menggunakan Query**

Server GraphQL menerima permintaan dari pengguna, membaca field books, lalu mengembalikan data buku sesuai dengan definisi pada schema.py. Field title digunakan untuk memperoleh judul buku, sedangkan field author digunakan untuk memperoleh nama penulis. Pengujian ini memastikan bahwa schema yang dibuat dapat diakses melalui antarmuka GraphQL.
<img width="949" height="237" alt="task-5" src="https://github.com/user-attachments/assets/0e3502de-ab9c-43fe-bda7-b963c073b739" /><br></br>

**Membuat Client untuk mengakses server**

PowerShell pertama tetap menjalankan server. Kita buka PowerShell kedua, kemudian menjalankan:
```bash
cd C:\Users\user\workspace-01
notepad client.py
```

setelah membuat notepad baru untuk client lalu menambahkan kode ini dan save (Ctrl + S) lalu close notepad:
```bash

import json
from urllib.request import Request, urlopen
from urllib.error import HTTPError, URLError

url = "http://127.0.0.1:8000/graphql"

query = """
{
    books {
        title
        author
    }
}
"""

payload = json.dumps({"query": query}).encode("utf-8")

request = Request(
    url,
    data=payload,
    headers={"Content-Type": "application/json"},
    method="POST"
)

try:
    with urlopen(request, timeout=10) as response:
        result = json.loads(response.read().decode("utf-8"))

    print("HASIL PERMINTAAN GRAPHQL")
    print(json.dumps(result, indent=4, ensure_ascii=False))

except HTTPError as error:
    print("HTTP Error:", error.code)
    print(error.read().decode("utf-8"))

except URLError as error:
    print("Gagal terhubung ke server:", error.reason)
```

**Menjalankan program client**

Pada powershell kedua isi kode ini:
```bash
uv run --directory . python client.py
```

Dan jika berhasil maka akan seperti ini tampilan data buku dalam format JSONnya:
```bash
HASIL PERMINTAAN GRAPHQL
{
    "data": {
        "books": [
            {
                "title": "The Great Gatsby",
                "author": "F. Scott Fitzgerald"
            }
        ]
    }
}
```
Program client menggunakan modul urllib.request untuk mengirim permintaan HTTP ke alamat server GraphQL. Query ditulis untuk meminta field title dan author dari data books, kemudian dibungkus dalam format JSON agar dapat dikirim melalui HTTP POST. Header Content-Type: application/json memberitahukan server bahwa isi permintaan menggunakan format JSON.
Setelah server memberikan respons, program membaca respons tersebut dan mengubahnya menjadi objek Python menggunakan json.loads(). Selanjutnya, data ditampilkan dengan format JSON yang lebih rapi. Penanganan HTTPError dan URLError ditambahkan agar program dapat memberikan informasi ketika server mengembalikan kesalahan HTTP atau tidak dapat dihubungi.



<h3>Kesimpulan praktikum</h3>

Komunikasi antarproses pada sistem terdistribusi dapat dilakukan melalui client dan server yang saling bertukar data menggunakan protokol HTTP. Dalam praktikum ini, server GraphQL menyediakan data buku berdasarkan schema yang telah dibuat, sedangkan client Python mengirimkan query dan menerima hasilnya dalam format JSON. Dengan demikian, client dapat memperoleh data dari server tanpa harus mengakses kode internal server secara langsung.
