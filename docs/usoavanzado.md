# Uso avanzado de Git

## Deshacer cambios

### Deshaciendo cambios antes de la fase de staging.

Volvemos a la rama máster y vamos a modificar el comentario que pusimos:

    $ git checkout master
    Previous HEAD position was 3283e0d... Se añade un parámetro por defecto
    Switched to branch 'master'

Modificamos _hola.php_ de la siguiente manera:

```php
<?php
// Este comentario está mal y hay que borrarlo
$nombre = isset($argv[1]) ? $argv[1] : "Mundo";
@print "Hola, {$nombre}\n";
```

Y comprobamos:

    $ git status
    # On branch master
    # Changes not staged for commit:
    #   (use "git add <file>..." to update what will be committed)
    #   (use "git checkout -- <file>..." to discard changes in working directory)
    #
    #   modified:   hola.php
    #
    no changes added to commit (use "git add" and/or "git commit -a")

El mismo Git nos indica que debemos hacer para añadir los cambios o para deshacerlos:

    $ git checkout hola.php
    $ git status
    # On branch master
    nothing to commit, working directory clean
    $ cat hola.php
    <?php
    // El nombre por defecto es Mundo
    $nombre = isset($argv[1]) ? $argv[1] : "Mundo";
    @print "Hola, {$nombre}\n";

### Deshaciendo cambios antes del commit

Vamos a hacer lo mismo que la vez anterior, pero esta vez sí añadiremos el cambio al _staging_ (sin hacer _commit_).
Así que volvemos a modificar _hola.php_ igual que la anterior ocasión:

```php
<?php
// Este comentario está mal y hay que borrarlo
$nombre = isset($argv[1]) ? $argv[1] : "Mundo";
@print "Hola, {$nombre}\n";
```

Y lo añadimos al _staging_

    $ git add hola.php
    $ git status
    # On branch master
    # Changes to be committed:
    #   (use "git reset HEAD <file>..." to unstage)
    #
    #   modified:   hola.php
    #

De nuevo, Git nos indica qué debemos hacer para deshacer el cambio:

    $ git reset HEAD hola.php
    Unstaged changes after reset:
    M   hola.php
    $ git status
    # On branch master
    # Changes not staged for commit:
    #   (use "git add <file>..." to update what will be committed)
    #   (use "git checkout -- <file>..." to discard changes in working directory)
    #
    #   modified:   hola.php
    #
    no changes added to commit (use "git add" and/or "git commit -a")
    $ git checkout hola.php

Y ya tenemos nuestro repositorio limpio otra vez. Como vemos hay que hacerlo en dos pasos:
uno para borrar los datos del _staging_ y otro para restaurar la copia de trabajo.

### Deshaciendo commits no deseados.

Si a pesar de todo hemos hecho un commit y nos hemos equivocado, podemos deshacerlo con la orden `git revert`.
Modificamos otra vez el archivo como antes:

```php
<?php
// Este comentario está mal y hay que borrarlo
$nombre = isset($argv[1]) ? $argv[1] : "Mundo";
@print "Hola, {$nombre}\n";
```

Pero ahora sí hacemos commit:

    $ git add hola.php
    $ git commit -m "Ups... este commit está mal."
    master 5a5d067] Ups... este commit está mal
     1 file changed, 1 insertion(+), 1 deletion(-)

Bien, una vez confirmado el cambio, vamos a deshacer el cambio con la orden `git revert`:

    $ git revert HEAD --no-edit
    [master 817407b] Revert "Ups... este commit está mal"
    1 file changed, 1 insertion(+), 1 deletion(-)
    $ git hist
    * 817407b 2013-06-16 | Revert "Ups... este commit está mal" (HEAD, master) [Sergio Gómez]
    * 5a5d067 2013-06-16 | Ups... este commit está mal [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]

### Borrar commits de una rama

El anterior apartado revierte un commit, pero deja huella en el historial de cambios. Para hacer que no aparezca hay que usar la orden `git reset`.

    $ git reset --hard v1
    HEAD is now at fd4da94 Se añade un comentario al cambio del valor por defecto
    $ git hist
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (HEAD, tag: v1, master) [Sergio Góme
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]

El resto de cambios no se han borrado (aún), simplemente no están accesibles porque git no sabe como referenciarlos. Si sabemos su hash podemos acceder aún a ellos. Pasado un tiempo, eventualmente Git tiene un recolector de basura que los borrará. Se puede evitar etiquetando el estado final.

!!! danger

    La orden _reset_ es una operación delicada. Debe evitarse si no se sabe bien lo que se está haciendo,
    sobre todo cuando se trabaja en repositorios compartidos, porque podríamos alterar la historia de cambios
    lo cual puede provocar problemas de sincronización.

### Modificar un commit

Esto se usa cuando hemos olvidado añadir un cambio a un commit que acabamos de realizar. Tenemos
nuestro archivo _hola.php_ de la siguiente manera:

```php
<?php
// Autor: Sergio Gómez
// El nombre por defecto es Mundo
$nombre = isset($argv[1]) ? $argv[1] : "Mundo";
@print "Hola, {$nombre}\n";
```

Y lo confirmamos:

    $ git commit -a -m "Añadido el autor del programa"
    [master cf405c1] Añadido el autor del programa
     1 file changed, 1 insertion(+)

!!! tip

    El parámetro `-a` hace un `git add` antes de hacer _commit_ de todos los archivos modificados
     o borrados (de los nuevos no), con lo que nos ahorramos un paso.

Ahora nos percatamos que se nos ha olvidado poner el correo electrónico. Así que volvemos a modificar nuestro archivo:

```php
<?php
// Autor: Sergio Gómez <sergio@uco.es>
// El nombre por defecto es Mundo
$nombre = isset($argv[1]) ? $argv[1] : "Mundo";
@print "Hola, {$nombre}\n";
```

Y en esta ocasión usamos `commit --amend` que nos permite modificar el último estado confirmado, sustituyéndolo por el estado actual:

    $ git add hola.php
    $ git commit --amend -m "Añadido el autor del programa y su email"
    [master 96a39df] Añadido el autor del programa y su email
     1 file changed, 1 insertion(+)
    $ git hist
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email (HEAD, master) [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (tag: v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (tag: v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]

!!! danger

    Nunca modifiques un _commit_ que ya hayas sincronizado con otro repositorio o
    que hayas recibido de él. Estarías alterando la historia de cambios y provocarías
    problemas de sincronización.

## Moviendo y borrando archivos

### Mover un archivo a otro directorio con git

Para mover archivos usaremos la orden `git mv`:

    $ mkdir lib
    $ git mv hola.php lib
    $ git status
    # On branch master
    # Changes to be committed:
    #   (use "git reset HEAD <file>..." to unstage)
    #
    #   renamed:    hola.php -> lib/hola.php
    #

### Mover y borrar archivos.

Podíamos haber hecho el paso anterior con la órden del sistema _mv_ y el resultado hubiera sido el mismo. Lo siguiente es a modo de ejemplo y no es necesario que lo ejecutes:

    $ mkdir lib
    $ mv hola.php lib
    $ git add lib/hola.php
    $ git rm hola.php

Y, ahora sí, ya podemos guardar los cambios:

    $ git commit -m "Movido hola.php a lib."
    [master 8c2a509] Movido hola.php a lib.
     1 file changed, 0 insertions(+), 0 deletions(-)
     rename hola.php => lib/hola.php (100%)
