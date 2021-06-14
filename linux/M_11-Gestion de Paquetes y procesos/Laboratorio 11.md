
## 11.1 Introducción


Este es Lab 11: Ubicaciones de los Datos de Linux. Mediante la realización de esta práctica de laboratorio, los estudiantes aprenderán acerca de la ubicación de la información del kernel, información del proceso, las librerías, los archivos de registro, y los paquetes de software.

En este laboratorio llevarás a cabo las siguientes tareas:

-   Investiga cómo el kernel utiliza al sistema de archivos `/proc`
-   Utiliza el comando `ps` para ver la información del proceso
-   Aprende a gestionar los procesos al iniciarlos, detenerlos y continuarlos
-   Visualización de archivos de registro
-   Gestionar la capacidad de cargar las librerías compartidas

----------------------
## 11.2 El kernel y /proc


En esta tarea explorarás el directorio /proc y los comandos que se comunican con el kernel de Linux. El directorio `/proc` parece ser un directorio ordinario, como el `/usr` o `/etc`, pero no lo es. A diferencia de los directorios `/usr` o `/etc`, que por lo general se escriben en una unidad de disco, el directorio `/proc` es un seudo sistema de archivos ubicado en la memoria de la computadora.

El directorio `/proc` contiene un subdirectorio para cada proceso presente en el sistema. Los programas tales como `pd` y `top` leen la información sobre los procesos en ejecución de estos directorios. El directorio `/proc` también contiene la información acerca del sistema operativo y su hardware en los archivos como `/proc/cpuinfo`, `/proc/meminfo` y `/proc/devices`.

El subdirectorio `/proc/sys` contiene los seudo archivos que se pueden utilizar para alterar la configuración del kernel en ejecución. Ya que estos archivos no son archivos «reales», no se debe utilizar el editor para modificarlos; en su lugar debes utilizar el comando `echo` o `sysctl` para sobrescribir el contenido de estos archivos. Por la misma razón, no intentes ver estos archivos en un editor, sino utiliza el comando `cat` o `sysctl` en lugar.

Para los cambios de configuración permanentes, el kernel utiliza el archivo `/etc/sysctl.conf`. Normalmente, el kernel utiliza este archivo para realizar cambios en los archivos `/proc` al iniciar el sistema.

-------------
### 11.2.1 Paso 1


En esta tarea explorarás algunos de los archivos ubicados en el directorio `/proc`:

	ls /proc

El resultado debe ser similar al siguiente:
```shell-session
sysadmin@localhost:~$ ls /proc
1          cpuinfo      kallsyms    mpt           sysrq-trigger
19         crypto       kcore       mtrr          sysvipc
23         devices      key-users   net           thread-self
25         diskstats    keys        pagetypeinfo  timer_list
41         dma          kmsg        partitions    timer_stats
50         driver       kpagecount  sched_debug   tty
62         execdomains  kpageflags  schedstat     uptime
74         fb           loadavg     scsi          version
acpi       filesystems  locks       self          version_signature
buddyinfo  fs           mdstat      slabinfo      vmallocinfo
bus        interrupts   meminfo     softirqs      vmstat
cgroups    iomem        misc        stat          zoneinfo
cmdline    ioports      modules     swaps
consoles   irq          mounts      sys
sysadmin@localhost:~$
```


Recuerda que los directorios que tienen los números para los nombres representan procesos que se ejecutan en el sistema. El primer proceso es siempre `/sbin/init`, po lo que el directorio `/proc/1` contendrá los archivos con la información sobre el proceso init en ejecución.

El archivo cmdline dentro del directorio del proceso (`/proc/1/cmdline`, por ejemplo) mostrará el comando que se ejecuta. El orden en que se inician otros procesos varía mucho de un sistema al otro. Dado que el contenido de este archivo no contiene un carácter de nueva línea, un comando `echo` se ejecutará para hacer que el prompt vaya a una nueva línea.

-------------
### 11.2.2 Paso 2


Utiliza `cat` y después `ps` para ver la información sobre el proceso `/sbin/init` (identificador de proceso (PID) de 1):

	cat /proc/1/cmdline; echo
	ps -p 1

El resultado debe ser algo como esto:
```shell-session
sysadmin@localhost:~$ cat /proc/1/cmdline; echo
/sbin/init
```

>Nota: El comando `echo` en este ejemplo se ejecuta inmediatamente después del comando `cat`. Ya que no tiene ningún argumento, funciona sólo para poner la siguiente línea de comandos en una nueva línea. Ejecuta sólo el comando `cat` para ver la diferencia.

