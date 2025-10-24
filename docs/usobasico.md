# Uso básico de Git

## Crear un proyecto

### Crear un programa "Hola Mundo"

Creamos un directorio donde colocar el código

    $ mkdir curso-de-git
    $ cd curso-de-git

Creamos un fichero `hola.py` que muestre Hola Mundo.

```python
print("Hola, Mundo.");
```

### Crear el repositorio

Para crear un nuevo repositorio se usa la orden `git init`

```shell
$ git init
Initialized empty Git repository in /home/cc0gobas/git/curso-de-git/.git/
```

### Añadir la aplicación

Vamos a almacenar el archivo que hemos creado en el repositorio para poder trabajar, después explicaremos para qué sirve cada orden.

```shell
$ git add hola.py
$ git commit -m "Creación del proyecto"
[main (root-commit) e19f2c1] Creación del proyecto
    1 file changed, 2 insertions(+)
    create mode 100644 hola.py
```

!!! example "Estado del repositorio"

    Acabamos de iniciar nuestra rama de cambios y ya tenemos nuestro primer nodo. El siguiente gráfico es una representación visual del estado
    del repositorio. Ahora mismo no entenderás mucho pero poco a poco lo irás haciendo.

    ```mermaid
    gitGraph:
    commit id: "e19f2c1"
    ```

### Comprobar el estado del repositorio

Con la orden `git status` podemos ver en qué estado se encuentran los archivos de nuestro repositorio.

```shell
$ git status
On branch main
nothing to commit, working tree clean
```

Si modificamos el archivo `hola.py`:

```python
import sys

if len(sys.argv) > 1:
    print(f"Hola, {sys.argv[1]}.")
else:
    print("Hola.")
```

Y volvemos a comprobar el estado del repositorio:

```shell
$ git status
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hola.py

no changes added to commit (use "git add" and/or "git commit -a")
```

Git nos indica en color rojo que `hola.py` ha sido modificado y nos lo indica como `Changes not staged for commit`.
Esto nos indica que hemos modificado el archivo original y que ahora tenemos que decidir que hacer con él,
si queremos marcar esos cambios para guardarlos o si queremos restaurar el fichero a su estado anterior.

### Preparar archivos

Vamos a seguir el flujo más habitual, vamos a decirle a _git_ que queremos conservar los cambios. 
Con la orden `git add` indicamos a git que prepare los cambios para que sean almacenados.

```shell
$ git add hola.py
$ git status
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   hola.py
```

Sigue indicandonos que el fichero está modificado, pero ahora en color verde y como `Changes to be committed`.

!!! info
    Esto es lo que se conoce como fase de _stagging_ o preparación.

### Confirmar los cambios

Ahora vamos a guardar definitivamente la actualización de nuestro programa en nuestro repositorio.
Con la orden `git commit` confirmamos los cambios definitivamente.

```shell
$ git commit -m "Parametrización del programa"
[main efc252e] Parametrización del programa
    1 file changed, 5 insertion(+), 2 deletion(-)
$ git status
On branch main
nothing to commit, working directory clean
```

!!! example "Estado del repositorio"

    Así esta el estado de nuestro repositorio. No te preocupes si no obtienes exactamente la misma salida, es completamente normal.
    Lo importante es que el estado del repositorio sea el mismo (con o sin ficheros para confirmar)

    ```mermaid
    gitGraph:
        commit id: "e19f2c1"
        commit id: "efc252e"
    ```

### Diferencias entre _workdir_ y _staging_.

Modificamos nuestra aplicación para que soporte un parámetro por defecto y añadimos los cambios.

```python
import sys

nombre = sys.argv[1] if len(sys.argv) > 1 else "Mundo"
print(f"Hola, {nombre}")
```

Este vez añadimos los cambios a la fase de _staging_ pero sin confirmarlos (_commit_).

```
git add hola.py
```

