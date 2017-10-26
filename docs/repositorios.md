# Administración de repositorios

## Trabajando con otros repositorios

### Clonar un repositorio

Clonar es la acción de copiar el contenido de un repositorio para generar otra copia de trabajo. Se usa la orden `git clone`. Vamos a clonar nuestro respositorio de ejemplo en otro directorio:

    $ cd ..
    $ git clone curso-git curso-git-clonado
    Cloning into 'curso-git-clonado'...
    done.

Y comprobamos que el nuevo repositorio es igual que el que teníamos:

    $ cd curso-git-clonado/
    $ ls
    README.md  lib
    $ git hist --all
    * 491f1d2 2013-06-16 | hola usa la clase HolaMundo (HEAD, origin/master, origin/hola, origin/HEAD, master) [Sergio Gómez]
    * c13d75c 2013-06-16 | Añadida la clase HolaMundo [Sergio Gómez]
    * c3e65d0 2013-06-16 | Añadido README.md [Sergio Gómez]
    * 81c6e93 2013-06-16 | Movido hola.php a lib [Sergio Gómez]
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]

Bueno, no exactamente igual. Hay unas nuevas ramas llamadas _origin/*_ que indican que están vinculadas con un repositorio externo. Podemos verlas con la orden `git remote`:

    $ git remote show origin
    * remote origin
      Fetch URL: /home/cc0gobas/git/curso-git
      Push  URL: /home/cc0gobas/git/curso-git
      HEAD branch (remote HEAD is ambiguous, may be one of the following):
        hola
        master
      Remote branches:
        hola   tracked
        master tracked
      Local branch configured for 'git pull':
        master merges with remote master
      Local ref configured for 'git push':
        master pushes to master (up to date)

### Ramas remotas

Para poder ver las ramas remotas debemos usar el parámetro _-a_ en la orden `git branch`:

    $ git branch -a
    * master
      remotes/origin/HEAD -> origin/master
      remotes/origin/hola
      remotes/origin/master

### Cómo actualizar el repositorio copia

Cuando se trabaja en grupo, es normal que el repositorio remoto cambie. Vamos a modificarlo y ver como podemos actualizar el repositorio copia:

    $ cd ..
    $ cd curso-git
    $ cat README.md
    # Curso de GIT

    Este proyecto contiene el curso de introducción a GIT.
    Ahora es un repositorio compartido.
    $ git add README.md
    $ git commit -m "Cambiado el README.md"
    [master 3d2db03] Cambiado el README.md
     1 file changed, 2 insertions(+), 1 deletion(-)

Al proceso de traer una actualización se le llama _pull_ y de hecho hay una orden llamada `git pull` que realiza ese cometido. Vamos a ver, de todas maneras, el camino largo usando `git fetch`. Esta última órden lo que hace es actualizar la rama _origin/master_ y _origin/HEAD_ de nuestro repositorio copia, pero no actualiza las ramas locales. Es decir, no fusiona las ramas remotas con las locales:

    $ cd ../curso-git-clonado/
    $ git fetch
    remote: Counting objects: 5, done.
    remote: Compressing objects: 100% (3/3), done.
    remote: Total 3 (delta 0), reused 0 (delta 0)
    Unpacking objects: 100% (3/3), done.
    De /home/cc0gobas/git/curso-git
       491f1d2..3d2db03  master     -> origin/master
    $ git hist --all
    * 3d2db03 2013-06-17 | Cambiado el README.md (origin/master, origin/HEAD) [Sergio Gómez]
    * 491f1d2 2013-06-16 | hola usa la clase HolaMundo (HEAD, origin/hola, master) [Sergio Gómez]
    * c13d75c 2013-06-16 | Añadida la clase HolaMundo [Sergio Gómez]
    * c3e65d0 2013-06-16 | Añadido README.md [Sergio Gómez]
    * 81c6e93 2013-06-16 | Movido hola.php a lib [Sergio Gómez]
    * 96a39df 2013-06-16 | Añadido el autor del programa y su email [Sergio Gómez]
    * fd4da94 2013-06-16 | Se añade un comentario al cambio del valor por defecto (v1) [Sergio Gómez]
    * 3283e0d 2013-06-16 | Se añade un parámetro por defecto (v1-beta) [Sergio Gómez]
    * efc252e 2013-06-16 | Parametrización del programa [Sergio Gómez]
    * e19f2c1 2013-06-16 | Creación del proyecto [Sergio Gómez]

Podemos comprobar que el archivore `README.md` no ha cambiado. Para hacer los cambios tendremos que fusionar las ramas como hemos visto:

    $ git merge origin/master
    Updating 491f1d2..3d2db03
    Fast-forward
     README.md | 3 ++-
     1 file changed, 2 insertions(+), 1 deletion(-)

Los dos pasos anteriores se suelen reducir en uno solo:

    $ git pull
    Updating 491f1d2..3d2db03
    Fast-forward
     README.md | 3 ++-
     1 file changed, 2 insertions(+), 1 deletion(-)

¿Por qué existe entonces `git fetch`? Pues porque normalmente los integradores prefieren ver los cambios que se van a realizar antes de hacerlos. Se puede hacer un _checkout_ a la rama remota para ver lo que ha cambiado.

### Añadir una rama de seguimiento

Cuando se clona un repositorio, en el repositorio local solo se crea una rama máster, pero las ramas secundarias que ya existían en el repositorio remoto no se crean. Solo aparecen a través de las ramas remotas _origin/*_. Para crear ramas locales que hagan seguimiento de esas ramas remotas usaremos `git branch`:

    $ git branch --track hola origin/hola
    Branch hola set up to track remote branch hola from origin.
    $ git branch -a
      hola
    * master
      remotes/origin/HEAD -> origin/master
      remotes/origin/hola
      remotes/origin/master
    $ git hist --max-count=2
    * 3d2db03 2013-06-17 | Cambiado el README.md (HEAD, origin/master, origin/HEAD, master) [Sergio Gómez]
    * 491f1d2 2013-06-16 | hola usa la clase HolaMundo (origin/hola, hola) [Sergio Gómez]

### Borrar ramas remotas

Para borrar una rama local se usaba `git branch`:

    $ git branch -d nombre-rama

Pero para borrar una rama que esté en el repositorio remoto hay que usar `git push` colocando el símbolo `:` antes del nombre de la rama:

    $ git push origin :nombre-rama

## Repositorios básicos

### Creación

Los repositorio básicos (en inglés conocidos como _bare repositories_) no contienen la parte llamada _"directorio de trabajo"_ y son usados específicamente para ser compartidos, se usa indicando el parámetro `--bare` a las órdenes `git clone` y `git init`:

    $ cd ..
    $ git clone --bare curso-git curso-git.git
    Cloning into bare repository 'curso-git.git'...
    hecho.
    $ ls curso-git.git/
    branches  config  description  HEAD  hooks  info  objects  packed-refs  refs

### Añadir un repositorio remoto

Es posible indicar, para un proyecto ya existente, que utilice un repositorio remoto. En este caso, al proyecto original vamos a indicarle que use el nuevo repositorio básico:

    $ cd curso-git
    $ git remote add origin ../curso-git.git

### Subir cambios

El escenario que tenemos ahora es el siguiente:

* Un repositorio básico llamado _curso-git.git_ donde se almacenará centralmente el proyecto.
* Un repositorio llamado _curso-git_ donde trabajaremos.

Este es el escenario normal, un directorio centralizado y compartido y uno donde trabajamos localmente. En este escenario, iremos realizando cambios en nuestro directorio y, posteriormente, los subiremos al repositorio compartido. Para eso usaremos la orden `git push`.

Vamos a modificar nuestro archivo `README.md` de nuevo y a subir los cambios:

    $ cat README.md
    # Curso de GIT

    Este proyecto contiene el curso de introducción a GIT.
    Ahora es un repositorio compartido.
    Con esto ya estamos terminando el curso.
    $ git add README.md
    $ git commit -m "Añadido un comentario compartido en README.md"
    [master 243d834] Añadido un comentario compartido en README.md
     1 file changed, 1 insertion(+)
    $ git push -u origin master
    Counting objects: 5, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (3/3), done.
    Writing objects: 100% (3/3), 372 bytes, done.
    Total 3 (delta 1), reused 0 (delta 0)
    To ../curso-git.git/
       3d2db03..243d834  master -> master
    Branch master set up to track remote branch master from origin.

### Actualizar cambios desde repositorios compartidos

El repositorio _curso-git-clonado_ era un clon del repositorio original. Git permite modificar las ramas remotas de un proyecto o añadir más, de tal manera que podríamos hacer ahora dos cosas con ese repositorio:

* Eliminar las referencias de origin/* y crear nuevas al repositorio básico `curso-git.git`.
* Añadir más ramas remotas asociadas al repositorio básico.

Esto último sería:

    $ cd ../curso-git-clonado/
    $ git remote add shared ../curso-git.git
    $ git pull -u shared master
    remote: Counting objects: 5, done.
    remote: Compressing objects: 100% (3/3), done.
    remote: Total 3 (delta 1), reused 0 (delta 0)
    Unpacking objects: 100% (3/3), done.
    De ../curso-git
     * branch            master     -> FETCH_HEAD
    Updating 3d2db03..243d834
    Fast-forward
     README.md | 1 +
     1 file changed, 1 insertion(+)
    $ cat README.md
    # Curso de GIT

    Este proyecto contiene el curso de introducción a GIT.
    Ahora es un repositorio compartido.
    Con esto ya estamos terminando el curso.

El parámetro _-u_ es equivalente a hacer esto:

    git branch --track shared master
