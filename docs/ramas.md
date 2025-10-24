# Ramas

## Administración de ramas

### Crear una nueva rama

Cuando vamos a trabajar en una nueva funcionalidad, es conveniente hacerlo en una nueva rama, para no modificar la rama principal y dejarla inestable. Aunque la orden para manejar ramas es `git branch` podemos usar también `git switch`.

Vamos a crear una nueva rama:

```
git branch hola
```

!!! example "Grafo del repositorio"
    ```mermaid
    gitGraph:
       commit id: "e19f2c1"
       commit id: "efc252e"
       commit id: "3283e0d" tag: "v1-beta"
       commit id: "fd4da94" tag: "v1"
       commit id: "96a39df"
       commit id: "8c2a509"
       branch hola
    ```

!!! info

    Si usamos `git branch` sin ningún argumento, nos devolverá la lista de ramas
    disponibles.

La orden anterior no devuelve ningún resultado y tampoco nos cambia de rama, para eso
debemos usar _switch_:

    $ git switch hola
    Switched to branch 'hola'

!!! tip

    Hay una forma más rapida de hacer ambas acciones en un solo paso. Con el parámetro
    `-c` de `git switch` podemos cambiarnos a una rama que, si no existe, se crea
    instantáneamente.

        $ git switch -c hola
        Switched to a new branch 'hola'

### Modificaciones en la rama secundaria

Añadimos un nuevo archivo en el directorio `lib` llamado `hola_mundo.py`:

```python
class HolaMundo:
    def __init__(self, nombre):
        self.nombre = nombre

    def __str__(self):
        return f"Hola, {self.nombre}."
```

Y modificamos `hola.py`:

```python
import sys

# Autor: Sergio Gómez <sergio@uco.es>
# El nombre por defecto es Mundo
from HolaMundo import HolaMundo

nombre = sys.argv[1] if len(sys.argv) > 1 else "Mundo"
print(HolaMundo(nombre))
```

Podríamos confirmar los cambios todos de golpe, pero lo haremos de uno en uno, con su comentario.

    $ git add lib/hola_mundo.py

    $ git commit -m "Añadida la clase HolaMundo"
    [hola 6932156] Añadida la clase HolaMundo
     1 file changed, 6 insertions(+)
     create mode 100644 lib/hola_mundo.py

    $ git add lib/hola.py

    $ git commit -m "hola usa la clase HolaMundo"
    [hola 9862f33] hola usa la clase HolaMundo
     1 file changed, 3 insertions(+), 1 deletion(-)

!!! example "Grafo del repositorio"
    ```mermaid
    gitGraph:
       commit id: "e19f2c1"
       commit id: "efc252e"
       commit id: "3283e0d" tag: "v1-beta"
       commit id: "fd4da94" tag: "v1"
       commit id: "96a39df"
       commit id: "8c2a509"
       branch hola
       commit id: "6932156"
       commit id: "9862f33"
    ```

Y ahora con la orden `git switch` podemos movernos entre ramas:

    $ git switch main
    Switched to branch 'main'

    $ git switch hola
    Switched to branch 'hola'

Comprueba como los ficheros cambian cuando cambiamos de rama.

### Modificaciones en la rama main

Podemos volver y añadir un nuevo archivo a la rama principal:

    $ git switch main
    Switched to branch 'main'

Creamos un archivo llamado `README.md` en la raíz de nuestro proyecto con el siguiente contenido:

```md
# Curso de GIT

Este proyecto contiene el curso de introducción a GIT
```

Y lo añadimos a nuestro repositorio en la rama en la que estamos:


    $ git add README.md

    $ git commit -m "Añadido README.md"
    [main c3e65d0] Añadido README.md
     1 file changed, 3 insertions(+)
     create mode 100644 README.md

    $ git hist --all
    * c3e65d0 2013-06-16 | Añadido README.md (HEAD, main) [Sergio Gómez]
    | * 9862f33 2013-06-16 | hola usa la clase HolaMundo (hola) [Sergio Gómez]
    | * 6932156 2013-06-16 | Añadida la clase HolaMundo [Sergio Gómez]
    |/
    * 81c6e93 2013-06-16 | Movido hola.py a lib [Sergio Gómez]
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]

Y vemos como `git hist` muestra la bifurcación en nuestro código.

