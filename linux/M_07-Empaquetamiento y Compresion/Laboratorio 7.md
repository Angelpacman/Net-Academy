## 7.1 Introducción
Este es Lab 7: Empaquetamiento y desempaquetamiento de los archivos. Mediante la realización de esta práctica de laboratorio, los estudiantes aprenderán cómo trabajar con los archivos empaquetados.

En este laboratorio llevarás a cabo las siguientes tareas:

1. Crear los archivos empaquetados usando tar con y sin compresión
2. Comprimir y descomprimir los archivos en un archivo empaquetado comprimido con gzip
3. Comprimir y descomprimir los archivos en un archivo empaquetado en bzip2
4. Utilizar zip y unzip para comprimir y descomprimir los archivos empaquetados

## 7.2 Comandos de empaquetamiento
En esta tarea, utilizaremos gzip, bzip2, tar y zip/unzip para empaquetar y restaurar los archivos. Estos comandos están diseñados para combinar varios archivos en un único archivo o comprimir un archivo grande en uno más pequeño. En algunos casos, los comandos tendrán ambas funciones.

La tarea de empaquetamiento de los datos es importante por varias razones, incluyendo pero no limitado a lo siguiente:

Los archivos grandes pueden ser difíciles de transferir. Haciendo estos archivos más pequeños ayuda a hacer la transferencia más rápida.
La transferencia de múltiples archivos de un sistema a otro puede llegar a ser tedioso cuando hay muchos archivos. Su fusión en un único archivo para la transferencia hace que este proceso sea más fácil.
Los archivos pueden ocupar rápidamente una gran cantidad de espacio, especialmente en un medio extraíble como memorias USB más pequeñas. El empaquetamiento reduce este problema.
Un área potencial de confusión que un usuario principiante de Linux puede experimentar se deriva de la siguiente pregunta: ¿Por qué hay tantos comandos diferentes de empaquetamiento? La respuesta es que estos comandos tienen diferentes características (por ejemplo, algunos de ellos permiten proteger el archivo empaquetado con una contraseña) y técnicas de compresión utilizadas.

Lo más importante que debe saber por ahora es cómo funcionan estos diferentes comandos. Con el tiempo, aprenderás a seleccionar la herramienta de empaquetamiento correcta para cualquier situación.

## 7.2.1 Paso 1
Utiliza el siguiente comando tar para crear un archivo empaquetado del directorio /etc/udev. Guardar la copia de seguridad en el directorio ~/mybackups :

```bash
cd
mkdir mybackups
tar –cvf mybackups/udev.tar /etc/udev
ls mybackups
```
Tu resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~$ cd
sysadmin@localhost:~$ mkdir mybackups
sysadmin@localhost:~$ tar -cvf mybackups/udev.tar /etc/udev
tar: Removing leading `/' from member names
/etc/udev/
/etc/udev/rules.d/
/etc/udev/rules.d/70-persistent-cd.rules
/etc/udev/rules.d/README
/etc/udev/udev.conf
sysadmin@localhost:~$ ls mybackups/
udev.tar
sysadmin@localhost:~$
```

El comando tar se utiliza para combinar varios archivos en un solo archivo. Por defecto no comprime los datos.

La opción `-c` le indica al comando tar que cree un archivo tar. La opción `-v` significa "verbose", que le indica al comando tar para muestre lo que está haciendo. La opción `-f` se utiliza para especificar el nombre del archivo tar.

Para tu información: tar significa Tape ARchive (archivo de cinta). Este comando se utilizó originalmente para crear copias de seguridad de cintas, pero hoy en día es más comúnmente utilizado para crear los archivo empaquetados.

>Importante: No tienes que utilizar la extensión .tar  con nombre de archivo empaquetado, sin embargo, es muy útil para determinar el tipo de archivo. Se considera de «buen estilo» cuando envias un archivo a otra persona.

## 7.2.2 Paso 2
Muestra el contenido del archivo tar (t = lista el contenido, v = verbose, f =nombre del archivo):

```bash
tar –tvf mybackups/udev.tar
```

