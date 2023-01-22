---
title: UT3.PC01 Seguridad y desplegado de aplicaciones con Docker
date: 2023-01-22 17:42:00 PM
categories: [Puesta en producción Alejandro,docker]
tags: [Puesta en produccion Alejandro,docker]     # TAG names should always be lowercase
---

![1_Ezua3p00_2I_NPeo6V50zw.png](/assets/img/UT%20PC01%20Seguridad%20y%20desplegado%20de%20aplicaciones%20con%20bd888f58f864441abe4cf5d48c084d8a/1_Ezua3p00_2I_NPeo6V50zw.png)

## Docker-Bench

**¿Que es docker-bench?**

**Docker Bench** for Security se trata de un script que realiza decenas de pruebas comprobando las buenas prácticas de seguridad para un entorno Productivo.

### Preparación para Docker-Bench.

Primero lo que tenemos que hacer es descargarnos Docker-bench, La formas es muy sencilla ya que solo nos tendremos que descargar un simple Script. (Recomendación: se necesita tener git preinstalado)

```bash
git clone https://github.com/docker/docker-bench-security.git
```

```bash
cd docker-bench-security/
```

```bash
./docker-bench-security.sh
```

### Utilizar Docker-Bench

Comando que se ejecuto para cargar el script.

```bash
sudo sh [docker-bench-security.sh](http://docker-bench-security.sh/)
```

Como podemos aqui se nos muestra el resultado del Docker-bench. Podemos ver que nos salen muchos errores debido a que no tenemos reglas a los ficheros y directorios que Docker recomienda ya que de por si las reglas estas puestas de forma predefinida. Así que nos tenemos que Descargar Auditd.

![Untitled](/assets/img/UT%20PC01%20Seguridad%20y%20desplegado%20de%20aplicaciones%20con%20bd888f58f864441abe4cf5d48c084d8a/Untitled.png)

## Auditd.

Para instalar auditd solo necesitamos poner un comando en la terminal.

```bash
sudo apt-get install auditd
```

Tendremos que editar un fichero que se encuentra en la ruta de /etc/audit/rules.d/audit.rules

```bash
nano **/etc/audit/rules.d/audit.rules

-w /usr/bin/docker -p wa
-w /var/lib/docker -p wa
-w /etc/docker -p wa
-w /lib/systemd/system/docker.service -p wa 
-w /lib/systemd/system/docker.socket -p wa 
-w /etc/default/docker -p wa
-w /etc/docker/daemon.json -p wa
-w /usr/bin/docker-containerd -p wa
-w /usr/bin/docker-runc -p wa
-w /run/containerd -p wa
-w /etc/containerd/config.toml -p wa
-w /usr/bin/containerd -p wa
-w /usr/bin/containerd-shim -p wa
-w /usr/bin/containerd-shim-runc-v1 -p wa
-w /usr/bin/containerd-shim-runc-v2 -p wa
-w /usr/bin/runc -p wa**
```

*“Con “-w” se indica que el fichero debe de ser auditado por “auditd” y “-p wa” indica que se deben generar logs ante cualquier modificación en dichos ficheros o directorios.”*

![Untitled](/assets/img/UT%20PC01%20Seguridad%20y%20desplegado%20de%20aplicaciones%20con%20bd888f58f864441abe4cf5d48c084d8a/Untitled%201.png)

Reiniciaremos Auditd con systemctl 

```bash
sudo systemctl restart auditd
```

Probaremos a hacer otro Docker-bench para ver los cambios y comprobar cuantos warnings nos hemos quitado de encima ya que a nadie le gusta ver tantos errores juntos.

Fotos de antes de la configuración del Auditd:

![Untitled](/assets/img/UT%20PC01%20Seguridad%20y%20desplegado%20de%20aplicaciones%20con%20bd888f58f864441abe4cf5d48c084d8a/Untitled%202.png)

Fotos después de la configuración del **auditd**:

![Untitled](/assets/img/UT%20PC01%20Seguridad%20y%20desplegado%20de%20aplicaciones%20con%20bd888f58f864441abe4cf5d48c084d8a/Untitled%203.png)

Como podemos ver a cambiado bastante. ya tenemos los directorios auditados. Pero aun asi movemos un poco la imagen y hacemos scroll con el raton podemos ver que un poco mas abajo seguimos teniendo algunos errores. Que estos ya se configuran en el archivo de configuración del demonio.

![Untitled](/assets/img/UT%20PC01%20Seguridad%20y%20desplegado%20de%20aplicaciones%20con%20bd888f58f864441abe4cf5d48c084d8a/Untitled%204.png)

## Ajuste de seguridad en el Demonio de Docker.

Algunas opciones pueden fallar porque no estén ya disponibles, por lo que si no se levanta el servicio de Docker. en todo caso vamos a crear un fichero en /etc/docker/daemon.json

Dentro del fichero daemon.json debe ir esto.

```bash
{
"icc": false,
    "userns-remap": "default",
    "log-driver": "syslog", 
    "disable-legacy-registry": true,
    "live-restore": true,
    "userland-proxy": false,
    "no-new-privileges": true
}
```

Como podemos ver hemos minimizado los errores.