!!! example "Grafo del repositorio"
    ```mermaid
    gitGraph:
       commit id: "e19f2c1"
       commit id: "efc252e"
       commit id: "3283e0d" tag: "v1-beta"
       commit id: "fd4da94" tag: "v1"
       commit id: "96a39df"
       commit id: "8c2a509"
       branch hola
       commit id: "6932156"
       commit id: "9862f33"
       checkout main
       commit id: "c3e65d0"
    ```

## Fusión de ramas y resolución de conflictos

### Mezclar ramas

Nuestras dos ramas tienen commits distintos, pero las dos parten de un nodo común. El mergeado de ramas
consiste en incoporar los cambios que una rama no posee a otra.

Vamos a hacer una prueba. Nos vamos a ir a la rama _hola_ y nos traeremos el commit que le falta, en el que hemos añadido el `README.md`:


    $ git switch hola
    Switched to branch 'hola'

    $ git merge --no-edit main
    Merge made by the 'ort' strategy.
     README.md | 3 +++
     1 file changed, 3 insertions(+)
     create mode 100644 README.md

    $ git hist --all
    *   9c6ac06 2013-06-16 | Merge commit 'c3e65d0' into hola (HEAD, hola) [Sergio Gómez]
    |\
    * | 9862f33 2013-06-16 | hola usa la clase HolaMundo [Sergio Gómez]
    * | 6932156 2013-06-16 | Añadida la clase HolaMundo [Sergio Gómez]
    | |
    | * c3e65d0 2013-06-16 | Añadido README.md [Sergio Gómez]
    |/
    * 81c6e93 2013-06-16 | Movido hola.py a lib [Sergio Gómez]
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]

El parámetro `--no-edit` de merge lo usamos para evitar que nos pida un mensaje para el commit y use el mensaje
estándar para los merges. Si no lo pones, te abrirá el editor que tengas configurado (vi, nano, etc) para que lo escribas.

!!! example "Grafo del repositorio"
    ```mermaid
    gitGraph:
       commit id: "e19f2c1"
       commit id: "efc252e"
       commit id: "3283e0d" tag: "v1-beta"
       commit id: "fd4da94" tag: "v1"
       commit id: "96a39df"
       commit id: "8c2a509"
       branch hola
       commit id: "6932156"
       commit id: "9862f33"
       checkout main
       commit id: "c3e65d0"
       checkout hola
       merge main id: "9c6ac06"
    ```

Ahora puedes comprobar que el fichero `README.md` también está en la rama _hola_. Pero al revés no, los cambios de _hola_ no 
están en _main_.


### Resolver conflictos

Un conflicto es cuando se produce una fusión que Git no es capaz de resolver. Vamos a modificar la rama main para crear uno con la rama hola.

    $ git switch main
    Switched to branch 'main'

Modificamos nuestro archivo _hola.py_ de nuevo:

```python
# Autor: Sergio Gómez <sergio@uco.es>
print("Introduce tu nombre:")
nombre = input().strip()
print(f"Hola, {nombre}")
```

Y guardamos los cambios:

    $ git add lib/hola.py

    $ git commit -m "Programa interactivo"
    [main 9c85275] Programa interactivo
     1 file changed, 2 insertions(+), 2 deletions(-)

    $ git hist --all
    *   9c6ac06 2013-06-16 | Merge commit 'c3e65d0' into hola (hola) [Sergio Gómez]
    |\
    * | 9862f33 2013-06-16 | hola usa la clase HolaMundo [Sergio Gómez]
    * | 6932156 2013-06-16 | Añadida la clase HolaMundo [Sergio Gómez]
    | | * 9c85275 2013-06-16 | Programa interactivo (HEAD, main) [Sergio Gómez]
    | |/
    | * c3e65d0 2013-06-16 | Añadido README.md [Sergio Gómez]
    |/
    * 81c6e93 2013-06-16 | Movido hola.py a lib [Sergio Gómez]
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]

!!! example "Grafo del repositorio"
    ```mermaid
    gitGraph:
       commit id: "e19f2c1"
       commit id: "efc252e"
       commit id: "3283e0d" tag: "v1-beta"
       commit id: "fd4da94" tag: "v1"
       commit id: "96a39df"
       commit id: "8c2a509"
       branch hola
       commit id: "6932156"
       commit id: "9862f33"
       checkout main
       commit id: "c3e65d0"
       checkout hola
       merge main id: "9c6ac06"
       checkout main
       commit id: "9c85275"
    ```

