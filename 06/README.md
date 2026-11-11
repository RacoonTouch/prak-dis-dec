[penguins.csv](https://github.com/user-attachments/files/34696985/penguins.csv)<h1>Distributed File System - HDFS</h1>

<h3>0. Persyaratan Software</h3>
Karena menggunakan Ubuntu WSL, perintah ini dijalankan di terminal Ubuntu.

Periksa instalasi Java:
```
java -version
```
```
openjdk version "17.0.21.0.3" 2026-10-24
OpenJDK Runtime Environment (build 17.0.21.0.3+1-1-26.04.1-Ubuntu)
OpenJDK 64-Bit Server VM (build 17.0.21.0.3+1-1-26.04.1-Ubuntu, mixed mode, sharing)
```


Periksa instalasi pdsh:
```
pdsh -V
```
```
pdsh-2.35 (+readline+debug)
rcmd modules: ssh,exec (default: ssh)
misc modules: genders
```

Periksa instalasi SSH:
```
ssh -V
```
```
OpenSSH_10.2p1 Ubuntu-2ubuntu3.8, OpenSSL 3.5.5 27 Jan 2026
```

**Pembahasan :**

Pada tahap persyaratan software, dilakukan instalasi Java Development Kit (JDK) versi 17, pdsh, dan SSH melalui package manager Ubuntu. JDK 17 berfungsi sebagai lingkungan untuk menjalankan Apache Hadoop, sedangkan pdsh digunakan untuk menjalankan perintah pada beberapa node secara bersamaan. SSH digunakan sebagai protokol komunikasi jarak jauh antarmesin. Perintah pemeriksaan versi digunakan untuk memastikan bahwa software yang dibutuhkan telah berhasil diinstal.<br></br>


<h3>1. Unduh Apache Hadoop</h3>
<img width="613" height="508" alt="1" src="https://github.com/user-attachments/assets/90ef699c-1e45-43b7-855a-491bc651327f" />

Cari versi Hadoop 3.5.0 dan unduh file distribusinya:
```
https://hadoop.apache.org/releases.html
```
lalu masuk ke folder Downloads Windows dapat diakses dari Ubuntu WSL melalui:
```
cd /mnt/c/Users/user/Downloads
```
Periksa file dan pastikan file hadoop-3.5.0.tar.gz terlihat:
```
ls
```
```
hadoop-3.5.0.tar.gz
```

**Pembahasan :**

Pada tahap ini dilakukan pengunduhan Apache Hadoop versi 3.5.0 melalui situs resmi Apache Hadoop. File yang diunduh memiliki format .tar.gz, yaitu format arsip terkompresi yang digunakan untuk mendistribusikan perangkat lunak. File tersebut nantinya akan diekstrak dan digunakan sebagai direktori instalasi Hadoop. Penggunaan versi 3.5.0 disesuaikan dengan ketentuan versi perangkat lunak<br></br>


<h3>2. Instalasi Apache Hadoop</h3>
Pada bagian ini, kita akan mengekstrak file Hadoop dan mengatur variabel lingkungan agar perintah Hadoop dapat dijalankan dari terminal.

Buat direktori penyimpanan Hadoop:
```
mkdir -p ~/software/storage
```

Ekstrak file Hadoop yang telah diunduh:
```
tar -xvf /mnt/c/Users/user/Downloads/hadoop-3.5.0.tar.gz -C ~/software/storage/
```

Buat symbolic link
```
cd ~/software/storage
ln -s hadoop-3.5.0 hadoop
```

Atur variabel lingkungan Hadoop.
```
nano ~/.bashrc
```

Tambahkan konfigurasi berikut di bagian paling bawah:
catatan: Simpan dengan Ctrl + O, tekan Enter, kemudian keluar menggunakan Ctrl + X. jika sudah selesai mengkonfigurasinya.
```
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
export HADOOP_HOME=$HOME/software/storage/hadoop
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

Aktifkan konfigurasi:
```
source ~/.bashrc
```

Periksa instalasi Hadoop:
```
hadoop version
```
Output :
```
Hadoop 3.5.0
Source code repository https://github.com/apache/hadoop.git -r dbcc7cd797100e6b32cd84f85b53a5193a5f9af0
Compiled by cnauroth on 2026-03-24T16:56Z
Compiled on platform linux-x86_64
Compiled with protoc 3.25.5
From source with checksum 64582ed0a62a169c39a19c734a8f33b5
This command was run using /home/racoon21_touch/software/storage/hadoop-3.5.0/share/hadoop/common/hadoop-common-3.5.0.jar
```

**Pembahasan :**

Pada tahap instalasi Apache Hadoop, dilakukan ekstraksi file Hadoop versi 3.5.0 ke dalam direktori ~/software/storage. Selanjutnya, dibuat symbolic link bernama hadoop yang mengarah ke direktori instalasi Hadoop agar pemanggilan direktori menjadi lebih sederhana. Variabel lingkungan JAVA_HOME digunakan untuk menentukan lokasi instalasi Java, sedangkan HADOOP_HOME menunjukkan lokasi instalasi Hadoop. Variabel PATH ditambahkan agar perintah Hadoop dapat dijalankan dari berbagai direktori terminal. Keberhasilan instalasi diperiksa menggunakan perintah hadoop version yang menampilkan informasi versi Hadoop.<br></br>


<h3>3. Instalasi pdsh dan konfigurasi SSH</h3>
Pada modul, pdsh digunakan untuk mendukung komunikasi dan eksekusi perintah pada node. Sementara itu, SSH digunakan untuk menghubungkan node tanpa harus memasukkan password setiap kali melakukan koneksi.

Karena kita menggunakan Ubuntu WSL, jalankan:
```
sudo service ssh start
```

Kemudian periksa status SSH:
*Jika status menunjukkan active (running), berarti layanan SSH sudah berjalan.*
```
sudo service ssh status
```
Output :
```
Active: active (running)
```

Periksa instalasi pdsh:
```
pdsh -V
```

Kemudian atur jenis komunikasi pdsh menggunakan SSH:
```
echo 'export PDSH_RCMD_TYPE=ssh' >> ~/.bashrc
source ~/.bashrc
```

Lalu kita akan membuat SSH key untuk koneksi ke localhost.
Buat direktori SSH jika belum tersedia:
```
mkdir -p ~/.ssh
```

Buat SSH key:
```
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
```

Tambahkan public key ke authorized_keys:
```
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

Atur permission:
```
chmod 0600 ~/.ssh/authorized_keys
```

Uji koneksi SSH:
```
ssh localhost
```

Jika muncul pertanyaan:
```
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```
Ketik "yes".
Jika berhasil, terminal akan masuk ke sesi localhost tanpa meminta password akun Linux.

**Pembahasan :**

Pada tahap ini dilakukan instalasi dan konfigurasi pdsh serta SSH. Program pdsh digunakan untuk menjalankan perintah pada node secara bersamaan, sedangkan SSH menyediakan mekanisme komunikasi antar-node. Variabel PDSH_RCMD_TYPE diatur menjadi ssh agar pdsh menggunakan SSH sebagai metode komunikasi. Selanjutnya, dibuat pasangan SSH key yang terdiri atas private key dan public key. Public key dimasukkan ke dalam file authorized_keys agar koneksi SSH ke localhost dapat dilakukan tanpa memasukkan password berulang kali. Pengujian dilakukan menggunakan perintah ssh localhost.<br></br>


<h3>4. Konfigurasi Apache Hadoop</h3>
Pada tahap ini, kita akan mengatur empat file konfigurasi Hadoop. Semua file berada di:

```
$HADOOP_HOME/etc/hadoop
```

Untuk masuk ke direktori tersebut:
```
cd $HADOOP_HOME/etc/hadoop
```

Pada sesi ini kita melakukan konfigurasi hdfs-site.xml dan buka file:
```
nano hdfs-site.xml
```

Sesuaikan isinya menjadi:
*jika sudah simpan perubahan*
```
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```

Lalu konfigurasi core-site.xml dan Buka file:
```
nano core-site.xml
```

Masukkan konfigurasi berikut. Ganti mahasiswa dengan hasil whoami:
```
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
    <property>
        <name>hadoop.proxyuser.mahasiswa.groups</name>
        <value>*</value>
    </property>
    <property>
        <name>hadoop.proxyuser.mahasiswa.hosts</name>
        <value>*</value>
    </property>
</configuration>
```
Ini fungsinya mengatur alamat sistem file HDFS menggunakan localhost pada port 9000. Konfigurasi proxyuser menentukan grup dan host yang diizinkan untuk menjalankan operasi atas nama pengguna tersebut.

Selanjutnya konfigurasi mapred-site.xml dan Buka file:
```
nano mapred-site.xml
```

Masukkan:
```
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>mapreduce.application.classpath</name>
        <value>$HADOOP_CLASSPATH:$HADOOP_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
    </property>
</configuration>
```
Fungsi ini menentukan YARN sebagai framework untuk menjalankan proses MapReduce. Pengaturan classpath digunakan untuk menentukan lokasi pustaka yang diperlukan oleh aplikasi MapReduce.

Konfigurasi yarn-site.xml
Buka file:
```
nano yarn-site.xml
```

Masukkan:
```
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <property>
        <name>yarn.nodemanager.env-whitelist</name>
        <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_HOME,PATH,LANG,TZ,HADOOP_MAPRED_HOME</value>
    </property>
    <property>
        <name>yarn.nodemanager.resource.memory-mb</name>
        <value>4096</value>
    </property>
    <property>
        <name>yarn.scheduler.minimum-allocation-mb</name>
        <value>2048</value>
    </property>
    <property>
        <name>yarn.nodemanager.vmem-pmem-ratio</name>
        <value>2.1</value>
    </property>
</configuration>
```

**Pembahasan :**

Pada tahap konfigurasi Apache Hadoop, dilakukan pengaturan terhadap empat file konfigurasi utama, yaitu hdfs-site.xml, core-site.xml, mapred-site.xml, dan yarn-site.xml. File hdfs-site.xml digunakan untuk menentukan jumlah replikasi data pada HDFS sebanyak satu kali. File core-site.xml digunakan untuk menentukan alamat sistem file HDFS serta konfigurasi proxyuser. File mapred-site.xml mengatur penggunaan YARN sebagai framework MapReduce, sedangkan yarn-site.xml digunakan untuk mengatur layanan NodeManager, variabel lingkungan, dan alokasi memori. Keempat konfigurasi tersebut diperlukan agar Hadoop dapat menjalankan layanan HDFS dan mendukung pemrosesan data terdistribusi.<br></br>


<h3>5. Format NameNode</h3>

Tahap ini bertujuan untuk menginisialisasi NameNode sebelum HDFS digunakan.
Menjalankan perintah:
```
hdfs namenode -format
```
Output :
```
2026-11-11 22:41:47,267 INFO namenode.NameNode: STARTUP_MSG:
/************************************************************
STARTUP_MSG: Starting NameNode
STARTUP_MSG:   host = YoungGC/127.0.1.1
STARTUP_MSG:   args = [-format]
STARTUP_MSG:   version = 3.5.0
STARTUP_MSG:   classpath = /home/racoon21_touch/software/storage/hadoop/etc/hadoop:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/hk2-utils-2.6.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/kerby-util-2.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jackson-databind-2.18.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/netty-handler-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jsr305-3.0.2.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jersey-server-2.46.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/hadoop-shaded-protobuf_3_25-1.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/hadoop-tos-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/commons-configuration2-2.10.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/commons-net-3.9.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/kerby-pkix-2.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/kerb-crypto-2.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/javax.servlet-api-3.1.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/j2objc-annotations-3.0.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jersey-client-2.46.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/commons-logging-1.3.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/zookeeper-jute-3.8.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/listenablefuture-9999.0-empty-to-avoid-conflict-with-guava.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/commons-lang3-3.18.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/dnsjava-3.6.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/commons-cli-1.9.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jersey-hk2-2.46.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/hadoop-annotations-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/commons-collections4-4.4.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/txw2-2.3.9.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/nimbus-jose-jwt-10.4.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jetty-xml-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/azure-storage-7.0.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/curator-client-5.2.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jaxb-runtime-2.3.9.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/slf4j-api-1.7.36.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/netty-common-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/hadoop-huaweicloud-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/commons-codec-1.15.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/commons-text-1.14.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/audience-annotations-0.12.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/commons-math3-3.6.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/httpclient-4.5.13.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/hk2-locator-2.6.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/netty-buffer-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jakarta.servlet.jsp-api-2.3.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jetty-util-ajax-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/netty-transport-native-unix-common-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jetty-io-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jackson-annotations-2.18.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/failureaccess-1.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jakarta.annotation-api-1.3.5.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jersey-container-servlet-2.46.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/hadoop-azure-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/hadoop-shaded-guava-1.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/osgi-resource-locator-1.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/javassist-3.30.2-GA.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jspecify-1.0.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/guava-33.4.8-jre.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/re2j-1.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/reload4j-1.2.22.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jakarta.servlet-api-4.0.4.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jersey-common-2.46.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/hadoop-cos-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jetty-servlet-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jetty-server-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jakarta.activation-api-1.2.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/hk2-api-2.6.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/zookeeper-3.8.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jetty-security-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jakarta.inject-2.6.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/wildfly-openssl-2.2.5.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/stax2-api-4.2.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jakarta.ws.rs-api-2.1.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jetty-http-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/hadoop-auth-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/slf4j-reload4j-1.7.36.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/kerb-util-2.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/netty-transport-native-epoll-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jakarta.activation-1.2.2.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/snappy-java-1.1.10.4.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/netty-resolver-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/hadoop-azure-datalake-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jakarta.xml.bind-api-2.3.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/hadoop-aws-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jsch-0.1.55.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/commons-daemon-1.0.13.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jul-to-slf4j-1.7.36.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/gson-2.9.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/aopalliance-repackaged-2.6.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/netty-transport-classes-epoll-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/hadoop-gcp-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/bcprov-jdk18on-1.82.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/kerby-config-2.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/HdrHistogram-2.1.12.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/httpcore-4.4.13.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/netty-codec-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/avro-1.11.5.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/kerb-core-2.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/analyticsaccelerator-s3-1.3.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/azure-keyvault-core-1.0.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/netty-transport-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/istack-commons-runtime-3.0.12.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/metrics-core-3.2.4.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jetty-webapp-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/commons-compress-1.26.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/commons-io-2.16.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jakarta.validation-api-2.0.2.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/curator-framework-5.2.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jetty-util-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/curator-recipes-5.2.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/kerby-asn1-2.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/woodstox-core-5.4.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jersey-container-servlet-core-2.46.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/lib/jackson-core-2.18.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/hadoop-registry-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/hadoop-common-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/hadoop-kms-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/hadoop-nfs-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/common/hadoop-common-3.5.0-tests.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/hk2-utils-2.6.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/HikariCP-4.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/kerby-util-2.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jackson-databind-2.18.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-handler-ssl-ocsp-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-handler-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jsr305-3.0.2.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jersey-server-2.46.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/hadoop-shaded-protobuf_3_25-1.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/commons-configuration2-2.10.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/commons-net-3.9.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-codec-http-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-codec-haproxy-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/kerby-pkix-2.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/kerb-crypto-2.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/javax.servlet-api-3.1.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/j2objc-annotations-3.0.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jersey-client-2.46.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-transport-classes-kqueue-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/commons-logging-1.3.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/zookeeper-jute-3.8.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-resolver-dns-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/listenablefuture-9999.0-empty-to-avoid-conflict-with-guava.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/commons-lang3-3.18.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-transport-native-epoll-4.1.130.Final-linux-riscv64.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-transport-rxtx-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/dnsjava-3.6.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/commons-cli-1.9.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jersey-hk2-2.46.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-resolver-dns-native-macos-4.1.130.Final-osx-aarch_64.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-codec-dns-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/hadoop-annotations-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/commons-collections4-4.4.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/txw2-2.3.9.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/nimbus-jose-jwt-10.4.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jetty-xml-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/curator-client-5.2.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jaxb-runtime-2.3.9.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-codec-mqtt-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-codec-smtp-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-common-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/commons-codec-1.15.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/commons-text-1.14.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/audience-annotations-0.12.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/commons-math3-3.6.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-codec-xml-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/httpclient-4.5.13.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/hk2-locator-2.6.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-buffer-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jakarta.servlet.jsp-api-2.3.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jetty-util-ajax-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-transport-native-unix-common-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-codec-http2-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jetty-io-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jackson-annotations-2.18.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/failureaccess-1.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jakarta.annotation-api-1.3.5.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jersey-container-servlet-2.46.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/json-simple-1.1.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/hadoop-shaded-guava-1.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/osgi-resource-locator-1.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/javassist-3.30.2-GA.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jspecify-1.0.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/guava-33.4.8-jre.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/re2j-1.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/reload4j-1.2.22.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jakarta.servlet-api-4.0.4.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-codec-redis-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jersey-common-2.46.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-transport-native-kqueue-4.1.130.Final-osx-aarch_64.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jetty-servlet-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jetty-server-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jakarta.activation-api-1.2.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/hk2-api-2.6.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/zookeeper-3.8.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-transport-native-epoll-4.1.130.Final-linux-x86_64.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jetty-security-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jakarta.inject-2.6.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/stax2-api-4.2.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jakarta.ws.rs-api-2.1.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-codec-stomp-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jetty-http-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/hadoop-auth-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/kerb-util-2.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-transport-native-epoll-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jakarta.activation-1.2.2.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/snappy-java-1.1.10.4.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-resolver-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jakarta.xml.bind-api-2.3.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jettison-1.5.4.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jsch-0.1.55.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-resolver-dns-native-macos-4.1.130.Final-osx-x86_64.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/commons-daemon-1.0.13.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/gson-2.9.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/aopalliance-repackaged-2.6.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-transport-classes-epoll-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/kerby-config-2.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/httpcore-4.4.13.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-transport-native-kqueue-4.1.130.Final-osx-x86_64.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-codec-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/avro-1.11.5.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/kerb-core-2.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-handler-proxy-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-transport-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/istack-commons-runtime-3.0.12.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/metrics-core-3.2.4.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jetty-webapp-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/commons-compress-1.26.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/leveldbjni-all-1.8.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-transport-native-epoll-4.1.130.Final-linux-aarch_64.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/commons-io-2.16.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-transport-sctp-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jakarta.validation-api-2.0.2.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-codec-memcache-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/curator-framework-5.2.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jetty-util-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/curator-recipes-5.2.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-resolver-dns-classes-macos-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/kerby-asn1-2.0.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-codec-socks-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/woodstox-core-5.4.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jersey-container-servlet-core-2.46.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-all-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/netty-transport-udt-4.1.130.Final.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/lib/jackson-core-2.18.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/hadoop-hdfs-3.5.0-tests.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/hadoop-hdfs-nfs-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/hadoop-hdfs-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/hadoop-hdfs-httpfs-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/hadoop-hdfs-client-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/hadoop-hdfs-native-client-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/hadoop-hdfs-native-client-3.5.0-tests.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/hadoop-hdfs-client-3.5.0-tests.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/hadoop-hdfs-rbf-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/hdfs/hadoop-hdfs-rbf-3.5.0-tests.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/mapreduce/lib/javax.inject-1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-3.5.0-tests.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-client-hs-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-client-app-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-client-hs-plugins-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-client-uploader-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-client-nativetask-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-client-core-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-client-shuffle-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-client-common-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/asm-commons-9.7.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/jackson-module-jaxb-annotations-2.18.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/jetty-annotations-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/org.eclipse.persistence.moxy-2.7.14.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/jackson-jaxrs-json-provider-2.18.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/websocket-common-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/swagger-annotations-1.5.4.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/jetty-client-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/javax-websocket-client-impl-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/commons-lang-2.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/javax.websocket-api-1.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/caffeine-2.9.3.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/org.eclipse.persistence.core-2.7.14.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/javax.annotation-api-1.3.2.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/cache-api-1.1.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/jersey-media-json-jettison-2.46.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/jetty-jndi-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/websocket-server-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/bcutil-jdk18on-1.82.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/jakarta.json-api-1.1.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/websocket-api-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/websocket-servlet-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/codemodel-2.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/asm-tree-9.7.1.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/bcpkix-jdk18on-1.82.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/websocket-client-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/jline-3.9.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/org.eclipse.persistence.asm-9.6.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/jcodings-1.0.13.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/guice-servlet-5.1.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/javax.websocket-client-api-1.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/jackson-jaxrs-base-2.18.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/commons-csv-1.9.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/guice-5.1.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/jersey-media-jaxb-2.46.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/jsonschema2pojo-core-1.0.2.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/failsafe-2.4.4.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/jna-5.2.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/javax-websocket-server-impl-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/mssql-jdbc-6.2.1.jre7.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/objenesis-2.6.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/fst-2.50.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/aopalliance-1.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/javax.json-1.1.4.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/ehcache-3.8.2.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/jetty-plus-9.4.58.v20250814.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/lib/snakeyaml-2.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-applications-mawo-core-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-client-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-server-nodemanager-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-server-applicationhistoryservice-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-server-globalpolicygenerator-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-server-timeline-pluginstorage-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-services-core-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-server-resourcemanager-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-server-tests-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-server-sharedcachemanager-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-api-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-applications-distributedshell-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-server-common-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-applications-unmanaged-am-launcher-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-registry-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-server-web-proxy-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-server-router-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-common-3.5.0.jar:/home/racoon21_touch/software/storage/hadoop/share/hadoop/yarn/hadoop-yarn-services-api-3.5.0.jar
STARTUP_MSG:   build = https://github.com/apache/hadoop.git -r dbcc7cd797100e6b32cd84f85b53a5193a5f9af0; compiled by 'cnauroth' on 2026-03-24T16:56Z
STARTUP_MSG:   java = 17.0.21.0.3
************************************************************/
2026-11-11 22:41:47,368 INFO namenode.NameNode: registered UNIX signal handlers for [TERM, HUP, INT]
2026-11-11 22:41:47,758 INFO namenode.NameNode: createNameNode [-format]
2026-11-11 22:41:49,422 INFO namenode.NameNode: Formatting using clusterid: CID-5868f5a5-847c-4a5a-9c7e-6a29203ac392
2026-11-11 22:41:49,560 INFO namenode.FSEditLog: Edit logging is async:true
2026-11-11 22:41:49,643 INFO namenode.FSNamesystem: KeyProvider: null
2026-11-11 22:41:49,700 INFO namenode.FSNamesystem: FSNLock is fair: true.
2026-11-11 22:41:49,701 INFO namenode.FSNamesystem: Detailed lock hold time metrics of FSNLock is disabled.
2026-11-11 22:41:49,776 INFO namenode.FSNamesystem: fsOwner                = racoon21_touch (auth:SIMPLE)
2026-11-11 22:41:49,776 INFO namenode.FSNamesystem: supergroup             = supergroup
2026-11-11 22:41:49,777 INFO namenode.FSNamesystem: isPermissionEnabled    = true
2026-11-11 22:41:49,777 INFO namenode.FSNamesystem: isStoragePolicyEnabled = true
2026-11-11 22:41:49,778 INFO namenode.FSNamesystem: HA Enabled: false
2026-11-11 22:41:49,862 INFO common.Util: dfs.datanode.fileio.profiling.sampling.percentage set to 0. Disabling file IO profiling
2026-11-11 22:41:50,229 INFO blockmanagement.DatanodeManager: Slow peers collection thread shutdown
2026-11-11 22:41:50,258 INFO blockmanagement.DatanodeManager: dfs.block.invalidate.limit : configured=1000, counted=60, effected=1000
2026-11-11 22:41:50,259 INFO blockmanagement.DatanodeManager: dfs.namenode.datanode.registration.ip-hostname-check=true
2026-11-11 22:41:50,283 INFO blockmanagement.BlockManager: dfs.namenode.startup.delay.block.deletion.sec is set to 000:00:00:00.000
2026-11-11 22:41:50,285 INFO blockmanagement.BlockManager: The block deletion will start around 2026 Nov 11 22:41:50
2026-11-11 22:41:50,293 INFO util.GSet: Computing capacity for map BlocksMap
2026-11-11 22:41:50,294 INFO util.GSet: VM type       = 64-bit
2026-11-11 22:41:50,307 INFO util.GSet: 2.0% max memory 1.9 GB = 39.4 MB
2026-11-11 22:41:50,315 INFO util.GSet: capacity      = 2^22 = 4194304 entries
2026-11-11 22:41:50,393 INFO blockmanagement.BlockManager: Storage policy satisfier is disabled
2026-11-11 22:41:50,393 INFO blockmanagement.BlockManager: dfs.block.access.token.enable = false
2026-11-11 22:41:50,414 INFO blockmanagement.BlockManagerSafeMode: Using 1000 as SafeModeMonitor Interval
2026-11-11 22:41:50,414 INFO blockmanagement.BlockManagerSafeMode: dfs.namenode.safemode.threshold-pct = 0.999
2026-11-11 22:41:50,415 INFO blockmanagement.BlockManagerSafeMode: dfs.namenode.safemode.min.datanodes = 0
2026-11-11 22:41:50,415 INFO blockmanagement.BlockManagerSafeMode: dfs.namenode.safemode.extension = 30000
2026-11-11 22:41:50,417 INFO blockmanagement.BlockManager: defaultReplication         = 1
2026-11-11 22:41:50,418 INFO blockmanagement.BlockManager: maxReplication             = 512
2026-11-11 22:41:50,418 INFO blockmanagement.BlockManager: minReplication             = 1
2026-11-11 22:41:50,418 INFO blockmanagement.BlockManager: maxReplicationStreams      = 2
2026-11-11 22:41:50,419 INFO blockmanagement.BlockManager: redundancyRecheckInterval  = 3000ms
2026-11-11 22:41:50,419 INFO blockmanagement.BlockManager: encryptDataTransfer        = false
2026-11-11 22:41:50,419 INFO blockmanagement.BlockManager: maxNumBlocksToLog          = 1000
2026-11-11 22:41:50,567 INFO namenode.FSDirectory: GLOBAL serial map: bits=29 maxEntries=536870911
2026-11-11 22:41:50,567 INFO namenode.FSDirectory: USER serial map: bits=24 maxEntries=16777215
2026-11-11 22:41:50,568 INFO namenode.FSDirectory: GROUP serial map: bits=24 maxEntries=16777215
2026-11-11 22:41:50,568 INFO namenode.FSDirectory: XATTR serial map: bits=24 maxEntries=16777215
2026-11-11 22:41:50,606 INFO util.GSet: Computing capacity for map INodeMap
2026-11-11 22:41:50,606 INFO util.GSet: VM type       = 64-bit
2026-11-11 22:41:50,612 INFO util.GSet: 1.0% max memory 1.9 GB = 19.7 MB
2026-11-11 22:41:50,612 INFO util.GSet: capacity      = 2^21 = 2097152 entries
2026-11-11 22:41:50,638 INFO namenode.FSDirectory: ACLs enabled? true
2026-11-11 22:41:50,638 INFO namenode.FSDirectory: POSIX ACL inheritance enabled? true
2026-11-11 22:41:50,639 INFO namenode.FSDirectory: XAttrs enabled? true
2026-11-11 22:41:50,640 INFO namenode.NameNode: Caching file names occurring more than 10 times
2026-11-11 22:41:50,674 INFO snapshot.SnapshotManager: Loaded config captureOpenFiles: false, skipCaptureAccessTimeOnlyChange: false, snapshotDiffAllowSnapRootDescendant: true, maxSnapshotFSLimit: 65536, maxSnapshotLimit: 65536
2026-11-11 22:41:50,674 INFO snapshot.SnapshotManager: dfs.namenode.snapshot.deletion.ordered = false
2026-11-11 22:41:50,678 INFO snapshot.SnapshotManager: SkipList is disabled
2026-11-11 22:41:50,692 INFO util.GSet: Computing capacity for map cachedBlocks
2026-11-11 22:41:50,692 INFO util.GSet: VM type       = 64-bit
2026-11-11 22:41:50,693 INFO util.GSet: 0.25% max memory 1.9 GB = 4.9 MB
2026-11-11 22:41:50,693 INFO util.GSet: capacity      = 2^19 = 524288 entries
2026-11-11 22:41:50,732 INFO metrics.TopMetrics: NNTop conf: dfs.namenode.top.window.num.buckets = 10
2026-11-11 22:41:50,732 INFO metrics.TopMetrics: NNTop conf: dfs.namenode.top.num.users = 10
2026-11-11 22:41:50,733 INFO metrics.TopMetrics: NNTop conf: dfs.namenode.top.windows.minutes = 1,5,25
2026-11-11 22:41:50,747 INFO namenode.FSNamesystem: Retry cache on namenode is enabled
2026-11-11 22:41:50,757 INFO namenode.FSNamesystem: Retry cache will use 0.03 of total heap and retry cache entry expiry time is 600000 millis
2026-11-11 22:41:50,761 INFO util.GSet: Computing capacity for map NameNodeRetryCache
2026-11-11 22:41:50,762 INFO util.GSet: VM type       = 64-bit
2026-11-11 22:41:50,764 INFO util.GSet: 0.029999999329447746% max memory 1.9 GB = 605.2 KB
2026-11-11 22:41:50,764 INFO util.GSet: capacity      = 2^16 = 65536 entries
Re-format filesystem in Storage Directory root= /tmp/hadoop-racoon21_touch/dfs/name; location= null ? (Y or N) Y
2026-11-11 22:42:59,405 INFO namenode.FSImage: Allocated new BlockPoolId: BP-2064774087-127.0.1.1-1794411779381
2026-11-11 22:42:59,405 INFO common.Storage: Will remove files: [/tmp/hadoop-racoon21_touch/dfs/name/current/fsimage_0000000000000000000, /tmp/hadoop-racoon21_touch/dfs/name/current/fsimage_0000000000000000000.md5, /tmp/hadoop-racoon21_touch/dfs/name/current/seen_txid, /tmp/hadoop-racoon21_touch/dfs/name/current/VERSION]
2026-11-11 22:42:59,447 INFO common.Storage: Storage directory /tmp/hadoop-racoon21_touch/dfs/name has been successfully formatted.
2026-11-11 22:42:59,530 INFO namenode.FSImageFormatProtobuf: Saving image file /tmp/hadoop-racoon21_touch/dfs/name/current/fsimage.ckpt_0000000000000000000 using no compression
2026-11-11 22:42:59,905 INFO namenode.FSImageFormatProtobuf: Image file /tmp/hadoop-racoon21_touch/dfs/name/current/fsimage.ckpt_0000000000000000000 of size 409 bytes saved in 0 seconds .
2026-11-11 22:42:59,912 INFO common.Storage: setMostRecentCheckpointInfo txid is 0, time is 1794411779912
2026-11-11 22:42:59,925 INFO namenode.NNStorageRetentionManager: Going to retain 1 images with txid >= 0
2026-11-11 22:42:59,951 INFO blockmanagement.DatanodeManager: Slow peers collection thread shutdown
2026-11-11 22:43:00,033 INFO namenode.FSNamesystem: Stopping services started for active state
2026-11-11 22:43:00,034 INFO namenode.FSNamesystem: Stopping services started for standby state
2026-11-11 22:43:00,044 INFO namenode.FSImage: FSImageSaver clean checkpoint: txid=0 when meet shutdown.
2026-11-11 22:43:00,046 INFO namenode.NameNode: SHUTDOWN_MSG:
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at YoungGC/127.0.1.1
************************************************************/
```

**Pembahasan :**

Pada tahap ini dilakukan proses format NameNode menggunakan perintah hdfs namenode -format. NameNode merupakan komponen utama HDFS yang bertugas mengelola metadata sistem file, seperti informasi direktori, lokasi blok data, dan struktur penyimpanan. Proses format dilakukan untuk menginisialisasi direktori penyimpanan NameNode sebelum layanan HDFS dijalankan. Apabila proses berhasil, Hadoop akan menampilkan pesan bahwa direktori penyimpanan telah berhasil diformat.<br></br>


<h3>6. Atur konfigurasi JAVA_HOME</h3>
Pada sesi ini kita mengatur lokasi instalasi Java pada file hadoop-env.sh.
Langkah 1. Buka file:

```
nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```

Langkah 2. Cari bagian JAVA_HOME.
Jika belum ada atau masih berupa komentar, tambahkan baris berikut:
```
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
```
Lokasi tersebut merupakan lokasi Java 17 yang umum digunakan pada Ubuntu WSL.

Langkah 3. Simpan file dengan Ctrl + O, Enter, kemudian keluar dengan Ctrl + X.

Langkah 4. Periksa lokasi Java:
```
readlink -f $(which java)
```
Output :
```
/usr/lib/jvm/java-17-openjdk-amd64/bin/java
```

**Pembahasan :**

Pada tahap ini dilakukan pengaturan variabel JAVA_HOME pada file hadoop-env.sh. Variabel tersebut berfungsi untuk memberitahukan kepada Apache Hadoop lokasi instalasi Java yang digunakan. Pengaturan ini diperlukan karena Hadoop membutuhkan Java untuk menjalankan berbagai layanan dan proses internalnya. Lokasi Java disesuaikan dengan direktori instalasi JDK 17 pada Ubuntu WSL. Setelah konfigurasi disimpan, lokasi Java diperiksa menggunakan perintah readlink untuk memastikan bahwa direktori Java yang digunakan sudah sesuai.<br></br>


<h3>7. Jalankan daemon untuk HDFS</h3>
Setelah semua konfigurasi selesai, selanjutnya kita menjalankan layanan HDFS.

Langkah 1. Pastikan SSH berjalan:
```
sudo service ssh start
```

Langkah 2. Jalankan daemon HDFS:
```
start-dfs.sh
```
Jika muncul permintaan konfirmasi SSH, masukkan yes apabila diperlukan.

Langkah 3. Periksa proses Hadoop:
```
jps
```
Output :
```
7968 NameNode
8498 Jps
8252 SecondaryNameNode
8078 DataNode
```
Ketiga proses utama tersebut menunjukkan bahwa layanan HDFS sudah berjalan.

Langkah 4. Buka browser Windows dan akses:

Hadoop NameNode Web Interface (localhost:9870)

<img width="615" height="569" alt="3" src="https://github.com/user-attachments/assets/28cba55d-0b92-4da1-a74a-fc58526c48b8" />

Jika berhasil, halaman antarmuka NameNode akan menampilkan informasi mengenai kondisi HDFS, kapasitas penyimpanan, dan jumlah DataNode.

**Pembahasan :**

Pada tahap ini dilakukan proses menjalankan daemon HDFS menggunakan perintah start-dfs.sh. Perintah tersebut digunakan untuk mengaktifkan layanan NameNode, DataNode, dan SecondaryNameNode. Setelah layanan dijalankan, dilakukan pemeriksaan menggunakan perintah jps untuk mengetahui proses Java yang sedang aktif. Apabila NameNode, DataNode, dan SecondaryNameNode muncul pada hasil pemeriksaan, berarti layanan HDFS telah berjalan. Selanjutnya, kondisi HDFS dapat dipantau melalui browser dengan mengakses alamat localhost:9870, yang menampilkan informasi mengenai sistem file terdistribusi Hadoop.<br></br>


<h3>8. Selesai dan pengujian HDFS</h3>

Setelah seluruh layanan berjalan, Hadoop dan HDFS sudah siap digunakan.

Langkah 1. Periksa isi direktori utama HDFS:
```
hdfs dfs -ls /
```

Langkah 2. Buat direktori percobaan:
```
hdfs dfs -mkdir -p /praktikum
```

Langkah 3. Periksa apakah direktori berhasil dibuat:
```
hdfs dfs -ls /
```
Output :
```
Found 1 items
drwxr-xr-x   - racoon21_touch supergroup          0 2026-11-11 23:07 /praktikum
```
Informasi pemilik, grup, dan ukuran dapat berbeda.


Langkah 4. Hentikan layanan HDFS jika praktikum sudah selesai:
```
stop-dfs.sh
```

**Pembahasan :**

Pada tahap akhir pengujian, dilakukan pemeriksaan terhadap sistem file HDFS menggunakan perintah hdfs dfs -ls /. Selanjutnya, dibuat direktori percobaan menggunakan perintah hdfs dfs -mkdir -p /praktikum. Perintah tersebut berfungsi untuk membuat direktori baru pada sistem file terdistribusi Hadoop. Keberhasilan pembuatan direktori diperiksa kembali menggunakan perintah hdfs dfs -ls /. Setelah seluruh pengujian selesai, layanan HDFS dapat dihentikan menggunakan perintah stop-dfs.sh.<br></br>


<h3>Tugas Praktikum</h3>

**Tugas 1. Membuat direktori datasets di HDFS**

Langkah 1. Pastikan HDFS sedang berjalan:
```
start-dfs.sh
```

Langkah 2. Periksa username Ubuntu:
```
whoami
```
Output :
```
racoon21_touch
```

Langkah 3. Buat direktori datasets pada HDFS:
```
dfs dfs -mkdir -p /user/racoon21_touch/datasets
```

Langkah 4. Periksa direktori:
```
hdfs dfs -ls /user/$USER
```
Output :
```
Found 1 items
drwxr-xr-x   - racoon21_touch supergroup          0 2026-11-11 23:18 /user/racoon21_touch/datasets
```

**Pembahasan :**

Pada ini dilakukan pembuatan direktori datasets pada sistem file HDFS dengan lokasi /user/mahasiswa/datasets. Pembuatan direktori dilakukan menggunakan perintah hdfs dfs -mkdir -p, yang berfungsi untuk membuat direktori baru beserta direktori induknya apabila belum tersedia. Penggunaan variabel $USER memungkinkan nama direktori disesuaikan secara otomatis dengan username Ubuntu yang digunakan. Setelah direktori berhasil dibuat, dilakukan pemeriksaan menggunakan perintah hdfs dfs -ls untuk memastikan bahwa direktori datasets telah tersedia pada HDFS.<br></br>


**Tugas 2. Mencari dan mengunggah 3 file CSV ke HDFS**

Langkah 1. Membuat folder penyimpanan dataset di Windows seperti:
```
C:\Users\user\Downloads\datasets
```

Langkah 2. Mengunduh tiga file CSV dari internet.
```
cd /mnt/c/Users/user/Downloads

curl -L -o iris.csv https://raw.githubusercontent.com/logpresso/dataset/master/iris.csv

curl -L -o titanic.csv https://raw.githubusercontent.com/pandas-dev/pandas/master/doc/data/titanic.csv

curl -L -o penguins.csv https://raw.githubusercontent.com/allisonhorst/palmerpenguins/main/inst/extdata/penguins.csv
```
Pastikan ketiga file sudah tersimpan di folder datasets Windows.


Langkah 3. Buka kembali terminal Ubuntu.

Masuk ke folder dataset Windows:
```
cd /mnt/c/Users/user/Downloads/datasets
```

Periksa file:
```
ls
```
cari file dataset ini setelah output dan seperti ini tulisannya :
```
iris.csv
titanic.csv
penguins.csv
```

Langkah 4. Unggah ketiga file ke HDFS.

Jalankan:
```
hdfs dfs -put iris.csv /user/$USER/datasets/

hdfs dfs -put titanic.csv /user/$USER/datasets/

hdfs dfs -put penguins.csv /user/$USER/datasets/
```

Langkah 5. Lalu periksa hasil pengunggahan:
```
hdfs dfs -ls /user/$USER/datasets
```
Output :
```
Found 3 items
-rw-r--r--   1 racoon21_touch supergroup       3716 2026-11-11 23:52 /user/racoon21_touch/datasets/iris.csv
-rw-r--r--   1 racoon21_touch supergroup      15241 2026-11-11 23:53 /user/racoon21_touch/datasets/penguins.csv
-rw-r--r--   1 racoon21_touch supergroup      60080 2026-11-11 23:52 /user/racoon21_touch/datasets/titanic.csv
```

Langkah 6. Terakhir memeriksa isi salah satu file CSV:
```
hdfs dfs -cat /user/$USER/datasets/iris.csv
```
Perintah tersebut akan menampilkan isi file CSV yang telah disimpan di HDFS.


**Pembahasan :**

Pada tugas 2 ini dilakukan pengumpulan tiga file dataset dengan format CSV yang diperoleh dari internet, yaitu iris.csv, titanic.csv, dan penguins.csv. Ketiga file tersebut terlebih dahulu disimpan pada direktori lokal Windows, kemudian diakses melalui Ubuntu WSL. Selanjutnya, file diunggah ke direktori /user/mahasiswa/datasets pada HDFS menggunakan perintah hdfs dfs -put. Perintah tersebut berfungsi untuk memindahkan salinan file dari penyimpanan lokal ke sistem file terdistribusi Hadoop. Setelah proses pengunggahan selesai, dilakukan pemeriksaan menggunakan perintah hdfs dfs -ls untuk memastikan ketiga file telah tersimpan pada direktori tujuan. Perintah hdfs dfs -cat juga digunakan untuk menampilkan isi file CSV secara langsung dari HDFS.<br></br>


<h3>Kesimpulan praktikum</h3>

Berdasarkan praktikum Distributed File System (HDFS) ini, Apache Hadoop merupakan framework yang menyediakan sistem penyimpanan data terdistribusi melalui Hadoop Distributed File System (HDFS). Dalam praktikum ini dilakukan instalasi Java, Apache Hadoop, pdsh, dan SSH, dilanjutkan dengan konfigurasi komponen HDFS dan YARN. Proses inisialisasi NameNode serta menjalankan daemon HDFS dilakukan agar sistem penyimpanan dapat digunakan. Pengujian dilakukan dengan membuat direktori dan mengunggah tiga file CSV ke dalam HDFS.