![Untitled](/assets/img/UT%20PC01%20Seguridad%20y%20desplegado%20de%20aplicaciones%20con%20bd888f58f864441abe4cf5d48c084d8a/Untitled%205.png)

Para solucionar el error 2.16 la forma de arreglarlo es poniendo.

```bash
 "userland-proxy": false,

```

Para solucionar el error el 2.14

```bash
    "no-new-privileges": true
```

## Analisis de Dockerfile

Elegid cualquier archivo dockerfile que hayáis generado en la actividad *[UT3.AP00a - Afianzando el uso de Dockerfile.](https://educacionadistancia.juntadeandalucia.es/centros/cadiz/mod/assign/view.php?id=454669)*
 y realizar un testeo con Trivy.

Instalacion de trivy

```bash
sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy
```

Ejecucion de trivy es muy sencillo nada mas tenemos que poner trivy image (la imagen a analizar)

Yo he utilizado mi dockerfile 2 web y como podemos ver gracias a trivy sueltas las vulnerabilidades.

![Untitled](/assets/img/UT%20PC01%20Seguridad%20y%20desplegado%20de%20aplicaciones%20con%20bd888f58f864441abe4cf5d48c084d8a/Untitled%206.png)

Como podemos comprobar me aparece que tengo una vulnerabilidad de bash que es el CVE-2022-3715 que básicamente explica que puedo llegar a tener overflow en el buffer y esto me causaría un problemas de memoria.

![Untitled](/assets/img/UT%20PC01%20Seguridad%20y%20desplegado%20de%20aplicaciones%20con%20bd888f58f864441abe4cf5d48c084d8a/Untitled%207.png)

## Análisis de imágenes

Debéis escanear la imagen creada de Wordpress que hayáis generado en la actividad *[UT3.AP03](https://educacionadistancia.juntadeandalucia.es/centros/cadiz/mod/assign/view.php?id=454668)* 
  con Trivy, Snyk o Docker Desktop. A continuación, descargate la versión wordpress:4.6 y realiza el mismo proceso.

Aquí podemos ver que tenemos las 2 imágenes descargadas de wordpress. Ahora con trivy haremos es una análisis de vulnerabilidad.

![Untitled](/assets/img/UT%20PC01%20Seguridad%20y%20desplegado%20de%20aplicaciones%20con%20bd888f58f864441abe4cf5d48c084d8a/Untitled%208.png)

De por si la comparativa esta clara la wordpres version 4.6 tiene un monton de vulnerabilidades.

En el curl, dpkg,  undev, systemd-sysv, libbsd0,   libc-bin, tiene varios Critical y una gran mayoria mas de vulnerabilidades reflejadas. tenemos que entender que es obvio que una version que salio en el 2016 tengas mas vulnerabilidades de un wordpres de ultima. Pero una vulnerabilidad mas notable tambien del wordpres 4.6 sea una vulnerabilidad critica en el mismo Login 

![Untitled](/assets/img/UT%20PC01%20Seguridad%20y%20desplegado%20de%20aplicaciones%20con%20bd888f58f864441abe4cf5d48c084d8a/Untitled%209.png)

foto wordpres 4.6.

![Untitled](/assets/img/UT%20PC01%20Seguridad%20y%20desplegado%20de%20aplicaciones%20con%20bd888f58f864441abe4cf5d48c084d8a/Untitled%2010.png)

Y si vamos bajando por las lista de vulnerabilidades podemos ecnontrarnos fallos tanto en el openssl y en el passwd.

**Wordpres ultima version.**

Con wordpress ultima versión tenemos algunos critical obviamente. todo no se puede arreglar, o son vulnerabilidades nuevas que han salido debido a la actualización. como por ejemplo. 

Libcur14 y libdb5.3

![Untitled](/assets/img/UT%20PC01%20Seguridad%20y%20desplegado%20de%20aplicaciones%20con%20bd888f58f864441abe4cf5d48c084d8a/Untitled%2011.png)

También podemos comprobar que también tiene una vulnerabilidad critica igual en el curl. CVE-2022-32221, igual que el wordpres 4.6.

![Untitled](/assets/img/UT%20PC01%20Seguridad%20y%20desplegado%20de%20aplicaciones%20con%20bd888f58f864441abe4cf5d48c084d8a/Untitled%2012.png)

Con trivy vamos a hacer un filtro por criticidad para ver ambos casos tanto ubuntu 4.6 y ubuntu latest

En el wordpres:Latest Solo nos encontramos con 7 vulnerabildiad criticas.

Comando:

```bash
trivy image --severity CRITICAL wordpress:latest
```

![Untitled](/assets/img/UT%20PC01%20Seguridad%20y%20desplegado%20de%20aplicaciones%20con%20bd888f58f864441abe4cf5d48c084d8a/Untitled%2013.png)

Mientras que en el wordpress 4.6 tenemos: 264 vulnerabilidades criticas 

![Untitled](/assets/img/UT%20PC01%20Seguridad%20y%20desplegado%20de%20aplicaciones%20con%20bd888f58f864441abe4cf5d48c084d8a/Untitled%2014.png)

Una gran diferencia. ya sabemos que debemos actualizar siempre a la ultima versión ;)