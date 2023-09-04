Yang akan dilakukan adalah memblokir akses ke webserver dari IP tertentu

## Konfigurasi wazuh agent
Install apache webserver
```
sudo apt update
sudo apt install apache2
```
Jika UFW aktif, gunakan perintah ini untuk melewatkan traffic
```
sudo ufw status
sudo ufw app list
sudo ufw allow 'Apache'
```
Cek status webserver service
```
sudo systemctl status apache2
```
Jika aktif, cek akses menggunakan curl
```
curl http://<IP Address>
```
Masukkan konfigurasi berikut pada file `/var/ossec/etc/ossec.conf` agent agar access log terbaca oleh wazuh
```
<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/apache2/access.log</location>
</localfile>
```
Restart wazuh agent
```
systemctl restart wazuh-agent
```

## Konfigurasi wazuh server
Pastikan wget sudah terinstall
```
sudo apt update && sudo apt install wget -y
```
Download daftar IP Address dari Alienvault
```
sudo wget https://raw.githubusercontent.com/firehol/blocklist-ipsets/master/alienvault_reputation.ipset -O /var/ossec/etc/lists/alienvault_reputation.ipset
```
Masukkan IP address host/VM yang akan diuji
```
sudo echo "<ATTACKER_IP>" >> /var/ossec/etc/lists/alienvault_reputation.ipset
```
Download script untuk ubah ipset ke cdb list
```
sudo wget https://wazuh.com/resources/iplist-to-cdblist.py -O /tmp/iplist-to-cdblist.py
```
Convert ipset ke cdb list menggunakan tools yang sudah di-download
```
sudo /var/ossec/framework/python/bin/python3 /tmp/iplist-to-cdblist.py /var/ossec/etc/lists/alienvault_reputation.ipset /var/ossec/etc/lists/blacklist-alienvault
```
Assign permission
```
sudo chown wazuh:wazuh /var/ossec/etc/lists/blacklist-alienvault
```
Tambahkan konfigurasi ini pada file local rules `/var/ossec/etc/rules/local_rules.xml` agar dapat memicu `active-response`
```
<group name="attack,">
  <rule id="100100" level="10">
    <if_group>web|attack|attacks</if_group>
    <list field="srcip" lookup="address_match_key">etc/lists/blacklist-alienvault</list>
    <description>IP address found in AlienVault reputation database.</description>
  </rule>
</group>
```
Tambahkan cdb list pada segmen ruleset dalam `/var/ossec/etc/ossec.conf`
``` #10
<ossec_config>
  <ruleset>
    <!-- Default ruleset -->
    <decoder_dir>ruleset/decoders</decoder_dir>
    <rule_dir>ruleset/rules</rule_dir>
    <rule_exclude>0215-policy_rules.xml</rule_exclude>
    <list>etc/lists/audit-keys</list>
    <list>etc/lists/amazon/aws-eventnames</list>
    <list>etc/lists/security-eventchannel</list>
    <!-- alienvault list -->
    <list>etc/lists/blacklist-alienvault</list>

    <!-- User-defined ruleset -->
    <decoder_dir>etc/decoders</decoder_dir>
    <rule_dir>etc/rules</rule_dir>
  </ruleset>

</ossec_config>
```
Pada `/var/ossec/etc/ossec.conf` tambahkan rule id 100100 pada segmen active response untuk mengaktifkan blokir.
```
<ossec_config>
  <active-response>
    <command>firewall-drop</command>
    <location>local</location>
    <rules_id>100100</rules_id>
    <timeout>60</timeout>
  </active-response>
</ossec_config>
```
Restart wazuh manager
```
sudo systemctl restart wazuh-manager
```
Uji rules dengan melakukan akses dari attacker IP.