Tu resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~$ tar -tvf mybackups/udev.tar
drwxr-xr-x root/root         0 2015-01-28 16:32 etc/udev/
drwxr-xr-x root/root         0 2015-01-28 16:32 etc/udev/rules.d/
-rw-r--r-- root/root       306 2015-01-28 16:32 etc/udev/rules.d/70-persistent-cd.rules
-rw-r--r-- root/root      1157 2012-04-05 19:18 etc/udev/rules.d/README
-rw-r--r-- root/root       218 2012-04-05 19:18 etc/udev/udev.conf
sysadmin@localhost:~$
```

Ten en cuenta que los archivos fueron copiados de forma recursiva utilizando los nombres de ruta relativa . Esto es importante porque al extraer los archivos, que serán colocados en el directorio actual, no se reemplazan los archivos actuales.


## 7.2.3 Paso 3
Para crear un archivo tar comprimido, utiliza la opción `-z`:

```bash
tar –zcvf mybackups/udev.tar.gz /etc/udev
ls –lh mybackups
```

Tu resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~$ tar -zcvf mybackups/udev.tar.gz /etc/udev
tar: Removing leading `/' from member names
/etc/udev/
/etc/udev/rules.d/
/etc/udev/rules.d/70-persistent-cd.rules
/etc/udev/rules.d/README
/etc/udev/udev.conf
sysadmin@localhost:~$ ls -lh mybackups/
total 16K
-rw-rw-r-- 1 sysadmin sysadmin  10K Jan 25 04:00 udev.tarf
-rw-rw-r-- 1 sysadmin sysadmin 1.2K Jan 25 04:34 udev.tar.gz
sysadmin@localhost:~$
```

Observa la diferencia de tamaño; la primera copia de seguridad (10 Kbytes) es mayor que la segunda copia de seguridad (1.2 Kbytes).

La opción `-z` hace uso de la utilidad gzip para realizar la compresión.

## 7.2.4 Paso 4
Extraer el contenido de un archivo. Los datos se restauran en el directorio actual por defecto:

```bash
cd mybackups
tar –xvf udev.tar.gz
ls
ls etc
ls etc/udev
ls etc/udev/rules.d
```
Tu resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~$ cd mybackups
sysadmin@localhost:~/mybackups$ ls
udev.tar  udev.tar.gz
sysadmin@localhost:~/mybackups$ tar -xvf udev.tar.gz
etc/udev/
etc/udev/rules.d/
etc/udev/rules.d/70-persistent-cd.rules
etc/udev/rules.d/README
etc/udev/udev.conf
sysadmin@localhost:~/mybackups$ ls
etc  udev.tar  udev.tar.gz
sysadmin@localhost:~/mybackups$ ls etc
udev
sysadmin@localhost:~/mybackups$ ls etc/udev
rules.d  udev.conf
sysadmin@localhost:~/mybackups$ ls etc/udev/rules.d
70-persistent-cd.rules  README
sysadmin@localhost:~/mybackups$
```

Si quieres «regresar» los archivos a su ubicación original, primero puede cd al directorio / , y luego ejecutar el comando tar. Sin embargo, en este ejemplo, esto requeriría que inicies la sesión como administrador porque la creación de los archivos en el directorio /etc  sólo puede realizarse por el administrador.

## 7.2.5 Paso 5
Para añadir un archivo a un archivo empaquetado existente, utiliza la opción `-r` con el comando tar. Ejecuta los siguientes comandos para realizar esta acción y comprobar la existencia del archivo nuevo en el archivo empaquetado tar:

```bash
tar -rvf udev.tar /etc/hosts
tar –tvf udev.tar
```

Tu resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~/mybackups$ tar -rvf udev.tar /etc/hosts
tar: Removing leading `/' from member names
/etc/hosts
sysadmin@localhost:~/mybackups$ tar -tvf udev.tar
drwxr-xr-x root/root         0 2015-01-28 16:32 etc/udev/
drwxr-xr-x root/root         0 2015-01-28 16:32 etc/udev/rules.d/
-rw-r--r-- root/root       306 2015-01-28 16:32 etc/udev/rules.d/70-persistent-c
d.rules
-rw-r--r-- root/root      1157 2012-04-05 19:18 etc/udev/rules.d/README
-rw-r--r-- root/root       218 2012-04-05 19:18 etc/udev/udev.conf
sysadmin@localhost:~/mybackups$
```

