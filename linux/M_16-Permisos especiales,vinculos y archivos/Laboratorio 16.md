## 16.1 Introducción
Este es Lab 16: Los Permisos Especiales. Mediante la realización de esta práctica de laboratorio, los estudiantes aprenderán acerca de los permisos especiales y diferentes tipos de archivos de enlace.

En este laboratorio llevarás a cabo las siguientes tareas:

Visualizar los archivos con permisos especiales
Crear vínculos físicos y simbólicos


## 16.2 Visualización de Permisos Especiales
En esta tarea encontrarás y entenderás el propósito de los permisos especiales más allá de lectura, escritura y ejecución.

## 16.2.1 Paso 1
Lista los detalles de los directorios /tmp y /var/tmp:

```bash
ls -ld /tmp
ls -ld /var/tmp
```

La salida muestra que los permisos en estos directorios son iguales:

```shell-session
sysadmin@localhost:~$ ls -ld /tmp                                         
drwxrwxrwt 2 root root 4096 Mar 14 17:34 /tmp                             
sysadmin@localhost:~$ ls -ld /var/tmp                                     
drwxrwxrwt 2 root root 4096 Apr 19  2012 /var/tmp                         
sysadmin@localhost:~$
```

Los directorios /tmp y /var/tmp tienen permisos de leer, escribir y ejecución para todos. Además de los directorios personales de los usuarios, estos dos directorios «_temporales_» son las ubicaciones en el sistema de archivos donde los usuarios normales pueden crear nuevos archivos o directorios.

Esto plantea un problema: si todos los usuarios pueden crear nuevos archivos, también pueden eliminar los archivos existentes. Esto se debe a que el permiso de escritura en un directorio otorga a los usuarios la capacidad de agregar y eliminar los archivos en un directorio.

La `t` en la columna de ejecución para los otros permisos indica que este directorio tiene configurado el permiso _sticky bit_. Este permiso especial significa que a pesar de que todo el mundo puede añadir archivos en estos directorios, sólo el usuario que crea un archivo puede eliminar ese archivo.

El usuario root no se ve afectado por este permiso, ya que esa cuenta puede eliminar todos los archivos en el directorio, independientemente de la propiedad.


## 16.2.2 Paso 2
Visualiza los permisos en el archivo /etc/shadow:

```bash
ls -l /etc/shadow
```

El resultado muestra que el usuario root tiene permisos de leer y escribir, los miembros del grupo shadow tienen permiso de lectura, mientras otros no tienen ningún permiso para este archivo:

```shell-session
sysadmin@localhost:~$ ls -l /etc/shadow                                   
-rw-r----- 1 root shadow 838 Mar 14 17:34 /etc/shadow                     
sysadmin@localhost:~$
```

Al igual que los otros archivos ubicados en el directorio /etc, el archivo /etc/shadow contiene información acerca de la configuración host. En concreto, el archivo /etc/shadow contiene las contraseñas cifradas de todas las cuentas de usuario locales e información sobre la caducidad de contraseñas (el tiempo que una contraseña es válida). Dado que esta es una información muy sensible, el acceso a este archivo está limitado al usuario root y comandos ejecutados comandos como root, así como los miembros del grupo shadow.

Cuando un usuario actualiza su contraseña con el comando passwd, el comando `passwd` se ejecuta con un permiso especial llamado ___setuid___. El permiso setuid hace que un archivo se ejecute como el usuario que posee el archivo, en lugar del usuario que está ejecutando realmente el comando.

Si tu conocimiento proviene de Microsoft Windows, puedes pensar que setuid sería como la característica de «_Ejecutar como administrador_» en Windows.

## 16.2.3 Paso 3
Visualiza los permisos del archivo /usr/bin/passwd:

```bash
ls -l /usr/bin/passwd
```

El listado de este archivo muestra:

```shell-session
sysadmin@localhost:~$ ls -l /usr/bin/passwd                                
-rwsr-xr-x 5 root root 42824 Sep 12  2012 /usr/bin/passwd                 
sysadmin@localhost:~$
```

Observa la s en la columna de permiso de ejecución del usuario. Esto indica que el archivo tiene el conjunto de permisos setuid, por lo que se ejecuta como el usuario que lo posee (root) en lugar del usuario que ejecuta el comando.

