## 15.1 Introducción
Este es Lab 15: Propiedad y Permisos. Mediante la realización de esta práctica de laboratorio, los estudiantes aprenderán a ajustar y ver la propiedad y los permisos de los archivos y directorios.

En este laboratorio llevarás a cabo las siguientes tareas:

Ver y entender los permisos de los archivos y directorios.
Utilizar el comando chmod para cambiar los permisos.
Cambiar la propiedad con los comandos `chown` y `chgrp`.

## 15.2 Permisos de los Archivos y la Propiedad
En esta tarea vas a crear los archivos y directorios, ver y configurar sus permisos y propiedad.

## 15.2.1 Paso 1
Crea dos directorios y dos archivos en el directorio /tmp:

```shell-session
cd /tmp
mkdir priv-dir pub-dir
touch priv-dir/priv-file
touch pub-dir/pub-file
sysadmin@localhost:~$ cd /tmp                                             
sysadmin@localhost:/tmp$ mkdir priv-dir pub-dir                           
sysadmin@localhost:/tmp$ touch priv-dir/priv-file                         
sysadmin@localhost:/tmp$ touch pub-dir/pub-file                          
sysadmin@localhost:/tmp$
```

## 15.2.2 Paso 2
Ve el contenido de los directorios:

```bash
ls -l priv-dir
ls -l pub-dir
```

Tu salida debe contener lo siguiente:

```shell-session
sysadmin@localhost:/tmp$ ls -l priv-dir                                   
total 0                                                                   
-rw-rw-r-- 1 sysadmin sysadmin 0 Apr 11 21:26 priv-file                   
sysadmin@localhost:/tmp$ ls -l pub-dir                                    
total 0                                                                   
-rw-rw-r-- 1 sysadmin sysadmin 0 Apr 11 21:27 pub-file                    
sysadmin@localhost:/tmp$
```
 Observa que para cada archivo que se muestra, el primer carácter de la línea es un guión `-`. Esto transmite que los artículos son archivos regulares. El primer carácter de la lista indica el tipo de archivo, donde `d` indica un directorio, `-` es un archivo regular, les un enlace simbólico, `b` es un archivo de dispositivo de bloques, `c` es un archivo de dispositivo de caracteres, `p` es un archivo canalizador y `s` es un archivo de ranura.

Los siguientes nueve caracteres están en tres grupos de tres caracteres. El primer grupo de tres caracteres (`rw-` en el ejemplo anterior) son los permisos del usuario propietario, los siguientes tres caracteres (`rw-` en el ejemplo anterior) son los permisos del propietario del grupo y los tres últimos caracteres (`r--` en el ejemplo anterior) representan todos los demás permisos (denominados «_otros_»).

Al visualizar los permisos, `r` indica el permiso leer, `w` indica el permiso escribir y `x` indica el permiso de ejecución. El guión `-` indica que ese permiso no se ha concedido.

Después de los permisos viene un número de enlaces indicando cuántos archivos están vinculados a este archivo. A continuación, ves al usuario propietario, al propietario del grupo, el tamaño del archivo, la _fecha/hora_ de la última modificación del archivo y el nombre del archivo.


## 15.2.3 Paso 3
Si quieres que un directorio sea más privado, a continuación, puedes utilizar el comando `chmod` para quitar los permisos que otros tienen en el directorio. Utiliza el comando `chmod` para quitar los permisos del otro para leer y ejecutar:
```bash
ls -ld priv-dir/
chmod o-rx priv-dir/
ls -ld priv-dir/
```

Ahora la salida muestra que otros no tienen permiso o acceso al priv-dir:
```shell-session
sysadmin@localhost:/tmp$ ls -ld priv-dir/                                 
drwxrwxr-x 2 sysadmin sysadmin 4096 Apr 11 21:26 priv-dir/                
sysadmin@localhost:/tmp$ chmod o-rx priv-dir/                             
sysadmin@localhost:/tmp$ ls -ld priv-dir/                                 
drwxrwx--- 2 sysadmin sysadmin 4096 Apr 11 21:26 priv-dir/                
sysadmin@localhost:/tmp$
```
Utilizaste el comando chmod para modificar los permisos para los demás mediante el uso del carácter o seguido de un carácter `+` o un carácter `-` para sumar o restar los permisos. El carácter `=` se puede utilizar para fijar un permiso exacto.

Puedes utilizar una `u` en lugar de una o para modificar los permisos del usuario propietario. Utiliza una `g` si quieres cambiar los permisos para el propietario del grupo.