## 7.2.6 Paso 6
En los siguientes ejemplos, vas a utilizar los comandos gzip y gunzip para comprimir y descomprimir un archivo. Ejecuta los siguientes comandos para comprimir una copia del archivo words:

```bash
cp /usr/share/dict/words .
ls -l words
gzip words
ls -l words.gz
```

Tu resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~/mybackups$ cp /usr/share/dict/words .
sysadmin@localhost:~/mybackups$ ls -l words
-rw-r--r-- 1 sysadmin sysadmin 938848 Jan 25 07:39 words
sysadmin@localhost:~/mybackups$ gzip words
sysadmin@localhost:~/mybackups$ ls -l words.gz
-rw-r--r-- 1 sysadmin sysadmin 255996 Jan 25 07:39 words.gz
sysadmin@localhost:~/mybackups$
```

Observa que el tamaño del archivo comprimido (255996 bytes en el ejemplo anterior) es mucho menor que el tamaño del archivo original (938848 bytes en el ejemplo anterior).

>Muy importante: Cuando utilizas el comando gzip, el archivo original se sustituye por el archivo comprimido. En el ejemplo anterior, el archivo words fue sustituido por words.gz.
>
>Al descomprimir el archivo, el archivo comprimido será reemplazado por el archivo original.

## 7.2.7 Paso 7
Ejecuta los siguientes comandos para descomprimir el archivo words.gz :

```bash
ls -l words.gz
gunzip words.gz
ls -l words
```

Tu resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~/mybackups$ ls -l words.gz
-rw-r--r-- 1 sysadmin sysadmin 255996 Jan 25 07:39 words.gz
sysadmin@localhost:~/mybackups$ gunzip words.gz
sysadmin@localhost:~/mybackups$ ls -l words
-rw-r--r-- 1 sysadmin sysadmin 938848 Jan 25 07:39 words
sysadmin@localhost:~/mybackups$
```

Linux proporciona un gran número de utilidades de compresión además de gzip/gunzip. Cada uno de ellos tiene pros y contras (compresión más rápida, mejores tasas de compresión, más flexible, más portátil, descompresión más rápida, etc.).

Los comandos gzip/gunzip son muy populares en Linux, pero se debe tener en cuenta que los comandos bzip2/bunzip2 también son muy populares en algunas distribuciones de Linux. Por suerte, la mayor parte de la funcionalidad (la forma de ejecutar los comandos) y las opciones son las mismas que en el caso de gzip/gunzip.

## 7.2.8 Paso 8
El uso de los comandos bzip2 y bunzip2 para comprimir y descomprimir un archivo es muy similar al de los comandos gzip y gunzip. El archivo comprimido se crea con una extensión .bz2. La extensión se retira una vez descomprimido. Ejecuta los siguientes comandos para comprimir una copia del archivo words :

```bash
ls -l words
bzip2 words
ls -l words.bz2
```

