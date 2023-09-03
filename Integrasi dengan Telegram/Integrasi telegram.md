# Integrasi wazuh dan telegram
Kirim wazuh alert melalui telegram menggunakan telegram bot.

1. Buat Telegram bot dan simpan **API KEY** dan **CHAT ID**, gunakan link ini untuk mendapatkan CHAT ID : `https://api.telegram.org/bot<HTTP-API>/getUpdates`.

2. Cek library tang dibutuhkan :
```
#pip install requests
```

3. Masukkan **CHAT ID** ke dalam `custom-telegram.py`. Copy `custom-telegram` dan `custom-telegram.py` ke dalam `/var/ossec/integrations/`

4. Atur permission untuk 2 file tersebut:
```
#chown root:wazuh /var/ossec/integrations/custom-telegram*
#chmod 750 /var/ossec/integrations/custom-telegram*
```

5. Masukkan API KEY ke dalam blok konfigurasi ini dan copy blok ke dalam file `/var/ossec/etc/ossec.conf`
```
    <integration>
        <name>custom-telegram</name>
        <level>3</level>
        <hook_url>https://api.telegram.org/bot<API_KEY>/sendMessage</hook_url>
        <alert_format>json</alert_format>
    </integration>
```
6. Restart wazuh server
```
#systemctl restart wazuh-manager
```