Para modificar los permisos de todo el mundo utiliza una a `r` en lugar de `o`, u `o` `g`.

comando | descripción
-|-
`chmod a+x file`	|#proporciona todos los permisos de ejecución
`chmod g-w file`	|#elimina el permiso de escritura para los propietarios del grupo
`chmod go+r file`	|#agrega permiso de lectura para el propietario del grupo y otros
`chmod o=rwx`	|#configura permisos de otros para leer, escribir y ejecutar


## 15.2.4 Paso 4
Si quieres que un directorio sea más público, a continuación, puedes utilizar el comando `chmod` para agregar el permiso de escritura para otros:

```bash
ls -ld pub-dir/
chmod o+w pub-dir/
ls -ld pub-dir/
```

Ahora, tu salida muestra que los demás tienen permiso de escritura en el directorio (pueden añadir o eliminar archivos dentro del directorio):

```shell-session
sysadmin@localhost:/tmp$ ls -ld pub-dir/                                  
drwxrwxr-x 2 sysadmin sysadmin 4096 Apr 11 21:27 pub-dir/                 
sysadmin@localhost:/tmp$ chmod o+w pub-dir/                               
sysadmin@localhost:/tmp$ ls -ld pub-dir/                                 
drwxrwxrwx 2 sysadmin sysadmin 4096 Apr 11 21:27 pub-dir/               
sysadmin@localhost:/tmp$
```

## 15.2.5 Paso 5
Utiliza el comando `chmod` para eliminar cualquier permiso del grupo u otros en el archivo priv-file:

```bash
ls -l priv-dir/priv-file
chmod g-rw,o-r priv-dir/priv-file
ls -l priv-dir/priv-file
```

El resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:/tmp$ ls -l priv-dir/priv-file                         
-rw-rw-r-- 1 sysadmin sysadmin 0 Apr 11 21:26 priv-dir/priv-file          
sysadmin@localhost:/tmp$ chmod g-rw,o-r priv-dir/priv-file                
sysadmin@localhost:/tmp$ ls -l priv-dir/priv-file                         
-rw------- 1 sysadmin sysadmin 0 Apr 11 21:26 priv-dir/priv-file         
sysadmin@localhost:/tmp$
```

## 15.2.6 Paso 6
Concede a todos los usuarios los mismos permisos de lectura y escritura para el archivo pub-file:

```bash
ls -l pub-dir/pub-file
chmod a=rw pub-dir/pub-file
ls -l pub-dir/pub-file
```

El resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:/tmp$ ls -l pub-dir/pub-file                           
-rw-rw-r-- 1 sysadmin sysadmin 0 Apr 11 21:27 pub-dir/pub-file            
sysadmin@localhost:/tmp$ chmod a=rw pub-dir/pub-file                      
sysadmin@localhost:/tmp$ ls -l pub-dir/pub-file                           
-rw-rw-rw- 1 sysadmin sysadmin 0 Apr 11 21:27 pub-dir/pub-file           
sysadmin@localhost:/tmp$
```

## 15.2.7 Paso 7
Crea un archivo _test.sh_ en el directorio /tmp con el contenido de «_date_»:

```bash
echo "date" > test.sh
```
```shell-session
sysadmin@localhost:/tmp$ echo "date" > test.sh                            
sysadmin@localhost:/tmp$
```
Si un archivo contiene comandos, entonces los comandos pueden correr o se pueden ejecutar, si el archivo tiene permiso de ejecución para el usuario. El proceso de crear un archivo ejecutable requiere dar el permiso de ejecución en el archivo. Sin este permiso, el archivo no puede ser tratado como un programa.

## 15.2.8 Paso 8
Intenta ejecutar el archivo test.sh; debería fallar. Ve los permisos en el archivo para ver por qué:

```bash
./test.sh
ls -l test.sh
```
 ```shell-session
sysadmin@localhost:/tmp$ ./test.sh                                        
-bash: ./test.sh: Permission denied                                       
sysadmin@localhost:/tmp$ ls -l test.sh                                    
-rw-rw-r-- 1 sysadmin sysadmin 5 Apr 11 22:27 test.sh                     
sysadmin@localhost:/tmp$
```

## 15.2.9 Paso 9
Sólo el usuario propietario de un archivo (o el usuario root) puede cambiar los permisos de un archivo. Como usuario propietario, date permisos de ejecución y luego ejecuta ___test.sh___:

```bash
chmod u+x test.sh
ls -l test.sh
./test.sh
```