!!! question

    Si ejecutaras ahora `git status`. ¿Qué debería ocurrir? Ya lo hemos hecho antes.
    Imagina lo que debería pasar repasando el tutorial y comprueba si has acertado.


Volvemos a modificar el programa para indicar con un comentario lo que hemos hecho.

```python
import sys

# El nombre por defecto es Mundo
nombre = sys.argv[1] if len(sys.argv) > 1 else "Mundo"
print(f"Hola, {nombre}")
```

Y vemos el estado en el que está el repositorio

```shell
$ git status
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   hola.py

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hola.py
```

Podemos ver como aparecen el archivo _hola.py_ dos veces. El primero está preparado
para ser confirmado y está almacenado en la zona de _staging_. El segundo indica
que el directorio hola.py está modificado otra vez en la zona de trabajo (_workdir_).

La zona de trabajo es donde tú trabajas, y la zona de _stagging_ es donde trabaja _git_.
Los ficheros que no vayan a la zona de staggig no se guardarán en el repositorio. Cuando un fichero
es distinto en ambas zonas, nos lo indicará para que sepamos que si guardamos los cambios, solo
se guardarán los que confirmamos la primera vez.

!!! warning

    Si volvieramos a hacer un `git add hola.py` sobreescribiríamos los cambios previos
    que había en la zona de _staging_.

Almacenamos los cambios por separado:

```shell
$ git commit -m "Se añade un parámetro por defecto"
[main 3283e0d] Se añade un parámetro por defecto
    1 file changed, 2 insertions(+), 1 deletion(-)
$ git status
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hola.py

no changes added to commit (use "git add" and/or "git commit -a")
$ git add .
$ git status
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   hola.py

$ git commit -m "Se añade un comentario al cambio del valor por defecto"
[main fd4da94] Se añade un comentario al cambio del valor por defecto
    1 file changed, 1 insertion(+)
$ git status
On branch main
nothing to commit, working tree clean
```

!!! tip

    El valor "." despues de `git add` indica que se añadan todos los archivos del directorio actual de forma recursiva. Eso nos evita
    tener que añadir uno a uno cuando tenemos muchos archivos. También puedes hacer `git add directorio/subdirectorio` para confirmar solo
    los cambios de cierta carpeta.
    Cuidado cuando lo uses, asegúrate de que no estás añadiendo archivos que no quieres añadir.

!!! example "Estado del repositorio"

    ```mermaid
    gitGraph:
        commit id: "e19f2c1"
        commit id: "efc252e"
        commit id: "3283e0d"
        commit id: "fd4da94"
    ```


### Ignorando archivos

La orden `git add .` o `git add nombre_directorio` es muy cómoda, ya que nos permite añadir todos los archivos del proyecto o todos los contenidos en un directorio y sus subdirectorios. Es mucho más rápido que tener que ir añadiéndolos uno por uno. El problema es que, si no se tiene cuidado, se puede terminar por añadir archivos innecesarios o con información sensible.

Por lo general se debe evitar añadir archivos que se hayan generado como producto de la compilación del proyecto, los que generen los entornos de desarrollo (archivos de configuración y temporales) y aquellos que contentan información sensible, como contraseñas o tokens de autenticación. Por ejemplo, en un proyecto de `C/C++`, los archivos objeto no deben incluirse, solo los que contengan código fuente y los make que los generen.

Para indicarle a _git_ que debe ignorar un archivo, se puede crear un fichero llamado _.gitignore_, bien en la raíz del proyecto o en los subdirectorios que queramos. Dicho fichero puede contener patrones, uno en cada línea, que especiquen qué archivos deben ignorarse. El formato es el siguiente:

```
# .gitignore
dir1/           # ignora todo lo que contenga el directorio dir1
!dir1/info.txt  # El operador ! excluye del ignore a dir1/info.txt (sí se guardaría)
dir2/*.txt      # ignora todos los archivos txt que hay en el directorio dir2
dir3/**/*.txt   # ignora todos los archivos txt que hay en el dir3 y sus subdirectorios
*.o             # ignora todos los archivos con extensión .o en todos los directorios
```

