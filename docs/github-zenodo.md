# Citar proyectos en GitHub

Extraído de la [guía oficial de GitHub](https://guides.github.com/activities/citable-code/).

A través de una aplicación de terceros (Zenodo, financiado por el CERN), es posible crear un DOI para uno de nuestros proyectos.

Estos son los pasos

## Paso 1. Elegir un repositorio

Este repositorio debe ser abierto (público), o de lo contrario Zenodo no podrá acceder al mismo. Hay que recordar escoger una licencia para el proyecto. Esta web puede ayudarnos [http://choosealicense.com/](http://choosealicense.com/).

## Paso 2. Entrar en Zenodo

Iremos a [Zenodo](http://zenodo.org/) y haremos login con GitHub. Lo único que tenemos que hacer en esta parte es autorizar a Zenodo a conectar con nuestra cuenta de GitHub.

!!! important

    Si deseas archivar un repositorio que pertenece a una organización en GitHub, deberás asegurarte de que el administrador de la organización haya habilitado el acceso de terceros a la aplicación Zenodo.

## Paso 3. Seleccionar los repositorios

En este punto, hemos autorizado a Zenodo para configurar los permisos necesarios para permitir el archivado y la emisión del DOI. Para habilitar esta funcionalidad, simplemente haremo clic en el botón que está junto a cada uno de los repositorios que queremos archivar.

!!! important

    Zenodo solo puede acceder a los repositorios públicos, así que debemos asegurarnos de que el repositorio que deseamos archivar sea público.

## Paso 4. Crear una nueva _release_

Por defecto, Zenodo realiza un archivo de nuestro repositorio de GitHub cada vez que crea una nueva versión. Como aún no tenemos ninguna, tenemos que volver a la vista del repositorio principal y hacer clic en el elemento del encabezado de versiones (_releases_).

## Paso 5. Acuñar un DOI

Antes de que Zenodo pueda emitir un DOI para nuestro repositorio, deberemos proporcionar cierta información sobre el repositorio de GitHub que acaba de archivar.

Una vez que estemos satisfechos con la descripción, heremos clic en el botón publicar.

## Paso 6. Publicar

De vuelta a nuestra página de Zenodo, ahora deberíamos ver el repositorio listado con una nueva insignia que muestra nuestro nuevo DOI.

!!! tip

    Podemos colocar la insigna en nuestro proyecto. Para eso haremos clic en la imagen DOI gris y azul. Se abrirá una ventana emergente y el texto que aparece como _Markdown_ es el que deberemos copiar
    en nuestro archivo _README.md_.