La salida muestra el permiso de ejecución agregado para el usuario propietario y la fecha y la hora actual desde la ejecución del comando date dentro de tu archivo script ___test.sh___:

```shell-session
sysadmin@localhost:/tmp$ chmod u+x test.sh                                
sysadmin@localhost:/tmp$ ls -l test.sh                                    
-rwxrw-r-- 1 sysadmin sysadmin 5 Apr 11 22:27 test.sh                     
sysadmin@localhost:/tmp$ ./test.sh                                        
Mon Apr 11 22:35:23 UTC 2016                                              
sysadmin@localhost:/tmp$
```

Hasta ahora, viste cómo utilizar el comando `chmod` con la notación simbólica, donde se utilizan símbolos para representar quién (u, g, o, y a), cómo (+, -, o =), y qué cambiar (r, w, y x). El comando `chmod` también se puede utilizar con un valor numérico que representa los permisos del usuario propietario, propietario del grupo y otros en lo que se llama notación ___octal___.

Para utilizar el comando chmod con la notación octal, primero hay que entender el valor octal de los permisos:

- Read (r) (leer)	4
- Write (w) (escribir)	2
- Execute (x) (ejecución)	1

A partir del último listado del archivo ___test.sh___, se mostró que los permisos eran `rwx` para el usuario propietario, `rw` para el propietario del grupo, y `r` para otros. Para expresar estos permisos en notación ___octal___, se calcula un total para cada propiedad.

Como resultado se calcularía el total del permiso del usuario como 4 + 2 + 1, o 7, donde 4 es permiso de lectura, el 2 de escritura, y 1 de ejecución.

El permiso total del grupo sería 4 + 2 o 6, donde 4 es permiso de lectura y 2 de escritura.

El permiso de propiedad para otros sería simplemente 4, permiso de lectura.

Poniendo todo junto, el valor octal para los permisos actuales sería `764`.

## 15.2.10 Paso 10
Utiliza el comando stat para verificar el valor octal para los permisos (acceso) al archivo ___test.sh___:
```bash
stat test.sh
```
```shell-session
sysadmin@localhost:/tmp$ stat test.sh                                   
  File: `test.sh'                                                       
  Size: 5               Blocks: 8          IO Block: 4096   regular file  
Device: fc00h/64512d    Inode: 3540004     Links: 1                       
Access: (0764/-rwxrw-r--)  Uid: ( 1001/sysadmin)   Gid: ( 1001/sysadmin)  
Access: 2016-04-11 22:27:24.987740243 +0000                               
Modify: 2016-04-11 22:27:24.987740243 +0000                               
Change: 2016-04-11 22:35:08.335757863 +0000                              
 Birth: -                                                                 