Por lo tanto, el comando `passwd` puede actualizar el archivo /etc/shadow, conforme se ejecuta como el usuario root  (recuerda que el usuario root puede editar cualquier archivo, independientemente de los permisos en el archivo).

## 16.2.4 Paso 4
Visualiza los permisos del comando /usr/bin/wall:

```bash
ls -l /usr/bin/wall
```
Ahora la salida muestra la s en la columna de ejecución para el grupo:

```shell-session
sysadmin@localhost:~$ ls -l /usr/bin/wall                                 
-rwxr-sr-x 5 root tty 18976 Jun 18  2014 /usr/bin/wall                    
sysadmin@localhost:~$
```

La `s` en la columna de ejecución de grupo indica que este archivo tiene el conjunto de permisos ___setgid___, por lo que se ejecuta como el grupo que lo posee (tty) en lugar del grupo del usuario que ejecuta el comando. Por lo tanto, el comando wall puede escribir en todas las terminales (ttys) conforme se ejecuta como el grupo de tty.

___Nota___: Esto es muy similar al permiso __setuid__, pero en lugar de ejecutar el comando como el usuario propietario del programa, el comando se ejecuta como el propietario del grupo del programa.

>Hasta ahora has visto tres tipos de permisos especiales: sticky bit en un directorio, setuid en un archivo ejecutable y setgid en un archivo ejecutable. Como has visto, estos tres tipos están presentes en un sistema típico.

>Puedes utilizar un tipo de permiso especial; El permiso setgid también se puede aplicar a un directorio.

>Si ves la s en la columna de ejecutar para el grupo propietario del directorio, el directorio tiene el permiso setgid. Cuando un directorio tiene el permiso setgid, entonces cualquier nuevo archivo o directorio creado en ese directorio será automáticamente propiedad del grupo que posee el directorio, no del grupo del usuario que creó el archivo.

## 16.3 Los Vínculos Físicos y Simbólicos

En esta tarea vas a crear y utilizar los vínculos físicos y simbólicos.

## 16.3.1 Paso 1
Cambia a tu directorio home:

	cd

## 16.3.2 Paso 2
Crea un archivo llamado source que contiene el texto «data» utilizando la redirección:

```bash
echo "data" > source
```

## 16.3.3 Paso 3
Visualiza los detalles e información del i-nodo del archivo source:

```bash
ls -li source
```

La salida resaltada muestra que el archivo tiene el número i-nodo `2076` (este número puede variar de un sistema a otro) y un conteo de vínculos 1:

```shell-session
sysadmin@localhost:~$ ls -li source                                       
2076 -rw-rw-r-- 1 sysadmin sysadmin 5 Apr 12 13:27 source             
sysadmin@localhost:~$
```

El sistema operativo Linux utiliza los i-nodos para realizar un seguimiento de la información acerca de un archivo. Una entrada de directorio asocia un i-nodo con un nombre de archivo.

Al crear un vínculo físico se crea otra entrada de directorio asociado con un i-nodo existente, y aumentará el número de los vínculos.

Los vínculos físicos te permiten tener varios nombres para referirse a un mismo archivo. Si se elimina cualquiera de estos nombres, aún puedes utilizar los otros nombres para hacer referencia al archivo.

De hecho, estos otros nombres, incluso se pueden utilizar para crear vínculos adicionales. Todos estos nombres se consideran equivalentes, ya que todos se refieren a un i-nodo existente.

>___Nota___: No puedes crear vínculos físicos a los directorios. Además, un vínculo físico a un archivo también tiene que existir dentro del mismo sistema de archivos (partición) igual que el archivo al que se vincula.


## 16.3.4 Paso 4
Utiliza el comando `ln` para crear un vínculo físico. Visualiza los detalles e información del i-nodo del archivo de vínculo físico original y el nuevo:

```bash
ln source hardlink
ls -li source hardlink
```

Observa que el archivo hardlink y el archivo original source comparten el mismo i-nodo:

```shell-session
sysadmin@localhost:~$ ln source hardlink                                  
sysadmin@localhost:~$ ls -li source hardlink                              
2076 -rw-rw-r-- 2 sysadmin sysadmin 5 Apr 12 13:27 hardlink           
2076 -rw-rw-r-- 2 sysadmin sysadmin 5 Apr 12 13:27 source             
sysadmin@localhost:~$
```