Volvemos a la rama hola y fusionamos:

    $ git switch -
    Switched to branch 'hola'
    $ git merge --no-edit main
    Auto-merging lib/hola.py
    CONFLICT (content): Merge conflict in lib/hola.py
    Automatic merge failed; fix conflicts and then commit the result.

!!! tip

    Si estamos continuamente cambiando entre dos ramas podemos usar `-` como nombre de rama. Eso le indica a git
    que nos devuelve a la última rama que hemos estado.

    ```
    git switch -
    ```

Si editamos nuestro archivo `lib/hola.py` obtendremos algo similar a esto:

```python
# Autor: Sergio Gómez <sergio@uco.es>
<<<<<<< HEAD
# El nombre por defecto es Mundo
from HolaMundo import HolaMundo

import sys
nombre = sys.argv[1] if len(sys.argv) > 1 else "Mundo"
print(HolaMundo(nombre))
=======
print("Introduce tu nombre:")
nombre = input().strip()
print(f"Hola, {nombre}")
>>>>>>> main
```

La primera parte marca el código que estaba en la rama donde trabajábamos (HEAD) y la parte final el código de donde fusionábamos. Resolvemos el conflicto, dejando el archivo como sigue:

```python
# Autor: Sergio Gómez <sergio@uco.es>
from HolaMundo import HolaMundo

print("Introduce tu nombre:")
nombre = input().strip()
print(HolaMundo(nombre))
```

Y resolvemos el conflicto confirmando los cambios:

    $ git add lib/hola.py
    $ git commit -m "Solucionado el conflicto al fusionar con la rama main"
    [hola a36af04] Solucionado el conflicto al fusionar con la rama main

!!! example "Grafo del repositorio"
    ```mermaid
    gitGraph:
       commit id: "e19f2c1"
       commit id: "efc252e"
       commit id: "3283e0d" tag: "v1-beta"
       commit id: "fd4da94" tag: "v1"
       commit id: "96a39df"
       commit id: "8c2a509"
       branch hola
       commit id: "6932156"
       commit id: "9862f33"
       checkout main
       commit id: "c3e65d0"
       checkout hola
       merge main id: "9c6ac06"
       checkout main
       commit id: "9c85275"
       checkout hola
       merge main id: "a36af04"
    ```

Por tanto, para resolver un conflicto de cambios, obligatoriamente tenemos que hacer un nuevo commit para integrar los cambios entre ambas ramas. 

### Rebasing vs Merging

Rebasing es otra técnica para fusionar distinta a merge y usa la orden `git rebase`. Vamos a dejar nuestro proyecto como estaba antes del fusionado. Para ello necesitamos anotar el hash anterior al de la acción de _merge_. El que tiene la anotación _"hola usa la clase HolaMundo"_.

Para ello podemos usar la orden `git reset` que nos permite mover HEAD donde queramos.

```shell hl_lines="12"
$ git switch hola
Switched to branch 'hola'

$ git hist
*   a36af04 2013-06-16 | Solucionado el conflicto al fusionar con la rama main (HEAD, hola) [Sergio Gómez]
|\
| * 9c85275 2013-06-16 | Programa interactivo (main) [Sergio Gómez]
* |   9c6ac06 2013-06-16 | Merge commit 'c3e65d0' into hola [Sergio Gómez]
|\ \
| |/
| * c3e65d0 2013-06-16 | Añadido README.md [Sergio Gómez]
* | 9862f33 2013-06-16 | hola usa la clase HolaMundo [Sergio Gómez]
* | 6932156 2013-06-16 | Añadida la clase HolaMundo [Sergio Gómez]
|/
* 81c6e93 2013-06-16 | Movido hola.py a lib [Sergio Gómez]
* 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
* fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
* 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
* efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
* e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]

$ git reset --hard 9862f33
HEAD is now at 9862f33 hola usa la clase HolaMundo
```

