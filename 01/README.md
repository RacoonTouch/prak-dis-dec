Pada Laporan Praktikum Sistem Terdistribusi dan Desentralisasi ini merupakan pengenalan cara menggunakan git-github supaya maahasiswa mengetahui cara menginstal git serta menggunakannya di minggu 1

**INSTALASI Git**

1. menginstal git pada halaman git serta mendownload versi Windows/x64 Setup dikarenakan versi ini mendukung pengguna Windows intel
<img width="440" height="392" alt="Install-Git-01" src="https://github.com/user-attachments/assets/d59e0f01-9f33-4bfe-949c-17f30c4a45e7" />
<br></br>

2. Menginstal sesuai prosedur pada petunjuk-git-github lalu memilih lokasi instalasi dan lanjut klik **Next** tanpa mengubah komponen (default).

<img width="481" height="379" alt="Install-Git-02" src="https://github.com/user-attachments/assets/7b6b066f-2c44-4ef2-aa96-212833083ceb" /><br></br>
<img width="491" height="374" alt="Install-Git-03" src="https://github.com/user-attachments/assets/0a0bfaab-9af0-4f73-a5d9-a507e6789d82" /><br></br>
<img width="494" height="378" alt="Install-Git-04" src="https://github.com/user-attachments/assets/d7835061-db4a-47ca-ba77-cffd0bc8d2d2" /><br></br>


3. Setelah itu mengisi sc untuk menu startnya. Selanjutnya memilih editor yang digunakan yaitu Vim dan mengubah konfigurasi dari defaultnya **master** ke **main**. Pada PATH environment saya memilih "Git from the command line and also from 3rd-party software.

<img width="497" height="382" alt="Install-Git-05" src="https://github.com/user-attachments/assets/60dec183-8498-4216-be28-66dd902468ee" /><br></br>
<img width="493" height="379" alt="Install-Git-06" src="https://github.com/user-attachments/assets/046201c0-8973-4d9e-8991-44e0c6046de9" /><br></br>
<img width="494" height="384" alt="Install-Git-07" src="https://github.com/user-attachments/assets/ecb35e75-0d04-42ff-befd-085f0ed3b584" /><br></br>
<img width="487" height="374" alt="Install-Git-08" src="https://github.com/user-attachments/assets/4949abb1-b982-4c34-b6d3-59c1bc6b9bc8" /><br></br>


4. Pada tahap ini saya memilih Use Bundle OpenSSH dikarenakan Git langsung menyertakan executable SSH sendiri sehingga tidak perlu menginstal aplikasi SSh tambahan di Windows. Selanjutnya memilih native Windows Secure Channel library sesuai instruksi serta memilih pilihan pertama pada Checkout Windows-style, commit Unix-style line endings.

<img width="495" height="378" alt="Install-Git-09" src="https://github.com/user-attachments/assets/d187ddf0-f663-4927-a4e4-ea9ce151a709" /><br></br>
<img width="495" height="381" alt="Install-Git-10" src="https://github.com/user-attachments/assets/1139f6bb-2b99-4917-bc48-a25c25ceb2e0" /><br></br>
<img width="492" height="384" alt="Install-Git-11" src="https://github.com/user-attachments/assets/dd8de5ec-9142-490f-9915-2e01bdff1ce7" /><br></br>


5. Memilih MinTTY untuk terminal yang akses Git Bash. Lalu memilih Merge pada default behavior of 'git pull' dan selanjutnya memilih Git Credential Manager pada Credential helper.

<img width="499" height="381" alt="Install-Git-12" src="https://github.com/user-attachments/assets/beaf1d33-f8a5-4873-9c7b-5a4aacef7ef7" /><br></br>
<img width="496" height="378" alt="Install-Git-13" src="https://github.com/user-attachments/assets/06c63463-f536-4b6d-b634-67d956600b83" /><br></br>
<img width="493" height="376" alt="Install-Git-14" src="https://github.com/user-attachments/assets/a473b001-bf05-4735-a37f-ae412f310eb5" /><br></br>