## 16.3.5 Paso 5

Utiliza el comando In para crear un vínculo físico al archivo source. Visualiza los detalles e información del i-nodo del archivo de vínculo físico original y el nuevo:

```bash
ln hardlink hardtoo
ls -li hardlink hardtoo source
```

Observa que el resultado sigue mostrando que los vínculos físicos comparten el mismo i-nodo y el número de vínculos aumenta en todos los vínculos cuando se añade uno nuevo:

```shell-session
sysadmin@localhost:~$ ln source hardtoo                                   
sysadmin@localhost:~$ ls -li hardlink hardtoo source                      
2076 -rw-rw-r-- 3 sysadmin sysadmin 5 Apr 12 13:27 hardlink           
2076 -rw-rw-r-- 3 sysadmin sysadmin 5 Apr 12 13:27 hardtoo           
2076 -rw-rw-r-- 3 sysadmin sysadmin 5 Apr 12 13:27 source             
sysadmin@localhost:~$
```

## 16.3.6 Paso 6

Retira el último vínculo que se creó y lista los archivos source y hardlink:

```bash
rm hardtoo
ls -li source hardlink
```

Observa que el número de vínculos se disminuye cuando se elimina uno de los archivos con el vínculo físico:

```shell-session
sysadmin@localhost:~$ rm hardtoo                                          
sysadmin@localhost:~$ ls -li source hardlink                              
2076 -rw-rw-r-- 2 sysadmin sysadmin 5 Apr 12 13:27 hardlink           
2076 -rw-rw-r-- 2 sysadmin sysadmin 5 Apr 12 13:27 source             
sysadmin@localhost:~$
```

## 16.3.7 Paso 7
Elimina el archivo hardlink y lista los detalles del archivo source:

```bash
rm hardlink
ls -li source
```
```shell-session
sysadmin@localhost:~$ rm hardlink                                         
sysadmin@localhost:~$ ls -li source                                       
2076 -rw-rw-r-- 1 sysadmin sysadmin 5 Apr 12 13:27 source            
sysadmin@localhost:~$
```

Otro tipo de vínculo que puedes crear se conoce como un vínculo simbólico o vínculo blando. Los vínculos simbólicos no aumentan el número de vínculos de los archivos con los que están vinculados.

Los archivos de vínculos simbólicos tienen su propio i-nodo y el tipo de archivo. En lugar de vincular y compartir un i-nodo, se vinculan al nombre de archivo. A diferencia de los vínculos físicos, los vínculos simbólicos pueden estar vinculados a los directorios y pueden atravesar los dispositivos y las particiones de sus objetivos.

## 16.3.8 Paso 8
Crea un vínculo simbólico al archivo source y ve los detalles de los dos archivos:

```bash
ln -s source softlink
ls -li source softlink
```

La salida resaltada muestra que el archivo softlink y source tienen diferentes i-nodos. Observa que el tipo de vínculo del archivo se crea al realizar un vínculo simbólico. Los permisos del enlace son irrelevantes, ya que son los permisos del archivo de destino que determinan el acceso:

```shell-session
sysadmin@localhost:~$ ln -s source softlink                               
sysadmin@localhost:~$ ls -li source softlink                              
2086 lrwxrwxrwx 1 sysadmin sysadmin 6 Apr 12 13:56 softlink -> source 
2076 -rw-rw-r-- 1 sysadmin sysadmin 5 Apr 12 13:27 source             
sysadmin@localhost:~$
```

## 16.3.9 Paso 9
Crea un enlace simbólico al directorio /proc y muestra el vínculo:

```bash
ln -s /proc crossdir
ls -l crossdir
```

El éxito de estos comandos muestra que los vínculos simbólicos pueden hacer referencia a los directorios, y pueden cruzar de un sistema de archivos a otro, que son dos cosas que los enlaces físicos no pueden hacer:

```shell-session
sysadmin@localhost:~$ ln -s /proc crossdir                                
sysadmin@localhost:~$ ls -l crossdir                                      
lrwxrwxrwx 1 sysadmin sysadmin 5 Apr 12 14:00 crossdir -> /proc           
sysadmin@localhost:~$
```