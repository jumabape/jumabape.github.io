---
title: TryHackMe EternalBlue
date: 2023-03-13 10:12:00 AM
categories: [hacking etico,MaquinasH TryHackMe]
tags: [Puesta en produccion,Tryhackme, EternalBlue]     # TAG names should always be lowercase
---

### Enumeracion de puertos.

| Puertos | Servicios |
| --- | --- |
| 135/tcp | Msrpc |
| 139/tcp | Netbios-ssn |
| 445/tcp | Microsoft-ds |
| 3389/tcp | ssl/ms-wbt-server |
| 49152/tcp 
49153/tcp 
49154/tcp
49158/tcp
49159/tcp  | open msrpc Microsoft Windows RPC |

Haciendo una escaneo de vulnerabilidad al puerto 445 nos sale que es vulnerable. 

```bash
nmap -p 445 -script vuln 10.10.150.30
```

Esto es el resultado del NMAP 

```bash

Host script results:
|_samba-vuln-cve-2012-1182: NT_STATUS_ACCESS_DENIED
|_smb-vuln-ms10-054: false
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|_      https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|_smb-vuln-ms10-061: NT_STATUS_ACCESS_DENIED
```

Podemos ver que tenemos en el puerto 445 tenemos un samba. que tiene una vulnerabilidad que si miramos el CVE:CVE-2017-0143 que es posible que es la Ejecución de código remoto en Samba.

En este caso vamos a necesitar usar un Exploit que nos permita tener acceso remotamente a la maquina. utilizaremos el metasploit.  Para poder abrir metasploit simplemente es poniendo msfconsole en la terminal ya se nos abre. 

![Untitled](/assets/img/Maquina%20EternalBlue%2001ba2bba9a2943748df3874bb375569b/Untitled.png)

con search y buscando por el CVE nos hace la vida un poco mas facil a la hora de elegir modulo, En este caso seleccionaremos el primero.

![Untitled](/assets/img/Maquina%20EternalBlue%2001ba2bba9a2943748df3874bb375569b/Untitled%201.png)

Si escribimos options en la terminal nos aparecera como configurar el metasploit. en este caso hemos tenido que escoger y cambiar la ip del RHOSTS y el Payload options tenemos que seleccionar el windows/x64/shell/reverse_tcp luego en el LHOST en mi caso tuve que poner la ip de la vpn de tryhackme. 

![Untitled](/assets/img/Maquina%20EternalBlue%2001ba2bba9a2943748df3874bb375569b/Untitled%202.png)

Lanzamos el exploit.  Solo nos tocara esperar un rato y ya estaremos dentro.

![Untitled](/assets/img/Maquina%20EternalBlue%2001ba2bba9a2943748df3874bb375569b/Untitled%203.png)

Pero lo que haremos ahora será abrir una terminal meterprete. lo que es una aplicación predefinida que la cargamos en memoria atraves de un payloads. Esta nos va a permitir a llegar a hacer cosas mucho mas faciles y sencillas.

Asi que con la Sesion de windows abiertas le haremos un background para dejarla en segundo plano. Como podemos ver si ponemos session. tenemos la sesion de background 

![Untitled](/assets/img/Maquina%20EternalBlue%2001ba2bba9a2943748df3874bb375569b/Untitled%204.png)

Ahora pasaremos a utilizar el meterprete. en este apartado si nos metemos en options solo tendríamos que cambiar solo una opción que la sesión sea 1.

![Untitled](/assets/img/Maquina%20EternalBlue%2001ba2bba9a2943748df3874bb375569b/Untitled%205.png)

![Untitled](/assets/img/Maquina%20EternalBlue%2001ba2bba9a2943748df3874bb375569b/Untitled%206.png)

Utilizando hashdump nos daran todas las contraseñas. pero las contraseñas estan hasheadas en NTP 

comando hashdump:

![Untitled](/assets/img/Maquina%20EternalBlue%2001ba2bba9a2943748df3874bb375569b/Untitled%207.png)

comando hashcat:

![Untitled](/assets/img/Maquina%20EternalBlue%2001ba2bba9a2943748df3874bb375569b/Untitled%208.png)

Resultado

![Untitled](/assets/img/Maquina%20EternalBlue%2001ba2bba9a2943748df3874bb375569b/Untitled%209.png)

Las flags estan encondidas por varios sitios:

![Untitled](/assets/img/Maquina%20EternalBlue%2001ba2bba9a2943748df3874bb375569b/Untitled%2010.png)

Strings en el metarprete es capaz de buscar todo.

locate tambien funciona.