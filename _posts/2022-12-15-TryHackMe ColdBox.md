---
title: TryHackMe ColdBox
date: 2022-12-15 17:42:00 PM
categories: [Puesta en producción,MaquinasP]
tags: [Puesta en produccion,Coldbox]     # TAG names should always be lowercase
---

# ColddBox: Easy

Comprobamos primero si tenemos conectividad con la maquina. asi que le vamos a hacer un ping

![Untitled](/assets/img/ColddBox%20Easy%20c67b2186c5804fa584c7d6ba06d4b90f/Untitled.png)

Luego de ver que tenemos conectividad con la maquina vamos a hacerle un escaneo a la maquina, para ver que puerto tiene abierto y que servicios tiene

<aside>
🖥️ map -sC -sV -p- 10.10.11.97

</aside>

![Untitled](/assets/img/ColddBox%20Easy%20c67b2186c5804fa584c7d6ba06d4b90f/Untitled%201.png)

Podemos ver que tiene el puerto 80 con un apache 2.4.18 que dentro de ese apache tiene instalado un serivdor wordpress version 4.1.31.

Luego en el puerto 4512 se puede ver que tiene un SSH 

Ahora ya que vemos que tiene un apache, vamos a hacer una escalada de directorio.

<aside>
🖥️ gobuster dir -u [http://10.10.11.97](http://10.10.11.97/) -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

</aside>

![Untitled](/assets/img/ColddBox%20Easy%20c67b2186c5804fa584c7d6ba06d4b90f/Untitled%202.png)

Como podemos comprobar nos aparece un directorio /hidden en mientras por detras le estamos haciendo tambien una escalada de directorios al directorio /hidden.

Vamos a hacer un escaneo al Wordpress gracias a la aplicaccion WPSCAN. con esta aplicación podemos buscar vulnerabilidades.

![Untitled](/assets/img/ColddBox%20Easy%20c67b2186c5804fa584c7d6ba06d4b90f/Untitled%203.png)

En este caso no nos da ningún plugin vulnerable

<aside>
🖥️ wpscan --url [http://10.10.11.97/](http://10.10.11.97/) --enumerate u

</aside>

Vamos a hacer otro escaneo con esta aplicación pero esta vez para que nos saque los usuarios

![Untitled](/assets/img/ColddBox%20Easy%20c67b2186c5804fa584c7d6ba06d4b90f/Untitled%204.png)

Como podemos ver nos saca philip, c0ld y hugo.

Ahora con esta misma aplicación vamos a hacer fuerza bruta a los usuarios

<aside>
🖥️ wpscan --url [http://10.10.11.97/](http://10.10.11.97/) --usernames c0ldd --passwords /usr/share/wordlists/rockyou.txt

</aside>

![Untitled](/assets/img/ColddBox%20Easy%20c67b2186c5804fa584c7d6ba06d4b90f/Untitled%205.png)

En la primera prueba como podemos ver nos a sacado la contraseña de el usuario c0ldd pero vamos a probar a hacerlo si diera el caso de que no tenemos usuarios.

<aside>
🖥️ wpscan --url [http://10.10.11.97/](http://10.10.11.97/) --passwords /usr/share/wordlists/rockyou.txt

</aside>

Ahora ya dentro del wordpress iniciamos sesion con el usuario c0ldd con la contraseña que sacamos anteriormente

![Untitled](/assets/img/ColddBox%20Easy%20c67b2186c5804fa584c7d6ba06d4b90f/Untitled%206.png)

Ahora nos vamos a descargar un shell desde la pagina [https://pentestmonkey.net/](https://pentestmonkey.net/)  ponemos nuestra ip de la vpn y un puerto abierto 

![Untitled](/assets/img/ColddBox%20Easy%20c67b2186c5804fa584c7d6ba06d4b90f/Untitled%207.png)

Ponemos nuestro kali en modo escucha para que la shell se pueda conectar a nuestro ordenador

nc -vnlp 8080

![Untitled](/assets/img/ColddBox%20Easy%20c67b2186c5804fa584c7d6ba06d4b90f/Untitled%208.png)

Como podemos ver ya tenemos acceso a la maquina atraves de terminal

![Untitled](/assets/img/ColddBox%20Easy%20c67b2186c5804fa584c7d6ba06d4b90f/Untitled%209.png)