6. Pada opsi ekstra memilih Enable file system caching dan proses instalasi berlangsung serta terakhir klik **Finish**.

<img width="495" height="381" alt="Install-Git-15" src="https://github.com/user-attachments/assets/213f08de-76d2-4b05-b26a-bcb71c0c3e35" /><br></br>
<img width="493" height="375" alt="Install-Git-16" src="https://github.com/user-attachments/assets/bea71b16-44ae-41ec-9608-63a708ebb215" /><br></br>
<img width="491" height="381" alt="Install-Git-17" src="https://github.com/user-attachments/assets/4675e033-2b5d-40a9-ae32-1c366576852e" /><br></br>


7. Selanjutnya mencoba dari cmd (command prompt) untuk mengetes install tersebut sudah atau belum dengan menjalankan "git" sehingga hasil tersebut seperti ini:

C:\Users\user>git
usage: git [-v | --version] [-h | --help] [-C <path>] [-c <name>=<value>]
           [--exec-path[=<path>]] [--html-path] [--man-path] [--info-path]
           [-p | --paginate | -P | --no-pager] [--no-replace-objects] [--no-lazy-fetch]
           [--no-optional-locks] [--no-advice] [--bare] [--git-dir=<path>]
           [--work-tree=<path>] [--namespace=<name>] [--config-env=<name>=<envvar>]
           <command> [<args>]

These are common Git commands used in various situations:

start a working area (see also: git help tutorial)
   clone      Clone a repository into a new directory
   init       Create an empty Git repository or reinitialize an existing one

work on the current change (see also: git help everyday)
   add        Add file contents to the index
   mv         Move or rename a file, a directory, or a symlink
   restore    Restore working tree files
   rm         Remove files from the working tree and from the index

examine the history and state (see also: git help revisions)
   bisect     Use binary search to find the commit that introduced a bug
   diff       Show changes between commits, commit and working tree, etc
   grep       Print lines matching a pattern
   log        Show commit logs
   show       Show various types of objects
   status     Show the working tree status

grow, mark and tweak your common history
   backfill   Download missing objects in a partial clone
   branch     List, create, or delete branches
   commit     Record changes to the repository
   history    EXPERIMENTAL: Rewrite history
   merge      Join two or more development histories together
   rebase     Reapply commits on top of another base tip
   reset      Set `HEAD` or the index to a known state
   switch     Switch branches
   tag        Create, list, delete or verify tags

collaborate (see also: git help workflows)
   fetch      Download objects and refs from another repository
   pull       Fetch from and integrate with another repository or a local branch
   push       Update remote refs along with associated objects

'git help -a' and 'git help -g' list available subcommands and some
concept guides. See 'git help <command>' or 'git help <concept>'
to read about a specific subcommand or concept.
See 'git help git' for an overview of the system.

C:\Users\user>

==**VERSI DARI GIT**==

C:\Users\user>git --version
git version 2.56.0.windows.2

C:\Users\user>




**KONFIGURASI Git**
Pada sesi ini sesuai dengan instruksi, dalam mengkonfigurasi git tersebut user harus memberitahu tentang username dan email. Sehingga ada hal-hal yang perlu dilakukan seperti ini perintahnya:

$ git config --global user.name "Nama Anda di GitHub"
$ git config --global user.email email@domain.tld

menjadi:

<img width="595" height="59" alt="konfigurasi-git-01" src="https://github.com/user-attachments/assets/bbbdc6fc-4b06-4594-a453-71e56e058c8d" /><br></br>


Pada saat sudah melakukan sesuai perintah diatas (saya lakukan di command prompt), untuk melihatnya perlu melakukannya seperti ini di terminal "git config --list" maka akan menjadi seperti ini outputnya:

<img width="370" height="307" alt="konfigurasi-git-02" src="https://github.com/user-attachments/assets/2fd3a783-0fe6-47d9-b949-935ce2003cdf" />