sysadmin@localhost:/tmp$
```
Si quieree cambiar estos permisos usando la notación octal para dar al grupo y a otros permiso de ejecución, entonces deberías usar los tres números siguientes:

- 7 (lectura, escritura y ejecución) para el usuario propietario
- 7 (lectura, escritura y ejecución) para el propietario del grupo
- 5 (leer y ejecutar) para otros
El nuevo modo, o el número octal para los permisos sería entonces 775.




## 15.2.11 Paso 11
Utilizando la notación octal, modifica los permisos del archivo _test.sh_ de tal modo, que todo el mundo pueda ejecutar el archivo:

```bash
chmod 775 test.sh
ls -l test.sh
```

```shell-session
sysadmin@localhost:/tmp$ chmod 775 test.sh                                
sysadmin@localhost:/tmp$ ls -l test.sh                                    
-rwxrwxr-x 1 sysadmin sysadmin 5 Apr 11 22:27 test.sh                     
sysadmin@localhost:/tmp$
```

Hay dos comandos que pueden afectar la propiedad de los archivos. El comando `chown` sólo puede ser ejecutado por el usuario root y puede cambiar el usuario propietario de un archivo o el usuario y grupo propietario de un archivo.

El comando `chgrp` se puede utilizar ya sea por el usuario propietario de un archivo o por el usuario root.

El comando `chgrp` sólo cambia el grupo propietario de un archivo.

Cuando un usuario que sea un root utiliza el comando `chgrp`, sólo puede cambiar la propiedad del grupo a un grupo del sea miembro. El usuario root puede utilizar chgrp para cambiar la propiedad de grupo de cualquier archivo a cualquier grupo.


## 15.2.12 Paso 12
Cambia al usuario root para que puedas ejecutar tanto el comando `chown` como el `chgrp` para las pertenencias a grupos a cualquier grupo:

```bash
su - 
(proporciona la contraseña root: netlab123)
```

```shell-session
sysadmin@localhost:/tmp$ su -                                             
Password:                                                                 
root@localhost:~#
```

## 15.2.13 Paso 13
Cambia el directorio /tmp y lista los detalles del archivo pub-dir, y luego su contenido:

```bash
cd /tmp
ls -ld pub-dir
ls -l pub-dir/pub-file
```

Observa que la salida muestra el directorio y el archivo propiedad del usuario `sysadmin`, y del grupo `sysadmin`:

```shell-session
root@localhost:~# cd /tmp                                                 
root@localhost:/tmp# ls -ld pub-dir/                                      
drwxrwxrwx 2 sysadmin sysadmin 4096 Apr 11 22:48 pub-dir/                 
root@localhost:/tmp# ls -l pub-dir/pub-file                               
-rw-rw-rw- 1 sysadmin sysadmin 0 Apr 11 22:48 pub-dir/pub-file            
root@localhost:/tmp#
```

## 15.2.14 Paso 14
Utiliza el comando `chown` para cambiar al usuario y al propietario de grupo del archivo pub-dir al usuario root y al grupo root. A continuación, ve los detalles del directorio:

```bash
chown root:root pub-dir
ls -ld pub-dir
```

La salida debe mostrar que el usuario y los propietarios de grupos han cambiado:

```shell-session
root@localhost:/tmp# chown root:root pub-dir                              
root@localhost:/tmp# ls -ld pub-dir/                                      
drwxrwxrwx 2 root root 4096 Apr 11 22:48 pub-dir/                         
root@localhost:/tmp#
```

## 15.2.15 Paso 15
Utiliza el comando `chown` para cambiar al usuario propietario del archivo lpub-file al usuario `bin`:

```bash
chown bin pub-dir/pub-file
ls -l pub-dir/pub-file
```

Ahora la salida muestra que el usuario propietario ha sido actualizado a `bin`:

```shell-session
root@localhost:/tmp# chown bin pub-dir/pub-file                           
root@localhost:/tmp# ls -l pub-dir/pub-file                               
-rw-rw-rw- 1 bin sysadmin 0 Apr 11 22:48 pub-dir/pub-file                 
root@localhost:/tmp#
```

##  15.2.16 Paso 16
Visualiza los detalles del archivo priv-dir y su contenido:

```bash
ls -ld priv-dir
ls -l priv-dir/priv-file
```

La salida debe demostrar que priv-dir es propiedad del usuario `sysadmin` y del grupo `sysadmin`:

```shell-session
root@localhost:/tmp# ls -ld priv-dir                                      
drwxrwx--- 2 sysadmin sysadmin 4096 Apr 11 22:48 priv-dir                 
root@localhost:/tmp# ls -l priv-dir/priv-file                             
-rw------- 1 sysadmin sysadmin 0 Apr 11 22:48 priv-dir/priv-file          
root@localhost:/tmp#
```

## 15.2.17 Paso 17
Cambia el propietario del grupo del archivo _priv-dir_ y _priv-file_ al grupo users de forma recursiva con el comando `chgrp` y visualiza los archivos actualizados:

```bash
ls -ld priv-dir
ls -l priv-dir/priv-file
chgrp -R users priv-dir
ls -ld priv-dir
ls -l priv-dir/priv-file
```

```shell-session
root@localhost:/tmp# ls -ld priv-dir                                      
drwxrwx--- 2 sysadmin sysadmin 4096 Apr 11 22:48 priv-dir                 
root@localhost:/tmp# ls -l priv-dir/priv-file                             
-rw------- 1 sysadmin sysadmin 0 Apr 11 22:48 priv-dir/priv-file          
root@localhost:/tmp# chgrp -R users priv-dir                               
root@localhost:/tmp# ls -ld priv-dir                                     
drwxrwx--- 2 sysadmin users 4096 Apr 11 22:48 priv-dir                    
root@localhost:/tmp# ls -l priv-dir/priv-file                             
-rw------- 1 sysadmin users 0 Apr 11 22:48 priv-dir/priv-file             
root@localhost:/tmp#
```
Tu salida refleja que al aplicar los cambios de forma recursiva a un directorio, los cambios se aplican al directorio y a todo lo que contiene. Esto significaría que este cambio se aplica a cada subdirectorio bajo priv-dir, cada archivo en priv-dir y a todos sus subdirectorios.