```shell-session
sysadmin@localhost:~$ ps -p 1
  PID TTY          TIME CMD
    1 ?        00:00:00 init
```

-------------
### 11.2.3 Paso 3


Visualiza el archivo `/proc/cmdline` para ver qué argumentos pasan al kernel durante el arranque:

	cat /proc/cmdline

La salida del comando debe ser similar a esto:

```shell-session
sysadmin@localhost:~$ cat /proc/cmdline
BOOT_IMAGE=/vmlinuz-4.2.0-34-generic root=/dev/mapper/vlabs--vg-root ro cgroup_enable=memory swapaccount=1
sysadmin@localhost:~$
```

------------------------
## 11.3 Gestión de Procesos


En esta tarea verás cómo iniciar y detener los procesos.

-------------
### 11.3.1 Paso 1


Desde la terminal, escribe el siguiente comando:

	ping localhost > /dev/null

El resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~$ ping localhost > /dev/null
```

La salida del ping está siendo redirigida al archivo `/dev/null` (que se conoce comúnmente como _bit bucket_).

Observa que la terminal aparece que se cuelga con este comando. Esto se debe a la ejecución de este comando en el «_primer plano_». El sistema continuará procesando `ping` hasta que el proceso termine o lo suspenda el usuario.

-------------
### 11.3.2 Paso 2


Terminado el proceso en el primer plano presionando `Ctrl-C`.
```shell-session
sysadmin@localhost:~$ ping localhost > /dev/null
^C
sysadmin@localhost:~$
```

-------------
### 11.3.3 Paso 3


A continuación, para iniciar el mismo proceso en el segundo plano, introduce:

	ping localhost > /dev/null &

El resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~$ ping localhost > /dev/null &
[1] 158
```

Al añadir el signo `&` al final del comando, el proceso se inicia en el segundo plano y permite al usuario mantener el control de la terminal.

>Una forma más fácil de entrar al comando anterior sería aprovechar el historial de comandos. Podrías haber presionado la tecla de flecha arriba ↑ en el teclado, añadir un Espacio y `&` al final del comando y luego presionar la tecla Entrar. Esto te ahorra tiempo al introducir comandos similares.

Observa que el comando anterior devuelve la siguiente información:

	[1] 158

Esto significa que este proceso tiene un número de trabajo 1 (como lo muestra la salida `[1]`) y un identificador de proceso (PID) de `158`. Cada _terminal/shell_ tendrá sus números de trabajo únicos. El PID vale para todo el sistema; cada proceso tiene un número de identificación único.

Esta información es importante al realizar ciertas manipulaciones del proceso, tales como parar los procesos o cambiar su valor de prioridad.

>Nota: Tu ID de proceso probablemente será diferente de aquel en el ejemplo.

-------------
### 11.3.4 Paso 4


Para ver qué comandos se ejecutan en la terminal actual, escribe el siguiente comando:

	jobs

El resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~$ jobs
[1]+  Running                 ping localhost > /dev/null &
```


-------------
### 11.3.5 Paso 5


A continuación, inicia otro comando `ping` en segundo plano escribiendo lo siguiente:

ping localhost > /dev/null &

El resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~$ ping localhost > /dev/null &
[2] 100
```
Observa el diferente número de trabajo y el ID de proceso para este nuevo comando.

-------------
### 11.3.6 Paso 6


Ahora, debe haber dos comandos `ping` ejecutándose en segundo plano. Para verificarlo, emite el comando `jobs` de nuevo:

	jobs

El resultado debe ser similar al siguiente:
```shell-session
sysadmin@localhost:~$ jobs
[1]-  Running                 ping localhost > /dev/null &
[2]+  Running                 ping localhost > /dev/null &
```

-------------
### 11.3.7 Paso 7


Una vez que hayas comprobado que dos comandos `ping` se ejecutan, lleva al primer comando al primer plano escribiendo lo siguiente:

	fg %1

El resultado debe ser similar al siguiente:
```shell-session
sysadmin@localhost:~$ fg %1
ping localhost > /dev/null
```

-------------
### 11.3.8 Paso 8


Observa que, una vez más, el comando `ping` ha tomado el control de la terminal. Para suspender (pausar) el proceso y recuperar el control de la terminal, escribe Ctrl-Z:

```shell-session
sysadmin@localhost:~$ fg %1
ping localhost > /dev/null
^Z
[1]+  Stopped                 ping localhost > /dev/null
```

-------------
### 11.3.9 Paso 9