Y nuestro estado será:

    $ git hist --all
    * 9862f33 2013-06-16 | hola usa la clase HolaMundo (HEAD, hola) [Sergio Gómez]
    * 6932156 2013-06-16 | Añadida la clase HolaMundo [Sergio Gómez]
    | * 9c85275 2013-06-16 | Programa interactivo (main) [Sergio Gómez]
    | * c3e65d0 2013-06-16 | Añadido README.md [Sergio Gómez]
    |/
    * 81c6e93 2013-06-16 | Movido hola.py a lib [Sergio Gómez]
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]

!!! example "Grafo del repositorio"
    ```mermaid
    gitGraph:
       commit id: "e19f2c1"
       commit id: "efc252e"
       commit id: "3283e0d" tag: "v1-beta"
       commit id: "fd4da94" tag: "v1"
       commit id: "96a39df"
       commit id: "8c2a509"
       branch hola
       commit id: "6932156"
       commit id: "9862f33"
       checkout main
       commit id: "c3e65d0"
       commit id: "9c85275"
    ```

Hemos desecho todos los _merge_ y nuestro árbol está _"limpio"_. Vamos a probar ahora a hacer un rebase. Continuamos en la rama `hola` y ejecutamos lo siguiente:

    $ git rebase main
    Auto-merging lib/hola.py
    CONFLICT (content): Merge conflict in lib/hola.py
    error: could not apply 9862f33... hola usa la clase HolaMundo
    hint: Resolve all conflicts manually, mark them as resolved with
    hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
    hint: You can instead skip this commit: run "git rebase --skip".
    hint: To abort and get back to the state before "git rebase", run "git rebase --abort".
    hint: Disable this message with "git config set advice.mergeConflict false"
    Could not apply 9862f33... # hola usa la clase HolaMundo

El conflicto, por supuesto, se sigue dando. Resolvemos guardando el archivo `hola.py` como en los casos anteriores:

```python
# Autor: Sergio Gómez <sergio@uco.es>
from HolaMundo import HolaMundo

print("Introduce tu nombre:")
nombre = input().strip()
print(HolaMundo(nombre))
```

Añadimos los cambios en _staging_ y en esta ocasión, y tal como nos indicaba en el mensaje anterior, no tenemos que hacer `git commit` sino continuar con el _rebase_:

    $ git add lib/hola.py

    $ git status
    interactive rebase in progress; onto 269eaca
    Last commands done (2 commands done):
    pick 6932156 # Añadida la clase HolaMundo
    pick 9862f33 # hola usa la clase HolaMundo
    No commands remaining.
    You are currently rebasing branch 'hola' on '269eaca'.
    (all conflicts fixed: run "git rebase --continue")

    Changes to be committed:
    (use "git restore --staged <file>..." to unstage)
            modified:   lib/hola.py

    $ git rebase --no-edit --continue
    Applying: hola usa la clase HolaMundo

!!! note

    Cuando ejecutemos `git rebase --continue` se abrirá el editor para que 
    editemos el mensaje del commit que estamos modificando. Podemos dejarlo
    como está o aprovechara para actualizarlo.

Y ahora vemos que nuestro árbol tiene un aspecto distinto, mucho más limpio:

    $ git hist --all
    * 9862f33 2013-06-16 | hola usa la clase HolaMundo (HEAD -> hola) [Sergio Gómez]
    * 6932156 2013-06-16 | Añadida la clase HolaMundo [Sergio Gómez]
    * 9c85275 2013-06-16 | Programa interactivo (main) [Sergio Gómez]
    * c3e65d0 2013-06-16 | Añadido README.md [Sergio Gómez]
    * 81c6e93 2013-06-16 | Movido hola.py a lib [Sergio Gómez]
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]

!!! example "Grafo del repositorio"
    ```mermaid
    gitGraph:
       commit id: "e19f2c1"
       commit id: "efc252e"
       commit id: "3283e0d" tag: "v1-beta"
       commit id: "fd4da94" tag: "v1"
       commit id: "96a39df"
       commit id: "8c2a509"
       commit id: "c3e65d0"
       commit id: "9c85275"
       branch hola
       commit id: "6932156"
       commit id: "9862f33"
    ```

¿Por qué se ve distinto? Porque la fusión lo que hace es crear un commit nuevo para reconciliar los cambios. Pero rebase lo que hace es deshacer en orden inverso cada commit de la rama en la que estamos (_hola_) y los guarda en una pila (LIFO), hasta que llegemos al commit que es la unión de las dos ramas.

