# Flujo de trabajo con Git (git flow)

## La importancia de la organización del flujo de trabajo

En la introducción vimos los diferentes esquemas de organización externa de los repositorios (es decir, en lo relativo a los usuarios que componen el equipo de trabajo). Pero el repositorio en sí también tiene su esquema de organización. En los ejemplos hemos visto que usabamos una rama máster y creábamos ramas para añadir funcionalidades que luego integrábamos. Es un forma de trabajar de las muchas que hay propuestas, posiblemente la más simple, pero tiene el inconveniente de dejar la rama máster a expensas de una mala actualización y quedarnos sin una rama estable. Por eso, hay otras propuestas mejores que permiten separar el trabajo de desarrollo con el mantenimiento de las versiones estables. Una de las más conocidas es la propuesta por [Vincent Driessen](http://nvie.com/posts/a-successful-git-branching-model/) y que podemos ver en la figura siguiente.

![Git flow](images/gitlab-organizacion.png)

### Las ramas principales

En este esquema hay dos ramas principales con un tiempo de vida indefinido:

* master (_origin/master_): el código apuntado por _HEAD_ siempre contiene un estado listo para producción.
* develop (_origin/develop_): el código apuntado por _HEAD_ siempre contiene los últimos cambios desarrollados para la próxima versión del software. También se le puede llamar _rama de integración_. No es necesariamente estable.

Cuando el código de la rama de desarrollo es lo suficientemente estable, se integra con la rama master y una nueva versión es lanzada.

### Las ramas auxiliares

Para labores concretas, pueden usarse otro tipo de ramas, las cuales tienen un tiempo de vida definido. Es decir, cuando ya no son necesarias se eliminan:

* Ramas de funcionalidad (feature branches)
* Ramas de versión (release branches)
* Ramas de parches (hotfix branches)

#### Feature branches

* Pueden partir de: develop
* Deben fusionarse con: develop
* Convenición de nombres: feature-NUMissue-*.

#### Release branches

* Pueden partir de: develop
* Deben fusionarse con: develop y master
* Convenición de nombres: release-*

#### Hotfix branches

* Pueden partir de: master
* Deben fusionarse con: develop y master
* Convenición de nombres: hotfix-\*

## La extensión flow de Git

Una de las ventajas de Git es que, además, es extensible. Es decir, se pueden crear nuevas órdenes como si de plugins se tratara. Una de las más usadas es [gitflow](https://github.com/nvie/gitflow), que está basada en el artículo que hablamos al principio de este capítulo.

### Instalación

Aunque la fuente original de la extensión es del mismo autor del artículo, el código no se encuentra ya muy actualizado y hay un fork bastante más activo en [petervanderdoes/gitflow](https://github.com/petervanderdoes/gitflow). En el wiki del repositorio están las [instrucciones de instalación](https://github.com/petervanderdoes/gitflow/wiki) para distintos sistemas. Una vez instalados tendremos una nueva órden: `git flow`.

### Uso

Para cambiar a las ramas master y develop, seguiremos usando  `git checkout`, pero para trabajar con las ramas antes indicadas gitflow nos facilita las siguientes órdenes:

* `git flow init`: Permite inicializar el espacio de trabajo.
* `git flow feature`: Permite crear y trabajar con ramas de funcionalidades.
* `git flow release`: Permite crear y trabajar con ramas de versiones.
* `git flow hotfix`: Permite crear y trabajar con ramas de parches.

En el siguiente capítulo veremos como usarlas para trabajar en un proyecto ya creado.