Para que este proceso continúe ejecutándose en segundo plano, ejecuta el siguiente comando:

	bg %1

El resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~$ bg %1
[1]+ ping localhost > /dev/null &
```

---------------
### 11.3.10 Paso 10


Emite el comando `jobs` de nuevo para verificar dos procesos en ejecución:

	jobs

El resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~$ jobs
[1]-  Running                 ping localhost > /dev/null &
[2]+  Running                 ping localhost > /dev/null &
```

---------------
### 11.3.11 Paso 11


A continuación, inicia el comando `ping` escribiendo lo siguiente:

	ping localhost > /dev/null &

El resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~$ ping localhost > /dev/null &
[3] 101
```

---------------
### 11.3.13 Paso 13


Utilizando el número de trabajo, detenga el último comando `ping` con el comando `kill` y verifica que se haya detenido la ejecución del comando `jobs`:

	kill %3
	jobs

El resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~$ kill %3
sysadmin@localhost:~$ jobs
[1]   Running                 ping localhost > /dev/null &
[2]-  Running                 ping localhost > /dev/null &
[3]+  Terminated              ping localhost > /dev/null

---------------```
### 11.3.14 Paso 14


Por último, puedes detener todos los comandos `ping` con el comando `killall`. Después de ejecutar el comando `killall`, espera unos instantes, y luego ejecuta el comando `jobs` para comprobar que todos los procesos se hayan detenido:

	killall ping
	jobs

El resultado debe ser similar al siguiente:
```shell-session
sysadmin@localhost:~$ killall ping
[1]-  Terminated              ping localhost > /dev/null
[2]+  Terminated              ping localhost > /dev/null
sysadmin@localhost:~$ jobs
sysadmin@localhost:~$
```

----------------------------------------------
## 11.4 Uso del Comando Top para Ver los Procesos


En esta tarea vas a utilizar el comando `top` para trabajar con los procesos. Por defecto, el programa `top` ordena los procesos en orden descendente del porcentaje de uso de la CPU, por lo que los programas de mayor actividad estarán en la parte superior de tu lista.

-------------
### 11.4.1 Paso 1


Desde la ventana de la terminal, escribe los siguientes comandos:

	ping localhost > /dev/null &
	ping localhost > /dev/null &

El resultado debe ser similar al siguiente:
```shell-session
sysadmin@localhost:~$ ping localhost > /dev/null &
[1] 112
sysadmin@localhost:~$ ping localhost > /dev/null &
[2] 113
```

>Toma nota de la salida PID según los comandos anteriores! Serán diferentes a los ejemplos que estamos proporcionando. Va a utilizar los PID en los pasos posteriores.

-------------
### 11.4.2 Paso 2


A continuación, inicia el comando `top` escribiendo lo siguiente en la terminal:

	top

El resultado debe ser similar al siguiente:

```shell-session
top - 00:56:34 up  8:18,  1 user,  load average: 0.09, 0.14, 0.20
Tasks:  10 total,   1 running,   9 sleeping,   0 stopped,   0 zombie
Cpu(s):  2.1%us,  1.8%sy,  0.0%ni, 96.1%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
Mem:  65969788k total,  7629004k used, 58340784k free,   266340k buffers
Swap:  2097148k total,        0k used,  2097148k free,   939076k cached

  `PID` `USER`      `PR`  `NI``VIRT`  `RES`  `SHR` `S` `%CPU` `%MEM`    `TIME+`  `COMMAND`
    1 root      20   0 17868 2872 2624 S    0  0.0   0:00.14 init
   19 syslog    20   0  171m 2796 2420 S    0  0.0   0:00.05 rsyslogd
   23 root      20   0 19120 2020 1824 S    0  0.0   0:00.00 cron
   25 root      20   0 50048 3416 2812 S    0  0.0   0:00.00 sshd
   41 bind      20   0  376m  19m 5988 S    0  0.0   0:00.04 named
   50 root      20   0 54460 2584 2176 S    0  0.0   0:00.00 login
   62 sysadmin  20   0 18084 3200 2700 S    0  0.0   0:00.01 bash
  112 sysadmin  20   0  6504 1824 1688 S    0  0.0   0:00.46 ping
  113 sysadmin  20   0  6504 1788 1656 S    0  0.0   0:00.40 ping
  114 sysadmin  20   0 17212 2240 2008 R    0  0.0   0:00.01 top
```
>__Nota__: La salida del comando top cambia cada 2 segundos.

-------------
### 11.4.3 Paso 3


