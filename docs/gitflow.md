# Flujo de trabajo con Git (git flow)

## La importancia de la organización del flujo de trabajo

En la introducción vimos los diferentes esquemas de organización externa de los repositorios (es decir, en lo relativo a los usuarios que componen el equipo de trabajo). 

Pero el repositorio en sí también tiene su esquema de organización.

En los ejemplos hemos visto que usabamos una rama máster y creábamos ramas para añadir funcionalidades que luego integrábamos. Es un forma de trabajar de las muchas que hay propuestas, posiblemente la más simple, pero tiene el inconveniente de dejar la rama máster a expensas de una mala actualización y quedarnos sin una rama estable. Por eso, hay otras propuestas mejores que permiten separar el trabajo de desarrollo con el mantenimiento de las versiones estables. Una de las más conocidas es la propuesta por [Vincent Driessen](http://nvie.com/posts/a-successful-git-branching-model/) y que podemos ver en la figura siguiente.

![Git flow](images/gitlab-organizacion.png)

### Las ramas principales

En este esquema hay dos ramas principales con un tiempo de vida indefinido:

- master (_origin/master_): el código apuntado por _HEAD_ siempre contiene un estado listo para producción.
- develop (_origin/develop_): el código apuntado por _HEAD_ siempre contiene los últimos cambios desarrollados para la próxima versión del software. También se le puede llamar _rama de integración_. No es necesariamente estable.

Cuando el código de la rama de desarrollo es lo suficientemente estable, se integra con la rama master y una nueva versión es lanzada.

### Las ramas auxiliares

Para labores concretas, pueden usarse otro tipo de ramas, las cuales tienen un tiempo de vida definido. Es decir, cuando ya no son necesarias se eliminan:

- Ramas de funcionalidad (feature branches)
- Ramas de versión (release branches)
- Ramas de parches (hotfix branches)

#### Feature branches

- Pueden partir de: develop
- Deben fusionarse con: develop
- Convenición de nombres: feature-NUMissue-\*.

#### Release branches

- Pueden partir de: develop
- Deben fusionarse con: develop y master
- Convenición de nombres: release-\*

#### Hotfix branches

- Pueden partir de: master
- Deben fusionarse con: develop y master
- Convenición de nombres: hotfix-\*

## La extensión flow de Git

Una de las ventajas de Git es que, además, es extensible. Es decir, se pueden crear nuevas órdenes como si de plugins se tratara. Una de las más usadas es [gitflow](https://github.com/nvie/gitflow), que está basada en el artículo que hablamos al principio de este capítulo.

### Instalación

Aunque la fuente original de la extensión es del mismo autor del artículo, el código no se encuentra ya muy actualizado y hay un fork bastante más activo en [petervanderdoes/gitflow](https://github.com/petervanderdoes/gitflow). En el wiki del repositorio están las [instrucciones de instalación](https://github.com/petervanderdoes/gitflow/wiki) para distintos sistemas. Una vez instalados tendremos una nueva órden: `git flow`.

### Uso

Para cambiar a las ramas master y develop, seguiremos usando `git checkout`, pero para trabajar con las ramas antes indicadas gitflow nos facilita las siguientes órdenes:


#### - git flow init: 
Inicializa el espacio de trabajo. De forma automática, crea las ramas que necesitamos y permite configurar el nombre de las mismas.

```
$ git flow init
Initialized empty Git repository in ~/project/.git/
No branches exist yet. Base branches must be created now.
Branch name for production releases: [master]
Branch name for "next release" development: [develop]

How to name your supporting branch prefixes?
Feature branches? [feature/]
Release branches? [release/]
Hotfix branches? [hotfix/]
Support branches? [support/]
Version tag prefix? []

$ git branch
* develop
 master
```

Podemos ver que por defecto (usando intro en vez de escribir nada) pone nombres por defecto a cada rama. Con `git branch` comprobamos que ramas existen y en cual nos encontramos.




#### - git flow feature: 
Permite crear y trabajar con ramas de funcionalidades.

```
$ git flow feature start feature_branch

```

Así creamos una rama 'feature/feature_branch' y nos mueve automáticamente a ella.
En esta haremos los cambios que queramos en nuestro repositorio. Cuando queramos acabar de usar la rama, haremos un commit y la finalizaremos:

```
$ git flow feature stop feature_branch

```

Esto finaliza nuestra rama y la integra automáticamente a la rama develop. Si queremos seguir cambiando nuestro repositorio abriremos una nueva rama feature.




#### - git flow release: 
Permite crear y trabajar con ramas de versiones.
Cuando entendemos que despues de todas las funcionalidades (features, cambios en nuestro repositorio) nuestro trabajo esta listo para ser publicado, abriremos una rama release, que nacera de nuestra rama develop. 

```
$ git flow release start 0.1.0
Switched to a new branch 'release/0.1.0'
```

Usaremos un tag para identificar de que release se trata.
Ahora podemos hacer los cambios que estimemos oportuno para integrar todas las features que el repositorio ha sufrido hasta el momento. Tras hacer commit a todo el proceso, podemos cerrar la rama release.

```
$git flow release finish '0.1.0'
```

Esto la integrará de forma automática con master (con esto finalizamos el proceso de 'subir a producción' nuestro codigo) y con la rama develop, para que las futuras features estén al día.




#### - git flow hotfix: 
Permite crear y trabajar con ramas de parches. Esto lo usaremos para hacer cambios rapidos que no puedan esperar a la proxima integracion de una release.

```
$ git flow hotfix start hotfix_branch
```

Tras hacer commit finalizamos la rama hotfix. Esta se fusionará con nuestra rama master y con nuestra rama develop para que esta también esté al día de los últimos cambios.

```
$ git flow hotfix finish hotfix_branch
```
