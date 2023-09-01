Log in sebagai root, karena script instalasi harus dijalankan oleh akun root. Pindah ke home directory (atau `/tmp`) dan download Wazuh Installation script and file konfigurasi awal.
```
sudo su
cd /tmp
curl -sO https://packages.wazuh.com/4.5/wazuh-install.sh
curl -sO https://packages.wazuh.com/4.5/config.yml
```
Edit `config.yml`, masukkan IP Address yang sesuai untuk setiap wazuh-indexer, wazuh-server, dan wazuh-manager, untuk instalasi pada satu server, bisa ditulis IP Address yang sama.
```
nodes:
  # Wazuh indexer nodes
  indexer:
    - name: node-1
      ip: 10.1.2.32
    #- name: node-2
    #  ip: <indexer-node-ip>
    #- name: node-3
    #  ip: <indexer-node-ip>

  # Wazuh server nodes
  # If there is more than one Wazuh server
  # node, each one must have a node_type
  server:
    - name: wazuh-1
      ip: 10.1.2.32
    #  node_type: master
    #- name: wazuh-2
    #  ip: <wazuh-manager-ip>
    #  node_type: worker
    #- name: wazuh-3
    #  ip: <wazuh-manager-ip>
    #  node_type: worker

  # Wazuh dashboard nodes
  dashboard:
    - name: dashboard
      ip: 10.1.2.32
```

Jalankan script dengan option `--generate-config-files` untuk meng-generate Wazuh cluster key, certificates, dan password yang dibutuhkan untuk instlasi. Setelah script dijalankan, file instalasi disimpan pada file tar bernama `./wazuh-install-files.tar`.

```
bash wazuh-install.sh --generate-config-files
```

> Jika anda mengkonfigurasi wazuh-indexer, wazuh-server, wazuh-manager pada mesin yang berbeda (beda VM atau beda container), copy file `./wazuh-install-files.tar` kepada mesin lain dengan menggunakan perintah `scp`.

> Untuk melihat password admin, jalankan perintah

```
tar -axf wazuh-install-files.tar wazuh-install-files/wazuh-passwords.txt -O | grep -P "\'admin\'" -A 1
```

Install wazuh indexer dengan cara menjalankan script instalasi dengan option `--wazuh-indexer <nodes-name>`. Ganti `<nodes-name>` dengan nama yang ditulis dalam `config.yml` pada langkah 3, dalam hal ini saya menggunakan nama node-1.

```
bash wazuh-install.sh --wazuh-indexer node-1
```

> Jika anda mengkonfigurasi wazuh-indexer, wazuh-server, wazuh-manager pada mesin yang berbeda (beda VM atau beda container), jalankan perintah tersebut di setiap mesin/VM/container.

Inisiasi cluster dengan cara menjalankan script instalasi dengan option `--start-cluster`.

```
bash wazuh-install.sh --start-cluster
```

> Perintah ini hanya dijalankan sekali saja, jadi tidak perlu dijalankan pada setiap nodes (untuk lebih dari satu server).

Jalankan perintah di bawah ini untuk menguji keberhasilan instalasi. Ganti `<ADMIN_PASSWORD>` dengan output dari notes langkah ke-4. Dan ganti `<WAZUH_INDEXER_IP>` dengan IP Address yang ditulis pada `config.yml`.

```
curl -k -u admin:<ADMIN_PASSWORD> https://10.1.2.32:9200
```

Jika instalasi sudah benar, output yang didapat adalah seperti di bawah ini

```
{
  "name" : "node-1",
  "cluster_name" : "wazuh-indexer-cluster",
  "cluster_uuid" : "ZJEkN6M3SKKFt6RJySvA7g",
  "version" : {
    "number" : "7.10.2",
    "build_type" : "rpm",
    "build_hash" : "7203a5af21a8a009aece1474446b437a3c674db6",
    "build_date" : "2023-02-24T18:57:04.388618985Z",
    "build_snapshot" : false,
    "lucene_version" : "9.5.0",
    "minimum_wire_compatibility_version" : "7.10.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "The OpenSearch Project: https://opensearch.org/"
}
```

Install wazuh-server, jalankan perintah ini

```
bash wazuh-install.sh --wazuh-server wazuh-1
```

Jika tidak ada pesan error, lanjut ke langkah berikutnya.

Install wazuh dashboard dengan menjalankan perintah

```
bash wazuh-install.sh --wazuh-dashboard dashboard
```

Instalasi berhasil jika di akhir ada pesan seperti ini:

```
INFO: --- Summary ---
INFO: You can access the web interface https://<wazuh-dashboard-ip>
   User: admin
   Password: <ADMIN_PASSWORD>

INFO: Installation finished.
```

Lihat password yang digunakan selama instalasi dengan perintah

```
# tar -O -xvf wazuh-install-files.tar wazuh-install-files/wazuh-passwords.txt
```

> Jika anda bekerja di directory /tmp, segera copy file instalasi ke tempat yang bisa dijangkau, directory /tmp akan dibersihkan setiap 10 hari

Akses wazuh melalui web browser.
