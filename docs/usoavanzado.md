# Uso avanzado de Git

## Deshacer cambios

### Deshaciendo cambios antes de la fase de staging.

Volvemos a la rama máster y vamos a modificar el comentario que pusimos:

    $ git switch main
    Previous HEAD position was 3283e0d... Se añade un parámetro por defecto
    Switched to branch 'main'

Modificamos _hola.py_ de la siguiente manera:

```python
import sys
// Este comentario está mal y hay que borrarlo
nombre = sys.argv[1] if len(sys.argv) > 1 else "Mundo"
print(f"Hola, {nombre}")
```

Y comprobamos:

    $ git status
    On branch main
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)

      modified:   hola.py

    no changes added to commit (use "git add" and/or "git commit -a")

El mismo Git nos indica que debemos hacer para añadir los cambios o para deshacerlos:

    $ git restore hola.py
    $ git status
    On branch main
    nothing to commit, working directory clean

Y comprobamos que el archivo ha vuelto a su estado original.

    $ cat hola.py

    import sys
    # El nombre por defecto es Mundo
    nombre = sys.argv[1] if len(sys.argv) > 1 else "Mundo"
    print(f"Hola, {nombre}")

### Deshaciendo cambios antes del commit

Vamos a hacer lo mismo que la vez anterior, pero esta vez sí añadiremos el cambio al _staging_ (sin hacer _commit_).
Así que volvemos a modificar _hola.py_ igual que la anterior ocasión:


```python
import sys
// Este comentario está mal y hay que borrarlo
nombre = sys.argv[1] if len(sys.argv) > 1 else "Mundo"
print(f"Hola, {nombre}")
```

Y lo añadimos al _staging_:

    $ git add hola.py
    $ git status
    On branch main
    Changes to be committed:
      (use "git restore --staged <file>..." to unstage)

      modified:   hola.py


De nuevo, Git nos indica qué debemos hacer para deshacer el cambio:

    $ git restore --staged hola.py

    $ git status
    On branch main
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
    
      modified:   hola.py
    
    no changes added to commit (use "git add" and/or "git commit -a")

    $ git restore hola.py

En este caso, hemos tenido que hacer dos recuperaciones distintas. Uno para deshacer la confirmación
(`git restore --staged`) y otro para deshacer el cambio en el espacio de trabajo (`git restore`). Dependiendo
de lo que queramos hacer usaremos una opción u otra.

!!! tip

    Si quieres recuperar completamente un archivo en un solo paso puedes ejecutar lo siguiente:

        $ git restore --staged --worktree hola.py



### Deshaciendo commits no deseados

Si a pesar de todo hemos hecho un commit y nos hemos equivocado, podemos deshacerlo con la orden `git revert`.
Modificamos otra vez el archivo como antes:

```python
import sys
// Este comentario está mal y hay que borrarlo
nombre = sys.argv[1] if len(sys.argv) > 1 else "Mundo"
print(f"Hola, {nombre}")
```

Pero ahora sí hacemos commit:

    $ git add hola.py
    $ git commit -m "Ups... este commit está mal."
    [main 5a5d067] Ups... este commit está mal
     1 file changed, 2 insertions(+), 3 deletions(-)

Bien, una vez confirmado el cambio, vamos a deshacer el cambio con la orden `git revert`:

    $ git revert HEAD --no-edit
    [main 817407b] Revert "Ups... este commit está mal"
     1 file changed, 1 insertion(+), 1 deletion(-)
    $ git hist
    * 817407b 2013-06-16 | Revert "Ups... este commit está mal" (HEAD, main) [Sergio Gómez]
    * 5a5d067 2013-06-16 | Ups... este commit está mal [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]

!!! example  "Grafo del repositorio"
    ```mermaid
    gitGraph:
        commit id: "e19f2c1"
        commit id: "efc252e"
        commit id: "3283e0d" tag: "v1-beta"
        commit id: "fd4da94" tag: "v1"
        commit id: "5a5d067"
        commit id: "817407b"
    ```

### Borrar commits de una rama

El anterior apartado revierte un commit, pero deja huella en el historial de cambios. Para hacer que no aparezca hay que usar la orden `git reset`:

    $ git reset --hard v1
    HEAD is now at fd4da94 Se añade un comentario al cambio del valor por defecto
    $ git hist
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (HEAD, tag: v1, main) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]

!!! example  "Grafo del repositorio"
    ```mermaid
    gitGraph:
        commit id: "e19f2c1"
        commit id: "efc252e"
        commit id: "3283e0d" tag: "v1-beta"
        commit id: "fd4da94" tag: "v1"
        branch detached
        commit id: "5a5d067"
        commit id: "817407b"

    ```

