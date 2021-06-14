## 11.1 Introducción
Un sistema típico de Linux tiene miles de archivos. El Filesystem Hierarchy Standard (o «_estándar jerárquico del sistema de archivos_» en español) (explicado en detalle en un capítulo posterior) proporciona una guía para las distribuciones en cómo organizar estos archivos. En este capítulo verás cómo los sistemas de administración de paquetes de software pueden proporcionar información sobre la ubicación de los archivos pertenecientes a un paquete.

El _kernel_ de Linux es el núcleo del sistema operativo GNU/Linux. Este capítulo explica el papel del kernel de Linux y cómo proporciona información acerca del sistema bajo los pseudo-sistemas de archivo __/proc__ y __/sys__.

Verás cómo cada comando que se ejecuta causa que se ejecute un proceso y podrás ver los procesos ejecutándose con el comando `ps`. También verás discusión sobre cómo el sistema graba o registra mensajes desde los procesos en segundo plano llamados demonios (o «_daemons_» en inglés).

Finalmente, verás cómo visualizar el _ring buffer_ del kernel con el comando `dmesg` para ver los mensajes que contiene.


>__Conociendo__ como Linux promueve carreras profesionales. 86% de los profesionistas de Linux reportan que saber Linux les ha dado más oportunidades de carrera profesional. Y 64% dicen que seleccionaron trabajar con Linux por su omnipresencia en la infraestructura de tecnología del mundo moderno.

![](https://ndg-content-dev.s3.amazonaws.com/media/images/9-LPI-Graphics.png)