El comando `top` es un programa interactivo, lo que significa que puedes emitir comandos dentro del programa. Vas a utilizar el comando `top` para terminar los procesos `ping`. Primero introduce de la letra `k`. Observa que apareció un prompt debajo de `Swap`:

```shell-session
top - 01:52:14 up  9:13,  1 user,  load average: 0.04, 0.11, 0.19
Tasks:  10 total,   1 running,   9 sleeping,   0 stopped,   0 zombie
Cpu(s):  4.2%us,  2.6%sy,  0.0%ni, 93.0%id,  0.1%wa,  0.0%hi,  0.1%si,  0.0%st
Mem:  65969788k total,  8137188k used, 57832600k free,   286512k buffers
Swap:  2097148k total,        0k used,  2097148k free,  1110844k cached
PID to kill:
 `PID` `USER`       `PR`  `NI`  `VIRT`  `RES`  `SHR` `S` `%CPU` `%MEM`    `TIME+`  `COMMAND`
   1 root      20   0 17868 2872 2624 S    0  0.0   0:00.14 init
   19 syslog    20   0  171m 2796 2420 S    0  0.0   0:00.07 rsyslogd
   23 root      20   0 19120 2020 1824 S    0  0.0   0:00.00 cron
   25 root      20   0 50048 3416 2812 S    0  0.0   0:00.00 sshd
   41 bind      20   0  376m  19m 5988 S    0  0.0   0:00.07 named
   50 root      20   0 54460 2584 2176 S    0  0.0   0:00.00 login
   62 sysadmin  20   0 18084 3200 2700 S    0  0.0   0:00.01 bash
  103 sysadmin  20   0  6504 1824 1688 S    0  0.0   0:00.96 ping
  104 sysadmin  20   0  6504 1788 1656 S    0  0.0   0:00.85 ping
  108 sysadmin  20   0 17212 2240 2008 R    0  0.0   0:01.03 top

```

-------------
### 11.4.4 Paso 4


En el prompt `PID to kill`: escribe el PID del primer proceso `ping` en ejecución, y a continuación, presiona Entrar. Observa que el prompt cambia como a continuación:

