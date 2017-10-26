# Gitlab, gestor web de repositorios Git

## Descripción del menú

Ya comentamos que Gitlab es un software de gestión de proyectos que usan Git como sistema de control de versiones. Vamos a ver sus distintas partes y funcionalidades.

### Panel de control (Dashboard)

El dashboard o panel de control es la página principal a la que accedemos tras identificarnos. En ella podemos encontrar información sobre:

* Menú principal:
    * Últimos eventos (Push, comentarios, merges, equipo).
    * Proyectos en los que participamos
    * Grupos de proyectos de los que formamos parte
    * Equipos de trabajo en los que participamos
* Submenú _Projects_: Información sobre los proyectos a los que tenemos acceso
* Submenú _Issues_: Información sobre las incidencias.
* Submenú _Merge request_: Información sobre las peticiones de fusión propias o asignadas.
* Submenú _Search_: Búsqueda avanzada
* Submenú _Help_: Ayuda del sistema.

![Panel de control (Dashboard)](gitlab-dashboard.png)

### Página de proyecto

Los proyectos tienen su propia página desde la cual, y dependiendo de los permisos que tengamos podemos acceder a los siguientes submenús:

* Página principal: contiene información de los cambios, además de información general del proyecto (url para clonado, tamaño), etc.
* Submenú  _Files_: Es un explorador de archivos que permite ver el estado del repositorio por ramas y revisiones.
* Submenú _Commits_: Permite ver todos los commits realizados, quién y cuándo lo realizó, que cambios hubo, etc. Permite comparar dos commits, ver las ramas y tags existentes en el proyecto, además de bloquear ramas para escritura (solo administradores de proyecto).
* Submenú _Network_: Permite visualizar el gráfico de progreso del proyecto.
* Submenú _Issues_: Permite la gestión de incidencias (_issues_), hitos (_milestones_) y etiquetas (_labels_).
* Submenú _Merge request_: Información sobre las peticiones de fusión del proyecto.
* Submenú _Wiki_: Permite la creación de un wiki o gestor documental para generar información.
* Submenú _Settings_: Solo para los administradores del proyecto, permite realizar labores de administración como establecer el nombre, descripción, rama del proyecto, transferir a otro usuario o borrar el proyecto. También permite gestionar los miembros del proyecto o los servicios que va a utilizar.

![Página de proyecto](gitlab-proyecto.png)

## Trabajando con un proyecto

Vamos a clonar y configurar nuestra copia del proyecto con el que trabajaremos en grupo:

    [bash]
    $ git clone gitlab@gitlab.uco.es:cc0gobas/miniblog.git
    $ cd miniblog
    $ git flow init -d
    $ composer.phar install
    $ php console create-schema

Y probamos que nuestra aplicación funciona:

    [bash]
    $ php -S localhost:9999 -t web/

Podemos usar dos direcciones para probarla:

* Frontend: `http://localhost:9999/index_dev.php`
* Backend: `http://localhost:9999/index_dev.php/admin/` con usuario admin y contraseña 1234.

La orden `git flow init` nos habrá creado la rama develop y configurado el entorno de trabajo de `git flow`, además nos deja por dejecto en la rama develop. Podemos cambiar de una rama a otra:

    [bash]
    $ git checkout master
    $ git checkout develop

Tenemos que partir de una rama u otra dependiendo del trabajo que vayamos a hacer, como describimos en el capítulo anterior.

### Creando nuevas funcionalidades (feature branch).

Desde la rama develop vamos a crear una nueva funcionalidad. La rama se nombrará de forma descriptiva, y si hay un numero de incidencia (issue) lo llevará al comienzo, separado por un guión.

Vamos a crear una rama para cerrar la incidencia sobre la licencia de uso:

    [bash]
    $ git flow feature start 001-licencia 
    $ echo "LICENCIA MIT" > LICESE
    # el error es intencionado
    $ git commit -m "Archivo de licencia de uso"

Como no es una modificación que interfiera en el programa no vamos a probar nada. Ahora habría que integrar la rama feature en la rama develop, pero hay que tener en cuenta que como desarrolladores *no* tenemos permisos. Tenemos que enviarla al repositorio para que un administrador apruebe el cambio:

    [bash]
    $ git flow feature publish 001-licencia

Eso provoca que una nueva rama se ha subido al repositorio y si entramos en gitlab veremos un botón para crear la petición de fusión. Pulsamos, configuramos si es necesario la petición y la enviamos.

![Create Merge Request](gitlab-mergerequest.png)

Ahora en principio, cabría esperar que el administrador aprobara los cambios, pero podría pasar que nos indicara que cambiemos algo. En ese caso solo habría que modificar la rama y volverla a enviar.

    [bash]
    $ git mv LICESE LICENSE
    $ git commit -m "Fix: Nombre de archivo LICENSE"
    $ git flow feature publish 001-licencia

Ahora sí, el administrador puede aprobar la fusión y borrar la rama del repositorio. Una vez que lo haya hecho, la borramos nosotros de nuestro repositorio local:

    [bash]
    $ git checkout develop
    $ git pull
    $ git flow feature delete 001-licencia
    # Si el cambio no es aceptado, forzamos el borrado:
    $ git flow feature delete -f 001-licencia

Una propuesta de fusión, además de ser ampliada por el que la envió, puede ser ampliada también por el resto del equipo. Vamos a implementar la siguiente incidencia:

    [bash]
    $ git flow feature start 002-about
    $ curl -sSL http://db.tt/UNsrJfT1 | patch -p1

Probamos que la web funciona en:

* `http://localhost:9999/index_dev.php/admin/about/`

Ya podemos hacer el commit y el publish:

    [bash]
    $ git add src/ templates/
    $ git commit -m "Página 'Acerca de' añadida"
    $ git flow feature publish

Esta vez, el administrador decide probar el código antes de aceptarlo. Las siguientes órdenes son desde un usuario para escribir sobre 'develop' aunque para rechazar y probar propuestas no tiene por qué ser así:

    [bash]
    $ git checkout develop
    $ git pull
    $ git checkout feature/002-about
    $ php -S localhost:9999 -t web
    # testear

Podemos hacer un cambio en el template `about.twig` para comprobar que desde otros usuarios también es posible:

    [bash]
    $ git add templates/
    $ git commit -m "Fix: Titulo de la página"

Desde aqui podemos hacer un `git flow feature publish` y aceptar el merge en la página igual que antes o como somos administradores hacer el merge directamente. Aunque lo ideal sería hacer un publish y aceptar el merge vamos a hacerlo directamente 

    [bash]
    $ git flow feature finish 002-about
    $ git push

Podremos ver que el merge se ha cerrado también al hacerlo así, pero el último cambio no se ha quedado reflejado en la historia de commits del merge, aunque sí en el del proyecto (página _Network_).

### Creando ramas de actualizaciones

Tanto las ramas de versión (_release branches_ o RB), como las ramas de parches (_hotfix branches_ o HB), solo pueden ser integradas por los administradores, y es conveniente que sean ellos los que asuman la responsabilidad de crearlas.

La forma de crearlas es la misma para las dos, así que vamos a crear una HB. En esta ocasión, no se nombran con ninguna descripción, sino con el número de versión del producto en la rama master.

    [bash]
    $ git pull
    $ git flow hotfix start 1.0.1
    $ echo 1.0.1 > RELEASE
    $ git commit -a -m "Incrementada versión a 1.0.1"
    $ sed -i 's#2.2.2#2.3.2#' composer.json
    $ git commit -a -m "Fix: Versión de bootstrap con bug de seguridad"
    $ git flow hotfix finish 1.0.1
    $ git push