Ahora que estamos en el punto en común, git mueve el índice de _hola_ al final de _main_ e intenta aplicar cada commit que guardó por orden. Cada vez que no pueda porque haya conflictos nos dirá que lo arreglemos.

Es como si siempre hubieramos hecho los cambios en el código después del `README.md`.

Eso significa que se modifica el orden o la historia de creación de los cambios. Por eso rebase no debe usarse si el orden es importante o si la rama es compartida con otras personas (al menos que les avises).

## Mezclando con la rama main

Ya hemos terminado de implementar los cambios en nuestra rama secundaria y es hora de llevar los cambios a la rama principal. Usamos `git merge` para hacer una fusión normal:

    $ git switch main
    Switched to branch 'main'

    $ git merge hola
    Updating c3e65d0..491f1d2
    Fast-forward
     lib/hola_mundo.py | 16 ++++++++++++++++
     lib/hola.py      |  4 +++-
     2 files changed, 19 insertions(+), 1 deletion(-)
     create mode 100644 lib/hola_mundo.py

     $ git hist --all
     * 9862f33 2013-06-16 | hola usa la clase HolaMundo (HEAD -> main, hola) [Sergio Gómez]
     * 6932156 2013-06-16 | Añadida la clase HolaMundo [Sergio Gómez]
     * 9c85275 2013-06-16 | Programa interactivo [Sergio Gómez]
     * c3e65d0 2013-06-16 | Añadido README.md [Sergio Gómez]
     * 81c6e93 2013-06-16 | Movido hola.py a lib [Sergio Gómez]
     * 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
     * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
     * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
     * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
     * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]


Vemos que indica que el tipo de fusión es _fast-forward_. Este tipo de fusión tiene el problema que no deja rastro de la fusión, por eso suele ser recomendable usar el parámetro `--no-ff` para que quede constancia siempre de que se ha fusionado una rama con otra.

Vamos a volver a probar ahora sin hacer _fast-forward_. Reseteamos _main_ al estado _"Programa interactivo"_. Busca el hash de ese commit en tu historia y úsalo.

    $ git reset --hard 9c85275
    $ git hist --all
    * 9862f33 2013-06-16 | hola usa la clase HolaMundo (HEAD -> hola) [Sergio Gómez]
    * 6932156 2013-06-16 | Añadida la clase HolaMundo [Sergio Gómez]
    * 9c85275 2013-06-16 | Programa interactivo (main) [Sergio Gómez]
    * c3e65d0 2013-06-16 | Añadido README.md [Sergio Gómez]
    * 81c6e93 2013-06-16 | Movido hola.py a lib [Sergio Gómez]
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]

Vemos que estamos como en el final de la sección anterior, así que ahora mezclamos:

    $ git merge --no-edit --no-ff hola
    Merge made by the 'ort' strategy.
    lib/HolaMundo.py | 6 ++++++
    lib/hola.py      | 4 +++-
    2 files changed, 9 insertions(+), 1 deletion(-)
    create mode 100644 lib/HolaMundo.py

    $ git hist --all
    *   2eab8ca 2013-06-16 | Merge branch 'hola' (HEAD -> main) [Sergio Gomez]
    *\
    | * 9862f33 2013-06-16 | hola usa la clase HolaMundo (hola) [Sergio Gómez]
    | * 6932156 2013-06-16 | Añadida la clase HolaMundo [Sergio Gómez]
    |/
    * 9c85275 2013-06-16 | Programa interactivo (main) [Sergio Gómez]
    * c3e65d0 2013-06-16 | Añadido README.md [Sergio Gómez]
    * 81c6e93 2013-06-16 | Movido hola.py a lib [Sergio Gómez]
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]

!!! example "Grafo del repositorio"
    ```mermaid
    gitGraph:
       commit id: "e19f2c1"
       commit id: "efc252e"
       commit id: "3283e0d" tag: "v1-beta"
       commit id: "fd4da94" tag: "v1"
       commit id: "96a39df"
       commit id: "8c2a509"
       commit id: "c3e65d0"
       commit id: "9c85275"
       branch hola
       commit id: "6932156"
       commit id: "9862f33"
       checkout main
       merge hola id: "2eab8ca"
    ```


En la siguiente imagen se puede ver la diferencia:

![Diferencias entre tipos de fusión](images/gitlab-merge-ff.png)
