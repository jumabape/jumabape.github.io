---
title: Tryhackme Easy Peasy
date: 2022-12-15 18:24:00 PM
categories: [hacking etico,Maquinas]
tags: [hacking-etico,Tryhackme, Easy Peasy]     # TAG names should always be lowercase
---

## Comenzamos

**Comprobación de puertos y versiones**

**Utilizando la herramienta nmap**

```bash
**nmap -sC -sV -p- 10.10.188.208**
```

![Untitled](/assets/img/Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/Untitled.png)

Empezamos entrando en la página web del nginx y el apartado robots.txt

Para buscar a ver si encontramos algo en el código fuente

Ahora estamos haciendo un fuzzing (Fuerza bruta de directorios)

```bash
gobuster dir -u http://10.10.188.208 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

![Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image1.png](/assets/img/Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image1.png)

Hemos encontrado un directorio sospechoso, si entramos en dicho directorio podemos ver

![Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image2.png](/assets/img/Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image2.png)

Mientras le seguimos haciendo fuzzing al directorio principal de la pagina, abriendo otra terminal haremos otro fuzzing al director hidden

![Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image3.png](/assets/img/Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image3.png)

Esperando un tiempo nos aparece en el directorio hidden, un subdirectorio

![Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image4.png](/assets/img/Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image4.png)

Dentro de esta página nueva podemos pensar que está todo muy normal sin más. Si miramos en el código fuente podemos ver que nos aparece un párrafo en formato hidden.

![Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image5.png](/assets/img/Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image5.png)

![Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image6.png](/assets/img/Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image6.png)

Vemos un  párrafo que esta de forma oculta, y que tiene una lista de caracteres podemos deducir que podría ser un base64 debido a que justo en la terminación podemos ver que tienen dos signos de igual. utilizando el comando base64 -d podemos sacar dicha bandera

![Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image7.png](/assets/img/Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image7.png)

Y tendríamos la primera flag, Ahora vamos a enfocarnos en el servidor apache en si. de primeras si nos fijamos en el código fuente o directamente leyendo en la pagina nos aparecería otra venderá 

![Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image8.png](/assets/img/Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image8.png)

Aquí tenemos ya una flag.

Si nos vamos a la página principal del apache nos aparecerá un párrafo que está en formato hidden oculto.

![Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image9.png](/assets/img/Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image9.png)

Nos aparece un código que tendríamos desencriptar que es si lo desencriptamos en base62 nos aparecerá el directorio oculto.

/n0th1ng3ls3m4tt3r Es la pagina oculta del apache asi que decidimos entrar dentro

![Untitled](/assets/img/Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/Untitled%201.png)

En esta página como podemos ver nos aparece un texto un poco escondido que no se ve a simple vista

![Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image10.png](/assets/img/Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image10.png)

Gracias a que tryhackme nos deja un diccionario, cual utilizaremos para descfirar el hash

![Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image11.png](/assets/img/Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image11.png)

y nos dará una contraseña bastante curiosa

![Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image12.png](/assets/img/Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image12.png)

Ya que esta pagina tiene una foto puesta de una forma peculiar,Descargamos la foto ya que parece bastante sospechoso y lo que haremos es con steghide intentar extraer algún archivo

![Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image13.png](/assets/img/Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/image13.png)

Nos extrae el fichero secrettext.txt con la contraseña que es mypasswordforthatjob

El usuario y la contraseña del ssh

username:boring

Nos dara el password en un texto en binario, con cualquier aplicación o pagina web podemos transformar de binario a texto normal

![Untitled](/assets/img/Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/Untitled%202.png)

iconvertedmypasswordtobinary

**synt{a0jvgf33zfa0ez4y}**

![Untitled](/assets/img/Maquina%20Easy%20Peasy%20b395bf9a84f34708b7e2b172b3ede618/Untitled%203.png)

utilizar el cifrado cesar con rot13

**FLAG{N0WITS33MSN0RM4L}**

## Proximamente en proceso