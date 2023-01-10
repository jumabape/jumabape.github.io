---
title: Bounty Hacker
date: 2023-01-10 13:00:00 PM
categories: [hacking etico,MaquinasH]
tags: [hacking-etico,Tryhackme, Easy Peasy]     # TAG names should always be lowercase
---
# Bounty Hacker

![Untitled](/assets/img/Bounty%20Hacker%20ec0e9f0da386470299cce31fa66d9018/Untitled.png)

Como primer punto vamos a escanear los puertos de nuestra maquina de tryhackme y ver que vulnerabilidad puede tener

```bash
nmap -A -T5 10.10.254.148
```

![Untitled](/assets/img/Bounty%20Hacker%20ec0e9f0da386470299cce31fa66d9018/Untitled%201.png)

Vamos a mirar primero el ftp y intentar conectarnos

![Untitled](/assets/img/Bounty%20Hacker%20ec0e9f0da386470299cce31fa66d9018/Untitled%202.png)

y nos podremos conectarnos como anonymos ya que sabemos que el ftp el login esta activado en anonymous

![Untitled](/assets/img/Bounty%20Hacker%20ec0e9f0da386470299cce31fa66d9018/Untitled%203.png)

Ahora hacemos un get para descargar el archivo task.txt y el locks.txt

![Untitled](/assets/img/Bounty%20Hacker%20ec0e9f0da386470299cce31fa66d9018/Untitled%204.png)

como podemos ver en el archivo dentro de task.txt. podemos ver que alfinal del archivo.txt pone lin y asi sabremos quien escribió el fichero en este caso. Viendo dentro del archivo de texto lock.txt podemos ver que alparecer tienen unas especies de caracteres y frases. que podríamos utilizar para ver si son contraseñas. Asi que vamos a probar a hacerle un ataque por fuerza bruta con hydra al servicio ssh que esta alojado en el puerto 22

```bash
hydra -l lin -P locks.txt 10.10.254.148 ssh
```

![Untitled](/assets/img/Bounty%20Hacker%20ec0e9f0da386470299cce31fa66d9018/Untitled%205.png)

la contraseña de lin es RedDr4gonSynd1cat3, asi que ahora probaremos a iniciar en el ssh

poniendo ssh lin@10.10.254.148 podremos entrar de por si ya en la maquina y empezar a trastear

![Untitled](/assets/img/Bounty%20Hacker%20ec0e9f0da386470299cce31fa66d9018/Untitled%206.png)

Si probamos y hacemos un LS en el directorio donde estamos. podemos ver que tenemos un fichero llamado user.txt, le hacemos un cat y podemos ver que dentro del fichero users.txt nos aparece esta flag **THM{CR1M3_SyNd1C4T3}**

Ahora  tendremos que probar a hacer una escalada de privilegios. haciendo sudo -l gracias a la pagina de  GTFObins. nos copiamos el comando ya que vimos que podiamos hacer escalada de privilegios con tar

![Untitled](/assets/img/Bounty%20Hacker%20ec0e9f0da386470299cce31fa66d9018/Untitled%207.png)

ya siendo root vamos a la raiz del sistema. nos metemos en la carpeta root y un cat al fichero root.txt

![Untitled](/assets/img/Bounty%20Hacker%20ec0e9f0da386470299cce31fa66d9018/Untitled%208.png)

y aqui tendremos ya la flag de root