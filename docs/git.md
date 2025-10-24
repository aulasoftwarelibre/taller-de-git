# Aspectos básicos de Git

## Instalación

### Instalando en Linux

Si quieres instalar Git en Linux a través de un instalador binario, en general puedes hacerlo a través de la herramienta básica de gestión de paquetes que trae tu distribución. Si estás en Fedora, puedes usar yum:

    $ sudo dnf install git-core

O si estás en una distribución basada en Debian como Ubuntu, prueba con apt-get:

    $ apt-get install git

### Instalando en Windows

Instalar Git en Windows es muy fácil. El proyecto msysGit tiene uno de los procesos de instalación más sencillos. Simplemente descarga el archivo exe del instalador desde la página de GitHub, y ejecútalo:

[http://msysgit.github.com/](http://msysgit.github.com/)

Una vez instalado, tendrás tanto la versión de línea de comandos (incluido un cliente SSH que nos será útil más adelante) como la interfaz gráfica de usuario estándar. Se recomienda no modificar las opciones que trae por defecto el instalador.

### Instalando en MacOS

En MacOS se recomienda tener instalada la herramienta [homebrew](https://brew.sh/). Después, es tan fácil como ejecutar:

    $ brew install git

## Configuración

### Tu identidad

Lo primero que deberías hacer cuando instalas Git es establecer tu nombre de usuario y dirección de correo electrónico. Esto es importante porque las confirmaciones de cambios (commits) en Git usan esta información, y es introducida de manera inmutable en los commits que envías:

    $ git config --global user.name "John Doe"
    $ git config --global user.email johndoe@example.com

También se recomienda configurar el siguiente parámetro:

    $ git config --global push.default simple

Otra opción muy habitual es usar `main` en vez de `master` para la rama principal. Si quieres usarla para todos tus nuevos proyectos puedes configurarla así:

    $ git config --global init.defaultBranch main

### Shell Completion

_Bash completion_ es una utilidad que permite a _bash_ completar órdenes y parámetros. Por defecto suele venir desactivada en Ubuntu y es necesario modificar el archivo `$HOME/.bashrc` para poder activarla. Simplemente hay que descomentar las líneas que lo activan.

Si usas _zsh_ puedes consultar la documentación para activar los plugins de autocompletado y alias con git. Son bastante útiles y mejorarán tu productividad.
