Daftar pada situs virustotal agar dapet mengakses API, lalu simpan API key.
Pada wazuh server, masukkan konfigurasi integrasi berikut pada `/var/ossec/etc/ossec.conf`
```
<integration>
  <name>virustotal</name>
  <api_key>API_KEY</api_key> <!-- Ganti dengan VirusTotal API key -->
  <group>syscheck</group>
  <alert_format>json</alert_format>
</integration>
```
Gunakan FIM untuk memantau directory, konfigurasi ini bisa diterapkan di setiap file `/var/ossec/etc/ossec.conf` milik agent maupun secara terpusat via `agent.conf`
```
<syscheck>
  <directories check_all="yes" realtime="yes">/media/user/software</directories>
</syscheck>
```
Restart wazuh manager
```
systemctl restart wazuh-manager
```
Perhatikan alert pada security events.
