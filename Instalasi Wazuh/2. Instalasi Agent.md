### Install wazuh agent 
Seperti wazuh server, script ini harus dijalankan oleh user dengan root privilege. Agent ini saya instal di Webserver DVWA berbasis Ubuntu 20.04

```
sudo su
```

Install GPG key

```
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg
```

Add repository

```
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list
```
Update

```
apt-get update
```

Install dengan menggunakan command

```
WAZUH_MANAGER="10.1.2.32" apt-get install wazuh-agent
```

> 10.1.2.32 adalah IP Address wazuh manager

5. Enable service wazuh agent

```
systemctl daemon-reload
systemctl enable wazuh-agent
systemctl start wazuh-agent
```

>Jika terjadi error saat menjalankan wazuh-agent, periksa isi /var/ossec/etc/ossec.conf bagian ini
```
    <server>
      <address>10.1.2.32</address>
      <port>1514</port>
      <protocol>tcp</protocol>
    </server>
```
>Pastikan isi address sudah benar.

Jika semua proses sudah dijalankan agent akan muncul pada laman Wazuh, dan kita bisa memantau security events pada agent.
