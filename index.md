# Informe. Práctica 1: Configuración de máquina virtual en el IaaS.
### Desarrollo de Sistemas Informáticos
#### ADRIAN HERNANDEZ SUAREZ - alu0101235516@ull.edu.es

### _**Introducción.**_

Esto es un informe para poder llevar a cabo la **práctica 1 de Desarrollo de Sistemas Informáticos**, hemos aprendido a modificar los **hosts** de una maquina para así poder ejecutar `SSH` sin tener que introducir la contraseña ni el usuario. A parte hemos configurado **git** en la maquina virtual del **IaaS** para vincular el **GitHub** con la máquina y para así poder también configurar el `prompt` de la terminal para que aparezca la rama actual.

### _**Objetivos.**_

Los objetivos de esta práctica han sido configurar la maquina virtual en el IaaS, además de instalar y configurar todas las herramientas necesarias para comenzar a trabajar la asignatura.

### _**Pasos a seguir para la configuración de la **máquina virtual del IaaS**.**_
##### 1. Configurar el servicio VPN de la ULL.

  Para conectarnos remotamente al servicio IaaS desde fuera de la red universitaria, es necesario conectarse a la VPN de la ULL. Para ello, accederemos a la [CONFIGURACION VPN](https://www.ull.es/servicios/stic/2020/12/01/servicio-de-vpn-de-la-ull/) de la ULL y descargaremos las instrucciones correspondientes a nuestro sistema operativo, si las seguimos, nos conectaremos a la red de la universidad remotamente.
  
##### 2. Acceso al IaaS.

  Una vez conectada la VPN deberemos acceder al [Servicio IaaS de la ULL](https://iaas.ull.es/ovirt-engine/sso/login.html) e introducir nuestras credenciales. A continuación encontraremos todas las máqinas virtuales que tenemos, seleccionaremos la que se llama *DSI*, y pulsaremos **Ejecutar** para iniciar la máquina.
Una vez iniciada la máquina virtual, en la parte derecha de la interfaz, donde se indica *Interfaces de red*, encontraremos la IP asignada a la interfaz de nuestra máquina.

##### 3. Acceder por ssh a la máquina y cambiar la contraseña.

  Posteriormente, copiaremos la IP de la máquina virtual para conectarnos a ella por `SSH` a través de nuestra máquina local, por lo que ejecutaremos el siguiente comando desde una consola de nuestra máquina local:

```bash
  ssh usuario@10.6.XXX.XXX
```

  Cuando accedamos nos saldrá lo siguiente:

```bash
  The authenticity of host '10.6.XXX.XXX (10.6.XXX.XXX)' can't be established.
  ECDSA key fingerprint is SHA256:XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX.
  Are you sure you want to continue connecting (yes/no/[fingerprint])?
```
  Le daremos a `yes` y continuaremos.

  Después, te pide que introduzcas la contraseña, la cual es `usuario` y una vez introducida el propio sistema te pedirá que cambies la contraseña. Para ello te hará introducir la contraseña actual (`usuario`) y posteriormente tendras que ponerle una constraseñe nueva, una primera vez para establecerla y la segunda para confirmarla.

  Posteriormente se nos habrá cerrado la sesión por lo que nos conectaremos de nuevo a través de `ssh` e introduciremos la contraseña nueva.

##### 4. Modificar el nombre del host de la máquina virtual.

  Para hacer esto modificaremos el fichero `/etc/hostname`, el cual contiene el nombre del host de la máquina (el cual sera `ubuntu`) y lo cambiaremos por el nombre que queramos, en mi caso `iaas-dsi`. Para hacer esto ejecutaremos los siguientes comandos:

```bash
  # Mostramos el nombre de la máquina
  usuario@ubuntu:~$ cat /etc/hostname
  ubuntu
  
  # Lo cambiamos editando el fichero
  usuario@ubuntu:~$ sudo vi /etc/hostname
  
  # Mostramos el nombre nuevamente para comprobar que se haya cambiado
  usuario@ubuntu:~$ cat /etc/hostname
  iaas-dsi
```

A parte de esto, tambien deberemos modificar ciertos parámetros en el fichero `/etc/hosts`:

```bash
  # Mostramos el contenido del fichero (las ip seguido de los nombres de los hosts)
  usuario@ubuntu:~$ cat /etc/hosts
  127.0.0.1	localhost
  127.0.1.1	ubuntu
  ...

  # Editamos el fichero para cambiar el nombre del host local (ubuntu) por el que habíamos puesto
  usuario@ubuntu:~$ sudo vi /etc/hosts

  # Mostramos el contenido del fichero nuevamente para comprobar que se haya cambiado
  usuario@ubuntu:~$ cat /etc/hosts
  127.0.0.1	localhost
  127.0.1.1	iaas-dsi
  ...
```

Ya hemos cambiado el nombre de host antiguo `ubuntu`, por el nuevo `iaas-dsi`.

  Actualizamos la máquina:

```bash
  usuario@ubuntu:~$ sudo apt update
  ...
  usuario@ubuntu:~$ sudo apt upgrade
  ...
```

  Y la reiniciamos:

```bash
  usuario@ubuntu:~$ sudo reboot
  Connection to 10.6.XXX.XXX closed by remote host.
  Connection to 10.6.XXX.XXX closed.
```

##### 6. Añadir como host de la maquina local la máquina virtual.

  Antes de volver a conectarnos a la máquina virtual, editaremos el fichero `/etc/hosts` de la máquina local para incluir la información de conexión a la máquina virtual y así poder conectarnos directamente con el nombre de usuario y el host.

```bash
  # Mostramos el contenido del fichero (las ip seguido de los nombres de los hosts)
  adrian@ordenador:~$ cat /etc/hosts
  127.0.0.1	localhost
  127.0.1.1	ordenador
  ...
  
  # Editamos el fichero para añadir la línea '10.6.XXX.XXX	iaas-dsi' con el nombre que queramos, en mi caso 'iaas-dsi'
  adrian@ordenador:~$ sudo vi /etc/hosts

  # Mostramos el contenido del fichero nuevamente para comprobar que se haya cambiado
  adrian@ordenador:~$ cat /etc/hosts
  127.0.0.1	localhost
  127.0.1.1	ordenador
  10.6.XXX.XXX	iaas-dsi
  ...
```

##### 7. Configuración de la clave pública.

Para comprobar si ya tenemos generada una clave pública-privada ejecutaremos el comando:

```bash
  adrian@ordenador:~$ cat .ssh/id_rsa.pub 
  ssh-rsa MyjaxGqzdb0yWOk1/HYjh4OB8c4hVC1yc2EAAAADAQABAAABgQDhVLdhoSjuanwqdKnXeTo7U6M0Clcf3upzQ5Y9AXbJl04FoFgLQrcvlbnpPlXCzp40EiyCRkSPTUXHosXK6TU3phvnPffdBSxWU9xROk7/ZkgpBvDKwAlg4JXwvFkQq+WCmmazC6/+3kOhkQw4SWkBvzz1vf5LMDOF1Ja8j2isWwxebgSf9nxRlbDaaRXa1mkk0EiyCRkSPTUXHosXK6TU3phvnPffdBSxWU9xROk7/Zkwd966drZ8edUE/Q5gDxk+9umHzDYZ729yV7LE6IWPyJ5sxfhP6acdbHiDIqiYm4Z8in84OB8c4hVtVAkIIhRqecC3RYf52Sv6UgRZNj0QDNUcHksPXY1OhsctPgXEXD2OIxuL9lHxPmD2Jt5cFCBoux7yRUth8/nho43OYUyv79y4OB8c4hVF6ZAZ0wvabfBvFa2m4k8u0EiyCRkSPTUXHosXK6TU3phvnPffdBSxWU9xROk7/ZkSZoYFlG8tuKK5GPMz0= adrian@ordenador
```

En el caso de que nos salga que el fichero no existe deberemos ejecutar lo siguiente:

```bash
  adrian@ordenador:~$ ssh-keygen
```

Tomaremos los valores por defecto, para ello pulsaremos haremos "skip" de lo que nos vaya saliendo. Una vez generadas las claves, ejecutaremos el siguiente comando para permitir copiar su clave pública desde la máquina local a la virtual y en la pregunta introduciremos `yes`:

```bash
  adrian@ordenador:~$ ssh-copy-id usuario@iaas-dsi
  The authenticity of host 'iaas-dsi (10.6.XXX.XXX)' can't be established.
  ECDSA key fingerprint is SHA256:0EiyCRkSPTUXHosXK6TU3phvnPffdBSxWU9xROk7/Zk
  Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
  /usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
  /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
  usuario@iaas-dsi's password: 

  Number of key(s) added: 1

  Now try logging into the machine, with:   "ssh 'usuario@iaas-dsi'"
  and check to make sure that only the key(s) you wanted were added.
```

Una vez hecho esto, iniciaremos sesión en la máquina virtual por `SSH` nada mas usando el usuario y el nombre.

```bash
  adrian@ordenador:~$ ssh usuario@iaas-dsi
  Welcome to Ubuntu 18.04.5 LTS (GNU/Linux 4.15.0-135-generic x86_64)

   * Documentation:  https://help.ubuntu.com
   * Management:     https://landscape.canonical.com
   * Support:        https://ubuntu.com/advantage

    System information as of Fri Feb  5 10:16:46 WET 2021

  ...

  Last login: Fri Feb  5 10:14:25 2021 from 10.20.53.243
  usuario@iaas-dsi:~$
```

##### 8. Modificar el fichero de configuración SSH de la máquina local para no tener que introducir el usuario para iniciar sesión en la máquina virtual.

Tambien podemos iniciar sesión directamente con el nombre del host, sin hacer uso del nombre de usario, de la siguiente manera. Pasaremos a configurar el fichero `~/.ssh/config` en la máquina local ejecutando lo siguiente:

```bash
  # Creamos el fichero vacío
  adrian@ordenador:~$ touch ~/.ssh/config
  
  # Editamos el fichero y añadimos 3 líneas, la primera 'Host iaas-dsi', la segunda ' HostName iaas-dsi' y la tercera 'User usuario' (La línea 1 y 2 deben tener el nombre de la máquina que le hayamos puesto)
  adrian@ordenador:~$ vi ~/.ssh/config
  
  # Mostramos el contenido del fichero nuevamente para comprobar que se haya cambiado
  adrian@ordenador:~$ cat ~/.ssh/config 
  Host iaas-dsi
    HostName iaas-dsi
    User usuario
```

Ahora nos podemos conectar a la máquina virtual por `SSH` simplemente con el nombre correspondiente:

```bash
  adrian@ordenador:~$ ssh iaas-dsi
```


### _**Pasos a seguir para instalar git y Node.js en nuestra máquina.**_
##### 1. Instalar git en la máquina virtual.

  Ejectuamos lo siguiente:

```bash
  usuario@iaas-dsi:~$ sudo apt install git
  Leyendo lista de paquetes... Hecho
  Creando árbol de dependencias       
  Leyendo la información de estado... Hecho
  git ya está en su versión más reciente (1:2.25.1-1ubuntu3).
  0 actualizados, 0 nuevos se instalarán, 0 para eliminar y 0 no actualizados.
```

##### 2. Configuración de git.

  Para configurar git se puede seguir la guia [configurar git por primera vez](https://git-scm.com/book/es/v2/Inicio---Sobre-el-Control-de-Versiones-Configurando-Git-por-primera-vez), pero la configuración se reduce básicamente en los siguientes comandos con los datos correspondientes:

```bash
  # Añadimos el nombre de usuario
  usuario@iaas-dsi:~$ git config --global user.name "ADRIAN HERNANDEZ SUAREZ"
  
  # Añadimos el correo electrónico
  usuario@iaas-dsi:~$ git config --global user.email alu0101235516@ull.edu.es
  
  # Comprobamos que los datos se hayan añadido correctamente
  usuario@iaas-dsi:~$ git config --list
  user.name=ADRIAN HERNANDEZ SUAREZ
  user.email=alu0101235516@ull.edu.es
```

##### 3. Configuración del prompt.

  Ahora, configuraremos el prompt de la terminal de la máquina virtual para que aparezca la rama actual en la que nos encontramos cuando accedemos a algún directorio asociado con un repositorio git. Para hacer esto, lo primero que haremos es descargar el script [git prompt](https://github.com/git/git/blob/master/contrib/completion/git-prompt.sh) y se creará el fichero `git-prompt.sh`.

Posteriormente ejecutaremos los siguientes comandos:

```bash
  # Cambiamos el nombre del script
  usuario@iaas-dsi:~$ mv git-prompt.sh .git-prompt.sh
  
  # Le añadimos al final del fichero '.bashrc' la línea 'source ~/.git-prompt.sh' y la línea 'PS1='\[\033]0;\u@\h:\w\007\]\[\033[0;34m\][\[\033[0;31m\]\w\[\033[0;32m\]($(git      branch 2>/dev/null | sed -n "s/\* \(.*\)/\1/p"))\[\033[0;34m\]]$''
  usuario@iaas-dsi:~$ vi .bashrc
  
  # Comprobamos el final del fichero para asegurarnos de que se haya añadido correctamente
  usuario@iaas-dsi:~$ tail .bashrc
  ...
  source ~/.git-prompt.sh
  PS1='\[\033]0;\u@\h:\w\007\]\[\033[0;34m\][\[\033[0;31m\]\w\[\033[0;32m\]($(git branch 2>/dev/null | sed -n "s/\* \(.*\)/\1/p"))\[\033[0;34m\]]$'

  # Reiniciamos la terminal
  usuario@iaas-dsi:~$ exec bash -l
  [~()]$
```

Como se puede observar el prompt ha cambiado de `usuario@iaas-dsi:~$` a `[~()]$`.

No obstante para comprobar realmente que el prompt muestra lo que deseamos (la rama actual de trabajo cuando accedemos a un directorio asociado a un repositorio), tendremos que añadir la clave pública de la máquina virtual en la configuración de las claves de nuestra cuenta de GitHub, así nos sera más facil trabajar con repositorios remotos y también podremos clonar el repositorio para hacer la prueba.

Con el comando que vimos anteriormente, podremos copiar la clave:

```bash
  adrian@ordenador:~$ cat .ssh/id_rsa.pub 
  ssh-rsa MyjaxGqzdb0yWOk1/HYjh4OB8c4hVC1yc2EAAAADAQABAAABgQDhVLdhoSjuanwqdKnXeTo7U6M0Clcf3upzQ5Y9AXbJl04FoFgLQrcvlbnpPlXCzp40EiyCRkSPTUXHosXK6TU3phvnPffdBSxWU9xROk7/ZkgpBvDKwAlg4JXwvFkQq+WCmmazC6/+3kOhkQw4SWkBvzz1vf5LMDOF1Ja8j2isWwxebgSf9nxRlbDaaRXa1mkk0EiyCRkSPTUXHosXK6TU3phvnPffdBSxWU9xROk7/Zkwd966drZ8edUE/Q5gDxk+9umHzDYZ729yV7LE6IWPyJ5sxfhP6acdbHiDIqiYm4Z8in84OB8c4hVtVAkIIhRqecC3RYf52Sv6UgRZNj0QDNUcHksPXY1OhsctPgXEXD2OIxuL9lHxPmD2Jt5cFCBoux7yRUth8/nho43OYUyv79y4OB8c4hVF6ZAZ0wvabfBvFa2m4k8u0EiyCRkSPTUXHosXK6TU3phvnPffdBSxWU9xROk7/ZkSZoYFlG8tuKK5GPMz0= adrian@ordenador
```

Una vez copiada, accederemos a la configuración de su cuenta de GitHub (*account settings*), y en la sección SSH and GPG keys, pulsaremos sobre el botón *New SSH key*. En el formulario añadiremos un título para la clave y pegaremos la clave pública de nuestra máquina virtual en el campo de texto *key*. Por último, pulsaremos sobre el botón *Add SSH key*.

Después de hacer esto deberíamos poder clonar nuestro repositorio y una vez clonado entrariamos a él y nos tendría que poner en el prompt la rama actual.

```bash
  # Clonamos nuestro repositorio
  [~()]$git clone git@github.com:ULL-ESIT-INF-DSI-2021/ull-esit-inf-dsi-20-21-prct01-iaas-alu0101235516.git
  Clonando en 'prct01-iaas-vscode'...
  remote: Enumerating objects: 100, done.
  remote: Counting objects: 100% (100/100), done.
  remote: Compressing objects: 100% (79/79), done.
  remote: Total 100 (delta 32), reused 50 (delta 16), pack-reused 0
  Recibiendo objetos: 100% (100/100), 341.75 KiB | 1.63 MiB/s, listo.
  Resolviendo deltas: 100% (32/32), listo.
  
  # Comprobamos que se ha creado el directorio correspondiente
  [~()]$ls
  ull-esit-inf-dsi-20-21-prct01-iaas-alu0101235516
  
  # Entramos a dicho directorio el cual esta asociado con el repositorio
  [~()]$cd ull-esit-inf-dsi-20-21-prct01-iaas-alu0101235516/
  [~/ull-esit-inf-dsi-20-21-prct01-iaas-alu0101235516(gh-page)]$
```

##### 3. Instalar Node Version Manager (nmv), el gestor de versiones Node.js.

[Node.js](https://nodejs.org/en/) es un entorno que permite la ejecución de código desarrollado en JavaScript y variantes, como por ejemplo, TypeScript. Para instalar su gestor de versiones ([Node Version Manager](https://github.com/nvm-sh/nvm), deberemos  ejecutar los siguientes comandos:

```bash
  # Lo instalamos
  [~()]$wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash
  
  # Reiniciamos la terminal
  [~()]$exec bash -l
  
  # Comprobamos que se ha instalado correctamente
  [~()]$nvm --version
  0.37.2
```

Una vez hecho lo anterior, procederemos a instalar la versión más reciente de Node.js:

```bash
  [~()]$nvm install node
  Downloading and installing node v15.8.0...
  Downloading https://nodejs.org/dist/v15.8.0/node-v15.8.0-linux-x64.tar.xz...
  ####################################################################################################################################################################################### 100,0%
  Computing checksum with sha256sum
  Checksums matched!
  Now using node v15.8.0 (npm v7.5.1)
  Creating default alias: default -> node (-> v15.8.0)
  [~()]$node --version
  v15.8.0
  [~()]$npm --version
  7.5.1
```

##### 4. Si se quisiera instalar una versión concreta de Node.js.

Para instalar una versión mas concreta de Node.js, podemos hacer lo siguiente:

```bash
  # Lo instalamos
  [~()]$nvm install 12.0.0
  Downloading and installing node v12.0.0...
  Downloading https://nodejs.org/dist/v12.0.0/node-v12.0.0-linux-x64.tar.xz...
  ####################################################################################################################################################################################### 100,0%
  Computing checksum with sha256sum
  Checksums matched!
  Now using node v12.0.0 (npm v6.9.0)
  
  # Comprobamos que se instaló la versión que queriamos correctamente
  [~()]$node --version
  v12.0.0
  [~()]$npm --version
  6.9.0
```

Y si queremos cambiar entre versiones, podemos ejecutar los siguientes comandos:

```bash
  # Listamos las versiones que tenemos instaladas y con la '->' se nos muestra la versión que está en uso
  [~()]$nvm list
  ->      v12.0.0
          v15.8.0
  default -> node (-> v15.8.0)
  iojs -> N/A (default)
  unstable -> N/A (default)
  node -> stable (-> v15.8.0) (default)
  stable -> 15.8 (-> v15.8.0) (default)
  lts/* -> lts/fermium (-> N/A)
  lts/argon -> v4.9.1 (-> N/A)
  lts/boron -> v6.17.1 (-> N/A)
  lts/carbon -> v8.17.0 (-> N/A)
  lts/dubnium -> v10.23.2 (-> N/A)
  lts/erbium -> v12.20.1 (-> N/A)
  lts/fermium -> v14.15.4 (-> N/A)
  
  # Cambiamos la versión a usar
  [~()]$nvm use v15.8.0 
  Now using node v15.8.0 (npm v7.5.1)
  
  # Listamos la versión que estamos usando
  [~()]$node --version
  v15.8.0
  [~()]$npm --version
  7.5.1
```

### _**Conclusión.**_

En lo que a mi respecta, pienso que git es una herramienta imprescindible para la programación moderna, para la informática y más bien, para los informáticos, brinda una utilidad increible y un camino lleno de nuevas rutas y posibilidades. Cabe destacar también el Markdown, es la primera vez que lo uso para realizar un informe, y creo que es increible, no se por que no lo he usado antes, porque nos da mucha jugabilidad y si me permiten la expresión, mola mucho.

### _**Bibliografía.**_

Nombre | Enlaces
-------|--------
Introducción a Markdown | https://guides.github.com/features/mastering-markdown/
Información sobre GitHub Pages | https://docs.github.com/en/github/working-with-github-pages
Sitio web de Jekyll | https://jekyllrb.com/
GutHub Learning Lab | https://lab.github.com/
Curso de GitHub Pages | https://lab.github.com/githubtraining/github-pages
Curso 'Communicating using Markdown' | https://lab.github.com/githubtraining/communicating-using-markdown
Documentación de configuración de la VPN de la ULL | https://www.ull.es/servicios/stic/2020/12/01/servicio-de-vpn-de-la-ull/
Servicio IaaS de la ULL | https://iaas.ull.es/
Libro "Pro Git" de Scott Chachon y Ben Straub | https://git-scm.com/book/es/v2
Intrucciones para configurar git por primera vez | https://git-scm.com/book/es/v2/Inicio---Sobre-el-Control-de-Versiones-Configurando-Git-por-primera-vez
Script git prompt | https://github.com/git/git/blob/master/contrib/completion/git-prompt.sh
Curso de introducción a GitHub | https://lab.github.com/githubtraining/introduction-to-github
Curso 'First Week on GitHub' | https://lab.github.com/githubtraining/first-week-on-github
Node Version Manager (nvm) | https://github.com/nvm-sh/nvm
Node.js | https://nodejs.org/en/
Node Package Manager (npm) | https://www.npmjs.com/