Tu resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~/mybackups$ ls -l words
-rw-r--r-- 1 sysadmin sysadmin 938848 Jan 25 07:39 words
sysadmin@localhost:~/mybackups$ bzip2 words
sysadmin@localhost:~/mybackups$ ls -l words.bz2
-rw-r--r-- 1 sysadmin sysadmin 335405 Jan 25 07:39 words.bz2
sysadmin@localhost:~/mybackups$
```

Si se compara el resultado tamaño del archivo .bz2  resultante (335405) con el tamaño del archivo .gz  (255996) en el paso no. 7, se puede observar que gzip hizo un mejor trabajo al comprimir ese archivo en particular.

## 7.2.9 Paso 9
Ejecuta los siguientes comandos para descomprimir el archivo words.bz2 :

```bash
ls -l words.bz2
bunzip2 words.bz2
ls -l words
```

```shell-session
sysadmin@localhost:~/mybackups$ ls -l words.bz2
-rw-r--r-- 1 sysadmin sysadmin 335405 Jan 25 07:39 words.bz2
sysadmin@localhost:~/mybackups$ bunzip2 words.bz2
sysadmin@localhost:~/mybackups$ ls -l words
-rw-r--r-- 1 sysadmin sysadmin 938848 Jan 25 07:39 words
```

Mientras que los archivos empaquetados gzip y bzip se utilizan comúnmente en Linux, el tipo de empaquetamiento zip es más comúnmente utilizado por otros sistemas operativos, como Windows. De hecho, la aplicación Explorador de Windows tiene un soporte integrado para extraer los archivos empaquetados zip.

Por lo tanto, si quieres compartir un archivo con los usuarios de Windows, por lo general se prefiere el uso de empaquetamiento zip. A diferencia de gzip y bzip2, cuando un archivo se comprime con el comando zip, una  copia del archivo original es comprimido y el original permanece sin comprimir.

## 7.2.10 Paso 10
Utiliza el comando zip para comprimir el archivo words:

```bash
zip words.zip words
ls -l words.zip
```

Tu resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~/mybackups$ zip words.zip words
adding: words (deflated 73%)
sysadmin@localhost:~/mybackups$ ls -l words.zip
-rw-rw-r-- 1 sysadmin sysadmin 256132 Jan 25 21:25 words.zip
sysadmin@localhost:~/mybackups$
```

El primer argumento (words.zip en el ejemplo anterior) del comando zip es el nombre del archivo que quieres crear. El resto de los argumentos (words en el ejemplo anterior) son los archivos que quieres colocar en el archivo comprimido.

>Importante: No tienes que utilizar la extensión .zip  con nombre de archivo comprimido, sin embargo, es muy útil para determinar el tipo de archivo. También, se considera de «buen estilo» cuando envías un archivo a otra persona.

## 7.2.11 Paso 11
Comprime el directorio /etc/udev y su contenido con el comando de compresión zip:

```bash
zip -r udev.zip /etc/udev
ls -l udev.zip
```

Tu resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~/mybackups$ zip -r udev.zip /etc/udev
adding: etc/udev/ (stored 0%)
adding: etc/udev/rules.d/ (stored 0%)
adding: etc/udev/rules.d/70-persistent-cd.rules (deflated 29%)
adding: etc/udev/rules.d/README (deflated 50%)
adding: etc/udev/udev.conf (deflated 24%)
sysadmin@localhost:~/mybackups$ ls -l udev.zip
-rw-rw-r-- 1 sysadmin sysadmin 1840 Jan 25 21:33 udev.zip
sysadmin@localhost:~/mybackups$
```

El comando tar que viste anteriormente en este laboratorio desciende automáticamente a través de los subdirectorios de un directorio especificado para ser empaquetado. Con los comandos bzip2, gzip, y zip es necesario especificar la opción `-r` para realizar la recursividad en los subdirectorios.

## 7.2.12 Paso 12
Para ver el contenido de un archivo zip, utiliza la opción `-l` con el comando unzip:

```bash
unzip -l udev.zip
```
Tu resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~/mybackups$ unzip -l udev.zip
Archive:  udev.zip
Length      Date    Time    Name
---------  ---------- -----   ----
0  2015-01-28 16:32   etc/udev/
0  2015-01-28 16:32   etc/udev/rules.d/
306  2015-01-28 16:32   etc/udev/rules.d/70-persistent-cd.rules
1157  2012-04-05 19:18   etc/udev/rules.d/README
218  2012-04-05 19:18   etc/udev/udev.conf
---------                     -------
1681                     5 files
sysadmin@localhost:~/mybackups$
```

## 7.2.13 Paso 13
Para extraer el archivo zip, utiliza el comando unzip sin ninguna opción. En este ejemplo, primero tenemos que eliminar los archivos que se crearon en el ejemplo tar más arriba:

```bash
rm -r etc
unzip udev.zip
```

Tu resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~/mybackups$ rm -r etc
sysadmin@localhost:~/mybackups$ unzip udev.zip
Archive:  udev.zip
creating: etc/udev/
creating: etc/udev/rules.d/
inflating: etc/udev/rules.d/70-persistent-cd.rules
inflating: etc/udev/rules.d/README
inflating: etc/udev/udev.conf
sysadmin@localhost:~/mybackups$
```