El resto de cambios no se han borrado (aún), simplemente no están accesibles porque git no sabe como referenciarlos. Si sabemos su hash podemos acceder aún a ellos. Pasado un tiempo, eventualmente Git tiene un recolector de basura que los borrará.

Lo que hace `git reset` es modificar HEAD (recuerda, la posición de nuestro espacio de trabajo) al commit que le indiquemos. El parámetro `--hard` además le dice que mueva también el índice de la rama, es decir, lo que entiende git que es el último commit y desde donde debe continuar añadiendo.

!!! danger

    La orden _reset_ es una operación delicada. Debe evitarse si no se sabe bien lo que se está haciendo,
    sobre todo cuando se trabaja en repositorios compartidos, porque podríamos alterar la historia de cambios
    lo cual puede provocar problemas de sincronización.

### Modificar un commit

Esto se usa cuando hemos olvidado añadir un cambio a un commit que acabamos de realizar. Vamos a modificar
nuestro archivo _hola.py_ de la siguiente manera:

```python
import sys

# Autor: Sergio Gómez
# El nombre por defecto es Mundo
nombre = sys.argv[1] if len(sys.argv) > 1 else "Mundo"
print(f"Hola, {nombre}")
```

Y lo confirmamos:

    $ git commit -a -m "Añadido el autor del programa"
    [main cf405c1] Añadido el autor del programa
     1 file changed, 1 insertion(+)

!!! example  "Grafo del repositorio"

    ```mermaid
    gitGraph:
        commit id: "e19f2c1"
        commit id: "efc252e"
        commit id: "3283e0d" tag: "v1-beta"
        commit id: "fd4da94" tag: "v1"
        commit id: "cf405c1"
    ```

!!! tip

    El parámetro `-a` hace un `git add` antes de hacer _commit_ de todos los archivos modificados
     o borrados (de los nuevos no), con lo que nos ahorramos un paso.

Ahora nos percatamos que se nos ha olvidado poner el correo electrónico. Así que volvemos a modificar nuestro archivo:

```python
import sys

# Autor: Sergio Gómez <sergio@uco.es>
# El nombre por defecto es Mundo
nombre = sys.argv[1] if len(sys.argv) > 1 else "Mundo"
print(f"Hola, {nombre}")
```

Y en esta ocasión usamos `commit --amend` que nos permite modificar el último estado confirmado, sustituyéndolo por el estado actual:

    $ git add hola.py
    $ git commit --amend -m "Añadido el autor del programa y su email"
    [main 96a39df] Añadido el autor del programa y su email
     1 file changed, 1 insertion(+)

    $ git hist
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email (HEAD, main) [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]

!!! example  "Grafo del repositorio"
    ```mermaid
    gitGraph:
        commit id: "e19f2c1"
        commit id: "efc252e"
        commit id: "3283e0d" tag: "v1-beta"
        commit id: "fd4da94" tag: "v1"
        commit id: "96a39df"
    ```

!!! danger

    Nunca modifiques un _commit_ que ya hayas sincronizado con otro repositorio o
    que hayas recibido de él. Estarías alterando la historia de cambios y provocarías
    problemas de sincronización. Solo debes hacerlo en local y mientras no hayas compartido
    aún esos cambios con nadie. Lo veremos en el capítulo de Github.


## Moviendo y borrando archivos

### Mover un archivo a otro directorio con git

Para mover archivos usaremos la orden `git mv`:

    $ mkdir lib
    $ git mv hola.py lib
    $ git status
    On branch main
    Changes to be committed:
      (use "git restore --staged <file>..." to unstage)
    
      renamed:    hola.py -> lib/hola.py
    
!!! info Mover y borrar archivos

    Podíamos haber hecho el paso anterior con la órden del sistema _mv_ y el resultado hubiera sido el mismo. Lo siguiente es a modo de ejemplo y no es necesario que lo ejecutes:

        $ mkdir lib
        $ mv hola.py lib
        $ git add lib/hola.py
        $ git rm hola.py

Y, ahora sí, ya podemos guardar los cambios:

    $ git commit -m "Movido hola.py a lib."
    [main 8c2a509] Movido hola.py a lib.
     1 file changed, 0 insertions(+), 0 deletions(-)
     rename hola.py => lib/hola.py (100%)

!!! example  "Grafo del repositorio"
    ```mermaid
    gitGraph:
        commit id: "e19f2c1"
        commit id: "efc252e"
        commit id: "3283e0d" tag: "v1-beta"
        commit id: "fd4da94" tag: "v1"
        commit id: "96a39df"
        commit id: "8c2a509"
    ```