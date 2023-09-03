Use case untuk monitoring dan alert secara realtime : 
1. Monitor perubahan pada directory `/home/admin-server`
   Tambahkan konfigurasi ini pada `/var/ossec/etc/ossec.conf` pada agent
   ```
   <directories check_all="yes" report_changes="yes" realtime="yes">/home/admin-server</directories>
   ```
   Restart wazuh-agent
   ```
   systemctl restart wazuh-agent
   ```
   Buat file pada directory tersebut
   ```
   touch /home/admin-server/myfile.txt
   ```
   Masukkan data pada file tersebut
   ```
   echo "hello world" >> /home/admin-server/myfile.txt
   ```
   Perhatikan Security events pada Wazuh Dashboard
3. Monitor perubahan pada file critical `/etc/passwd`
   Tambahkan konfigurasi ini pada `/var/ossec/etc/ossec.conf` pada agent
   ```
   <directories check_all="yes" report_changes="yes" realtime="yes">/etc/passwd</directories>
   ```
   Restart wazuh-agent
   ```
   systemctl restart wazuh-agent
   ```
   Buat user baru
   ```
   adduser myssql
   ```
   Perhatikan log pada security events
