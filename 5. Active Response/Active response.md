Konfigurasi pada `/var/ossec/etc/ossec.conf`
```
<command>  
   <name>firewall-drop</name>
   <executable>firewall-drop</executable>
   <timeout_allowed>yes</timeout_allowed>
</command>
```

tambahkan parameter ini
```
<!-- Active response firewall-drop scanning -->
<active-response>
  <command>firewall-drop</command>
  <location>all</location>
  <rules_id>31151</rules_id>
  <timeout>3600</timeout>
</active-response>
```
location : all, maksudnya IP attacker ditolak untuk semua agent, jika ingin ke target saja, gunakan `local`
rules_id : rules id yang memicu firewall-drop
timeout : dalam second

Selain menggunakan `rules_id`, bisa juga menggunakan `rules_group`, juga ditambahkan `level`

Restart wazuh manager
```
systemctl restart wazuh-manager
```

Jika ingin memblokir penyerang berulang bisa tambahkan konfigurasi untuk repeat offender pada file `/var/ossec/etc/ossec.conf` pada agent.
```
<active-response>
  <disabled>no</disabled>
  <repeated_offenders>1,5,10</repeated_offenders>
</active-response>
```

repeat offender dalam satuan menit
