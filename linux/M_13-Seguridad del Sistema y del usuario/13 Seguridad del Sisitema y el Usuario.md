## 13.1 Introducción
Las cuentas de usuario están diseñadas para proporcionar seguridad en un sistema operativo Linux. Cada persona en el sistema debe iniciar la sesión utilizando una cuenta de usuario y la cuenta de usuario permite a la persona ya sea acceder a un directorio de archivos específico o niega dicho acceso. Esto se logra mediante los permisos de archivo, un tema del que hablaremos en un capítulo posterior.

Las cuentas de usuario también pertenecen a los grupos que también pueden utilizarse para proporcionar acceso a los archivos o directorios. Cada usuario pertenece al menos a un grupo (a menudo muchos) para permitir más fácilmente que los usuarios compartan los datos almacenados en los archivos con otros usuarios.

Los datos de la cuenta de usuario y de grupo se almacenan en archivos de base de datos. Conocer el contenido de estos archivos es importante, ya que te permitirá entender mejor cuáles de lo usuarios tienen acceso a los archivos y directorios en el sistema. Estos archivos de base de datos también contienen información de seguridad vital que puede afectar la capacidad de un usuario a acceder al sistema (login).

Hay varios comandos que te proporcionarán la capacidad de ver información de la cuenta de grupo y usuario, así como te permiten cambiar de una cuenta de usuario a otra (siempre que tengas la autorización para ello). Estos comandos son valiosos para revisar el uso del sistema, solucionar los problemas de sistema y controlar el acceso no autorizado al sistema.


“No tengo ninguna experiencia de trabajo en Linux, entonces ¿ Cómo obtengo un trabajo?Constatando tus habilidades obteniendo un certificado reconocido por la industria que demuestra a los empleadores que tienes las habilidades de hacer el trabajo.

Puede ser un gran forma de entrar a la primera compañía para que puedas ganar valor con la experiencia en el trabajo.

![](https://ndg-content-dev.s3.amazonaws.com/media/images/11-LPI-Graphics.png)