## 14.1 Introducción
En el capítulo anterior viste que la información de la cuenta de usuario se almacena en el archivo /etc/passwd y la información de autenticación de usuario (datos de la contraseña) se almacena en el archivo /etc/shadow. La creación de un nuevo usuario puede lograrse añadiendo una nueva línea manualmente a cada uno de estos archivos, pero generalmente no es la técnica recomendada. Mediante el uso de un comando adecuado para agregar un nuevo usuario, estos archivos pueden ser modificados automáticamente y de forma segura. Si fueras a modificar manualmente estos archivos, corres el riesgo de cometer un error que podría impedir el acceso normal de todos los usuarios.

En algunas distribuciones, creando una nueva cuenta de usuario automáticamente se crea una cuenta de grupo para el usuario, llamado un Grupo Privado de Usuario (___UPG___ «User Private Group» en inglés). En estos sistemas, el grupo y nombre de usuario serían los mismos y el único miembro de este nuevo grupo sería el nuevo usuario.

Para las distribuciones que no crean un UPG, los nuevos usuarios reciben el grupo de "users" («_usuarios_» en español) como su grupo primario. El administrador puede crear manualmente cuentas de grupo que sean privadas para el usuario, pero es más común que el administrador cree grupos para varios usuarios que deben colaborar. Las cuentas de usuario pueden ser modificadas en cualquier momento para agregar o quitar membresías de cuenta de grupo, pero los usuarios deben pertenecer por lo menos a un grupo para usarlo como su grupo primario.

Antes de empezar a crear los usuarios, debes planear cómo vas a utilizar los grupos. Los usuarios pueden crearse con membresías en grupos que ya existen o los usuarios existentes pueden ser modificados para tener membresías en grupos existentes.

Si ya tienes previsto qué usuarios y grupos quieres crear, es más eficiente primero crear sus grupos y después crear los usuarios con sus pertenencias a grupos. Por otra parte, si creas primero tus usuarios, y luego sus grupos, necesitarás un paso extra para modificar los usuarios para que sean miembros de sus grupos.


¿Quién es LPI? El Linux Professional Institute (o «Instituto Profesional de Linux» en español) es una organización comprometida en ayudar miembros de la comunidad de Linux y de código abierto, crecer en sus oportunidades profesionales, ofreciendo recursos profesionales y certificaciones de habilidades.