![](https://ndg-content-dev.s3.amazonaws.com/media/images/labs/linux-essentials/lab11_1.gif)

-------------
### 11.4.5 Paso 5


En el prompt `Kill PID with signal [15]`: introduce la señal para enviar a este proceso. En este caso, solamente presiona la tecla Entrar para utilizar la señal por defecto. Ten en cuenta que el primer comando `ping` se elimina y sólo un comando `ping` permanece en el listado (es posible que tengas que esperar unos segundos mientras el comando `top` actualiza):

![](https://ndg-content-dev.s3.amazonaws.com/media/images/labs/linux-essentials/lab11_2.gif)

>__Para considerar__
Existen diferentes tipos de valores numéricos que se pueden enviar a un proceso. Estos son los valores predefinidos, cada uno con un significado diferente. Si quieres obtener más información sobre estos valores, teclea `man kill` en la ventana de la terminal.
El símbolo indica que la señal por defecto es la señal indicada por la terminar `SIGTERM` o el número `15`.

-------------
### 11.4.6 Paso 6


A continuación, termina los procesos restantes `ping` como antes, excepto que esta vez, en el prompt de la señal `Kill PID with signal [15]`: utiliza el valor de `9` en lugar de aceptar el valor predeterminado `15`. Presiona Entrar para aceptar y entrar.

![](https://ndg-content-dev.s3.amazonaws.com/media/images/labs/linux-essentials/lab11_3.gif)

>__Para considerar__
>La señal de matar `9` o `SIGKILL` es una señal «_contundente_» que no puede ser ignorada, a diferencia del valor por defecto de `15`. Observa que todas las referencias al comando `ping` se han eliminado del `top`.

-------------
### 11.4.7 Paso 7


Introduce `q` para salir del comando `top`. La siguiente pantalla refleja que ambos comandos `ping` fueron terminados:
```bash
[1]-  Terminated              ping localhost > /dev/null
[2]+  Killed                  ping localhost > /dev/null
```

------------------------------------------------
## 11.5 Uso pkill y kill para terminar los procesos


En esta tarea, vamos a seguir trabajando con los procesos. Vas a utilizar `pkill` y `kill` para terminar los procesos.

-------------
### 	11.5.1 Paso 1


Para empezar, escribe los siguientes comandos en la terminal:

	sleep 888888 &
	sleep 888888 &

El resultado debe ser similar al siguiente:
```shell-session
sysadmin@localhost:~$ sleep 888888 &
[1] 85
sysadmin@localhost:~$ sleep 888888 &
[2] 86
sysadmin@localhost:~$
```
El comando `sleep` se utiliza normalmente para hacer una pausa en un programa (script shell) por un período de tiempo específico. En este caso se utiliza sólo para proporcionar un comando que se tarda mucho tiempo en ejecutarse.

>Asegúrate de tener en cuenta los PID en el sistema de los procesos `sleep` para los siguientes pasos! Tus PID serán diferentes a los demostrados en el laboratorio.

-------------
### 11.5.2 Paso 2


A continuación, determina qué trabajos se están ejecutando actualmente escribiendo:

	jobs

El resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~$ jobs
[1]-  Running                 sleep 888888 &
[2]+  Running                 sleep 888888 &
sysadmin@localhost:~$
```

-------------
### 11.5.3 Paso 3


Ahora, utiliza el comando `kill` para detener la primera instancia del comando `sleep` escribiendo lo siguiente (sustituye PID con el ID de proceso de tu primer comando `sleep`). También, ejecuta el comando `jobs` para verificar que el proceso se haya detenido:

	kill PID
	jobs

El resultado debe ser similar al siguiente:
```shell-session
sysadmin@localhost:~$ ps                                                          PID TTY          TIME CMD
   62 ?        00:00:00 bash
   89 ?        00:00:00 sleep
   90 ?        00:00:00 sleep
   91 ?        00:00:00 ps
sysadmin@localhost:~$ kill 89
sysadmin@localhost:~$ jobs
[1]-  Terminated              sleep 888888
[2]+  Running                 sleep 888888 &
sysadmin@localhost:~$
```
>__Consejo útil__: Si recuerdas el PID del primer proceso, sólo tienes que introducir el comando `ps` (proceso) tal como se muestra arriba.

-------------
### 11.5.4 Paso 4


A continuación, utiliza el comando `pkill` para terminar el comando `sleep` restante, utilizando el nombre del programa en lugar del PID:

	pkill -15 sleep

El resultado debe ser similar al siguiente:
```shell-session
sysadmin@localhost:~$ pkill -15 sleep
[2]+  Terminated              sleep 888888
sysadmin@localhost:~$
```

------------------------------------------------------
## 11.6 Usando ps para Seleccionar y Ordenar los procesos


Puedes utilizar el comando `ps` para ver los procesos. De forma predeterminada, el comando `ps` sólo mostrará los procesos ejecutados en el shell actual.

-------------
### 11.6.1 Paso 1


Inicia un proceso en segundo plano utilizando `ping` y visualiza los procesos actuales utilizando el comando `ps`:

	ping localhost > /dev/null &
	ps

El resultado debe ser similar al siguiente:
```shell-session
sysadmin@localhost:~$ ping localhost > /dev/null &
[1] 98
sysadmin@localhost:~$ ps
  PID TTY          TIME CMD
   62 ?        00:00:00 bash
   98 ?        00:00:00 ping
   99 ?        00:00:00 ps
```
>__Anota el PID del `ping`, ya va a utilizar el PID en un paso posterior__.

-------------
### 11.6.2 Paso 2


Ejecuta el comando `ps` usando la opción `-e`, para que se muestren todos los procesos.

	ps -e

El resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~$ ps -e
  PID TTY          TIME CMD
    1 ?        00:00:00 init
   19 ?        00:00:00 rsyslogd
   23 ?        00:00:00 cron
   25 ?        00:00:00 sshd
   41 ?        00:00:00 named
   50 ?        00:00:00 login
   62 ?        00:00:00 bash
   98 ?        00:00:00 ping
  100 ?        00:00:00 ps
```

>Debido a que este entorno es de un sistema operativo virtualizado, hay muchos menos procesos que lo que normalmente se muestra con Linux corriendo directamente en el hardware.

-------------
### 11.6.3 Paso 3


Utiliza el comando `ps` con la opción `-o` para especificar las columnas de salida.

	ps -o pid,tty,time,%cpu,cmd

El resultado debe ser similar al siguiente:
```shell-session
sysadmin@localhost:~$ ps -o pid,tty,time,%cpu,cmd
 PID TT           TIME %CPU CMD
   62 ?        00:00:00  0.0 -bash
   98 ?        00:00:00  0.0 ping localhost
  102 ?        00:00:00  0.0 ps -o pid,tty,time,%cpu,cmd
```

-------------
### 11.6.4 Paso 4


Utilizar la opción `--sort` para especificar el orden de la(s) columna(s). De manera predeterminada, una columna especificada para la clasificación se ordenará de manera ascendente, esto puede ser forzado colocando el símbolo plus `+` que aparece delante del nombre de la columna. Para especificar un orden descendente, utiliza el signo menos `-` delante del nombre de columna.

Ordena la salida del `ps` por `%mem`:

	ps -o pid,tty,time,%mem,cmd --sort %mem

El resultado debe ser similar al siguiente:
```shell-session
sysadmin@localhost:~$ ps -o pid,tty,time,%mem,cmd --sort %mem
  PID TT           TIME %MEM CMD
  103 ?        00:00:00  0.0 ps -o pid,tty,time,%mem,cmd --sort %mem
   98 ?        00:00:00  0.0 ping localhost
   62 ?        00:00:00  0.0 -bash
```

-------------
### 11.6.5 Paso 5


Mientras que el comando `ps` puede mostrar el porcentaje de memoria utilizado por el proceso, el comando `free` mostrará el uso total de la memoria del sistema:

	free

El resultado debe ser similar al siguiente:
```shell-session
sysadmin@localhost:~$ free
             total       used       free     shared    buffers     cached
Mem:      65969788    6242744   59727044          0     300852    1062284
-/+ buffers/cache:    4879608   61090180
Swap:      2097148          0    2097148
```

-------------
### 11.6.6 Paso 6


Detén el comando `ping` con el siguiente comando `kill` y verifica con el comando `jobs`:

	kill PID
	jobs

El resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~$ kill 98
sysadmin@localhost:~$ jobs
[1]+  Terminated              ping localhost > /dev/null
```

-----------------------------------------------
## 11.7 Visualización de los Registros del Sistema


Los registros del sistema son críticos para muchas tareas, incluyendo la solución de problemas del sistema operativo y para garantizar que tu sistema es seguro. Saber dónde se almacenan los archivos de registro del sistema y la forma de mantenerlos es importante para un administrador de sistemas.

Hay dos daemons que manejan los mensajes de registro: el daemon `syslogd` y el daemon `klogd`. Normalmente, no tienes que preocuparte por el `klogd`; éste sólo se ocupa de los mensajes de registro del kernel y envía su información de registro al daemon `syslogd`.

Los mensajes generados por el kernel en tiempo de arranque se almacenan en el archivo `/var/log/dmesg`. El comando `dmesg` permite la visualización de los mensajes actuales del kernel, así como proporcionar el control de si estos mensajes aparecerán en una ventana de la terminal de la consola.

El archivo de registro principal que se escribe por `syslogd` es `/var/log/messages`.

Además del registro realizado por `syslogd`, muchos otros procesos realizan su propio registro. Algunos ejemplos de procesos que hacen su propio registro incluyen el servidor web Apache (el archivo de registro se encuentra en el directorio `/var/log/httpd`), el Sistema de Impresión Común de Unix (`/var/log/cups`) y el daemon auditd (`/var/log/audit`).

>Nota: En los sistemas de CentOS, el `syslogd` se llama `rsyslogd`.

-------------
### 11.7.1 Paso 1


Debido a que los próximos comandos que se ejecutarán en este laboratorio requieren derechos de superusuario, utiliza el comando `su` para cambiar a la cuenta de root:

	su - root
	{Introduce la contraseña: netlab123}

```shell-session
sysadmin@localhost:~$ su - root
Password:
root@localhost:~#
```

-------------
### 11.7.2 Paso 2


Los registros del sistema se almacenan en el directorio `/var/log`. Lista los archivos en este directorio:

	ls /var/log

```shell-session
root@localhost:~# ls /var/log
alternatives.log  boot           cron.log  faillog   lastlog   news     wtmp
apt               bootstrap.log  dmesg     fsck      mail.err  syslog
auth.log          btmp           dpkg.log  kern.log  mail.log  upstart
root@localhost:~#
```

-------------
### 11.7.3 Paso 3


Cada archivo de registro representa un servicio o función. Por ejemplo, el archivo `auth.log` muestra información sobre la autorización o la autenticación, tal como los intentos de inicio de sesión del usuario. Los nuevos datos se almacena en la parte inferior del archivo. Ejecuta los siguientes comandos para ver un ejemplo:

	ssh localhost
	{En el primer prompt, introduce yes}
	{En el segundo prompt, introduce abc}
	{En el tercer prompt, introduce abc}
	{En el cuarto prompt, introduce abc}
	tail -5 /var/log/auth.log

```shell-session
root@localhost:~# ssh localhost
The authenticity of host 'localhost (::1)' can't be established.
ECDSA key fingerprint is 5f:e2:43:0f:f9:26:e5:d5:77:ba:9e:95:72:9e:ee:64.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.
root@localhost's password:
Permission denied, please try again.
root@localhost's password:
Permission denied, please try again.
root@localhost's password:
Permission denied (publickey,password).
root@localhost:~# tail -5 /var/log/auth.log
Apr  8 20:25:13 localhost sshd[117]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=localhost  user=root
Apr  8 20:25:16 localhost sshd[117]: Failed password for root from ::1 port 58940 ssh2
Apr  8 20:25:28  sshd[117]: last message repeated 2 times
Apr  8 20:25:28 localhost sshd[117]: Connection closed by ::1 [preauth]
Apr  8 20:25:28 localhost sshd[117]: PAM 2 more authentication failures; logname= uid=0 euid=0 tty=ssh ruser= rhost=localhost  user=root
root@localhost:~#

```
El comando `ssh` se utiliza para los datos generados en el archivo `/var/log/auth.log`. Ten en cuenta que los intentos fallidos de conexión se registran en el archivo `/var/log/auth.log`.


-------------
### 11.7.4 Paso 4


Para ver otro ejemplo de las entradas de registro, ejecuta los siguientes comandos:

	crontab -e

A continuación, agrega la siguiente línea al documento (recuerda que i te permitirá entrar al modo de inserción) y luego guardálo y salir con ESC, `:wq`, Enter:

	0 2 * * 0 who >> /tmp/whothere

Después de haber completado los cambios, visualiza el archivo de registro para el servicio `crontab`:

	crontab -l | tail -2
	tail /var/log/cron.log

```shell-session
root@localhost:~# crontab -l | tail -2
# m h  dom mon dow   command
  0 2   *   *   0    who >> /tmp/whothere
root@localhost:~#   tail /var/log/cron.log
Apr  8 17:33:32 localhost /usr/sbin/cron[22]: (CRON) INFO (pidfile fd = 3)
Apr  8 17:33:32 localhost /usr/sbin/cron[23]: (CRON) STARTUP (fork ok)
Apr  8 17:33:32 localhost /usr/sbin/cron[23]: (CRON) INFO (Running @reboot jobs)
Apr  8 18:17:01 localhost /USR/SBIN/CRON[79]: (root) CMD (   cd / && run-parts -
-report /etc/cron.hourly)
Apr  8 19:17:02 localhost /USR/SBIN/CRON[82]: (root) CMD (   cd / && run-parts -
-report /etc/cron.hourly)
Apr  8 20:17:01 localhost /USR/SBIN/CRON[99]: (root) CMD (   cd / && run-parts -
-report /etc/cron.hourly)
Apr  8 20:31:18 localhost crontab[121]: (root) BEGIN EDIT (root)
Apr  8 20:32:42 localhost crontab[121]: (root) REPLACE (root)
Apr  8 20:32:42 localhost crontab[121]: (root) END EDIT (root)
Apr  8 20:33:19 localhost crontab[132]: (root) LIST (root)
root@localhost:~#
```


-------------
### 11.7.5 Paso 5


Visualiza las últimas cinco líneas del archivo `/var/log/dmesg` para ver los mensajes del kernel desde el momento del arranque y ejecuta el comando `dmesg` canalizado al comando `tail` para ver los cinco últimos mensajes del kernel:

	tail -5 /var/log/dmesg
	dmesg | tail -5

```shell-session
root@localhost:~# tail -5 /var/log/dmesg
[    2.922003] type=1400 audit(1386098331.347:10): apparmor="STATUS" operation="profile_load" name="/usr/sbin/tcpdump" pid=848 comm="apparmor_parser"
[    2.989112] Bridge firewalling registered
[    3.007035] ip_tables: (C) 2000-2006 Netfilter Core Team
[    3.010733] nf_conntrack version 0.5.0 (16384 buckets, 65536 max)
[    3.020096] input: ImPS/2 Generic Wheel Mouse as /devices/platform/i8042/serio1/input/input2
root@localhost:~# dmesg | tail -5
[279447.718341] device veth0pl17180 left promiscuous mode
[279447.718408] br998ad950-b830: port 1(veth0pl17180) entered disabled state
[279448.519497] bre2e72298-4b5e: port 1(veth0pl17664) entered disabled state
[279448.525087] device veth0pl17664 left promiscuous mode
[279448.525091] bre2e72298-4b5e: port 1(veth0pl17664) entered disabled state
root@localhost:~#
```
En este momento probablemente estarás pensando «¿Qué realmente significan todos estos mensajes?». La respuesta a esa pregunta no es simple, sin embargo lo importante de esta lección no es explicar el significado de todos los mensajes del registro, sino más enseñar dónde encontrar los mensajes de registro.

A medida que adquieras más experiencia en Linux, comenzarás a solucionar los problemas. En la mayoría de los casos primero buscarás es los archivos de registro.

Con el fin de proporcionar un ejemplo de solución de los problemas realistas, sigue el siguiente conjunto de tareas.

-------------
### 11.7.6 Paso 6


Introduce el siguiente comando para deshabilitar la posibilidad de que el administrador pueda crear las entradas `crontab` y regresa al usuario administrador del sistema:

	echo "sysadmin" > /etc/cron.deny
	exit

```shell-session
root@localhost:~# echo "sysadmin" > /etc/cron.deny
root@localhost:~# exit
logout
sysadmin@localhost:~$
```

-------------
### 11.7.7 Paso 7


Intenta a ejecutar el siguiente comando `crontab`:

	crontab -e

```shell-session
sysadmin@localhost:~$ crontab -e
You (sysadmin) are not allowed to use this program (crontab)
See crontab(1) for more information
sysadmin@localhost:~$
```

>Nota: Este comando produce un error debido a la entrada en el `/etc/cron.deny`. Si existe un nombre de usuario en este archivo, entonces ese usuario no puede utilizar el comando `crontab`.

-------------
### 11.7.8 Paso 8


En lugar de cambiar de usuario al root con el comando `su`, utiliza sudo para ejecutar el siguiente comando con los privilegios de root:

	sudo tail -5 /var/log/cron.log
	{Introduce la contraseña: netlab123}

```shell-session
sysadmin@localhost:~$ sudo tail -5 /var/log/cron.log
[sudo] password for sysadmin:
Apr  8 20:31:18 localhost crontab[121]: (root) BEGIN EDIT (root)
Apr  8 20:32:42 localhost crontab[121]: (root) REPLACE (root)
Apr  8 20:32:42 localhost crontab[121]: (root) END EDIT (root)
Apr  8 20:33:19 localhost crontab[132]: (root) LIST (root)
Apr  8 20:47:06 localhost crontab[139]: (sysadmin) AUTH (crontab command not allowed)
sysadmin@localhost:~$
```
Como puedes ver en la última línea de la salida del comando `tail`, el usuario administrador del sistema no tiene permiso para utilizar el comando `crontab`.

-------------------------------
## 11.8 La Bibliotecas Compartidas


Las librerías compartidas son archivos que contienen código que los programan ejecutables pueden enlazar con el fin de utilizar ese código. Debido a que los múltiples programas a menudo enlazan con un único archivo de la librería, esto ayuda a reducir la cantidad del espacio necesario para este código ya que cada programa no tiene que tener su propia copia del código de la librería.

Estos archivos de la librería se almacenan con más frecuencia en los directorios `/lib` y `/usr/lib`. Los directorios adicionales se pueden añadir mediante la edición del archivo de configuración, `/etc/ld.so.conf`. También puedes crear archivos con nombres que terminan en `.conf` y colocarlos en el directorio `/etc/ld.so.conf.d`. Por último, también puedes configurar la variable de entorno `LD_LIBRARY_PATH`.

-------------
### 11.8.1 Paso 1


Cuando se ejecuta como usuario root, el comando `ldconfig` se puede utilizar para actualizar el caché y los enlaces simbólicos para las librerías compartidas en el sistema. Como un usuario normal, vas a ejecutar el comando `ldconfig` para imprimir la lista de las librerías compartidas:

	ldconfig -p | less

> Recuerda presionar q para salir del paginador `less`.

-------------
### 11.8.2 Paso 2


Con el fin de ver qué librerías están vinculadas a un ejecutable, tal como `/bin/bash`, ejecuta el comando `ldd`:

	ldd /bin/bash

El resultado debe ser similar al siguiente:

```shell-session
sysadmin@localhost:~$ ldd /bin/bash
        linux-vdso.so.1 =>  (0x00007ffce6fbd000)
        libtinfo.so.5 => /lib/x86_64-linux-gnu/libtinfo.so.5 (0x00007fb32ae94000)
        libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007fb32ac90000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fb32a8d1000)
        /lib64/ld-linux-x86-64.so.2 (0x0000563ddfa4e000)
sysadmin@localhost:~$
```