Cada tipo de proyecto genera sus ficheros temporales, así que para cada proyecto hay un `.gitignore` apropiado. Existen repositorios que ya tienen creadas plantillas. Podéis encontrar uno en [https://github.com/github/gitignore](https://github.com/github/gitignore)

### Ignorando archivos globalmente

Si bien, los archivos que hemos metido en `.gitignore`, deben ser aquellos ficheros temporales o de configuración que se pueden crear durante las fases de compilación o ejecución del programa, en ocasiones habrá otros ficheros que tampoco debemos introducir en el repositorio y que son recurrentes en todos los proyectos. En dicho caso, es más útil tener un _gitignore_ que sea global a todos nuestros proyectos. Esta configuración sería complementaria a la que ya tenemos. Ejemplos de lo que se puede ignorar de forma global son los ficheros temporales del sistema operativo (`*~`, `.nfs*`) y los que generan los entornos de desarrollo.

Para indicar a _git_ que queremos tener un fichero de _gitignore_ global, tenemos que configurarlo con la siguiente orden:

```
git config --global core.excludesfile $HOME/.gitignore_global
```

Ahora podemos crear un archivo llamado `.gitignore_global` en la raíz de nuestra cuenta con este contenido:

```
# Compiled source #
###################
*.com
*.class
*.dll
*.exe
*.o
*.so

# Packages #
############
# it's better to unpack these files and commit the raw source
# git has its own built in compression methods
*.7z
*.dmg
*.gz
*.iso
*.jar
*.rar
*.tar
*.zip

# Logs and databases #
######################
*.log
*.sql
*.sqlite

# OS generated files #
######################
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db
*~
*.swp

# IDEs               #
######################
.idea
.settings/
.classpath
.project
```

## Trabajando con el historial

### Observando los cambios

Con la orden `git log` podemos ver todos los cambios que hemos hecho:

```shell
$ git log
commit fd4da946326fbe8b24e89282ad25a71721bf40f6
Author: Sergio Gómez <sergio@uco.es>
Date:   Sun Jun 16 12:51:01 2013 +0200

    Se añade un comentario al cambio del valor por defecto

commit 3283e0d306c8d42d55ffcb64e456f10510df8177
Author: Sergio Gómez <sergio@uco.es>
Date:   Sun Jun 16 12:50:00 2013 +0200

    Se añade un parámetro por defecto

commit efc252e11939351505a426a6e1aa5bb7dc1dd7c0
Author: Sergio Gómez <sergio@uco.es>
Date:   Sun Jun 16 12:13:26 2013 +0200

    Parametrización del programa

commit e19f2c1701069d9d1159e9ee21acaa1bbc47d264
Author: Sergio Gómez <sergio@uco.es>
Date:   Sun Jun 16 11:55:23 2013 +0200

    Creación del proyecto
```

También es posible ver versiones abreviadas o limitadas, dependiendo de los parámetros:

```shell
$ git log --oneline
fd4da94 Se añade un comentario al cambio del valor por defecto
3283e0d Se añade un parámetro por defecto
efc252e Parametrización del programa
e19f2c1 Creación del proyecto
git log --oneline --max-count=2
git log --oneline --since='5 minutes ago'
git log --oneline --until='5 minutes ago'
git log --oneline --author=sergio
git log --oneline --all
```

Una versión muy útil de `git log` es la siguiente, pues nos permite ver en que lugares está main y HEAD, entre otras cosas:

```shell
$ git log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short
* fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (HEAD, main) [Sergio Gómez]
* 3283e0d 2013-06-16 | Se añade un parámetro por defecto [Sergio Gómez]
* efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
* e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]
```

### Crear alias

Como estas órdenes son demasiado largas, Git nos permite crear alias para crear nuevas órdenes parametrizadas. Para ello podemos configurar nuestro entorno con la orden `git config` de la siguiente manera:

```shell
git config --global alias.hist "log --pretty=format:'%h %ad | %s%d [%an]' --graph --date=short"
```

!!! example

    Puedes configurar incluso alias para abreviar comandos. Algunos ejemplos de alias útiles:

        git config --global alias.br branch
        git config --global alias.co checkout
        git config --global alias.ci commit
        git config --global alias.st "status -u"
        git config --global alias.cane "commit --amend --no-edit"

    Si usas zsh y su plugin, ya tendrás muchos de estos alias.

### Recuperando versiones anteriores

Cada cambio es etiquetado por un hash, para poder regresar a ese momento del estado del proyecto se usa la orden `git checkout`.

```shell
$ git checkout e19f2c1
Note: switching to 'e19f2c1'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

Turn off this advice by setting config variable advice.detachedHead to false

HEAD is now at e19f2c1 Creación del proyecto
$ cat hola.py
print("Hola Mundo");
```

El aviso que nos sale nos indica que estamos en un estado donde no trabajamos en ninguna rama concreta. Eso significa que los cambios que hagamos podrían "perderse" porque si no son guardados en una nueva rama, en principio no podríamos volver a recuperarlos. Hay que pensar que Git es como un árbol donde un nodo tiene información de su nodo padre, no de sus nodos hijos, con lo que siempre necesitaríamos información de dónde se encuentran los nodos finales o de otra manera no podríamos acceder a ellos.

### Volver a la última versión de la rama main.

Usamos `git switch` indicando el nombre de la rama:

```git
$ git switch main
Previous HEAD position was e19f2c1 Creación del proyecto
Switched to branch 'main'
```

### Etiquetando versiones

Para poder recuperar versiones concretas en la historia del repositorio, podemos etiquetarlas, lo cual es más facil que usar un hash. Para eso usaremos la orden `git tag`.

```shell
$ git tag v1
```

Ahora vamos a etiquetar la versión inmediatamente anterior como v1-beta. Para ello podemos usar los modificadores `^` o `~` que nos llevarán a un ancestro determinado. Las siguientes dos órdenes son equivalentes:

```shell
$ git checkout v1^
$ git checkout v1~1
$ git tag v1-beta
```

Si ejecutamos la orden sin parámetros nos mostrará todas las etiquetas existentes.

```shell
$ git tag
v1
v1-beta
```

Y para verlas en el historial:

```shell
$ git hist main --all
* fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1, main) [Sergio Gómez]
* 3283e0d 2013-06-16 | Se añade un parámetro por defecto (HEAD, tag: v1-beta) [Sergio Gómez]
* efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
* e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]
```

!!! example "Estado del repositorio"

    ```mermaid
    gitGraph:
        commit id: "e19f2c1"
        commit id: "efc252e"
        commit id: "3283e0d" tag: "v1-beta"
        commit id: "fd4da94" tag: "v1"
    ```

### Borrar etiquetas

Para borrar etiquetas:

```shell
git tag -d nombre_etiqueta
```

### Visualizar cambios

Para ver los cambios que se han realizado en el código usamos la orden `git diff`. La orden sin especificar nada más, mostrará los cambios que no han sido añadidos aún, es decir, todos los cambios que se han hecho antes de usar la orden `git add`. Después se puede indicar un parámetro y dará los cambios entre la versión indicada y el estado actual. O para comparar dos versiones entre sí, se indica la más antigua y la más nueva. Ejemplo:

```shell
$ git diff v1-beta v1
diff --git a/hola.py b/hola.py
index a31e01f..25a35c0 100644
--- a/hola.py
+++ b/hola.py
@@ -1,5 +1,4 @@
 import sys
 
-# El nombre por defecto es Mundo
 nombre = sys.argv[1] if len(sys.argv) > 1 else "Mundo"
 print(f"Hola, {nombre}")
```
