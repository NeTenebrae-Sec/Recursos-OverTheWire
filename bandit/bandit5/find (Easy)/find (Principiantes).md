---
sticker: ""
tags:
  - OverTheWire
  - BASH
banner: _assets/Pasted image 20260408145132.png
banner_y: "49.5"
---

<iframe title="ENCUENTRA ARCHIVOS como un HACKER | OverTheWire: bandit5" src="https://www.youtube.com/embed/khTm8RrxVMM?feature=oembed" height="113" width="200" allowfullscreen="" allow="fullscreen" style="aspect-ratio: 1.76991 / 1; width: 100%; height: 100%;"></iframe>
# ¿Para qué sirve find?
El comando `find` sirve para buscar archivos de forma precisa en sistemas Linux, incluye parámetros de búsqueda y concatenación de comandos sin necesidad de pipes (`|`).

Este comando lo puedes practicar en [Bandit5 -> bandit6](https://overthewire.org/wargames/bandit/bandit6.html).
## Parámetro `-help`
```bash
Usage: find [-H] [-L] [-P] [-Olevel] [-D debugopts] [path...] [expression]

Default path is the current directory; default expression is -print.
Expression may consist of: operators, options, tests, and actions.

Operators (decreasing precedence; -and is implicit where no others are given):
      ( EXPR )   ! EXPR   -not EXPR   EXPR1 -a EXPR2   EXPR1 -and EXPR2
      EXPR1 -o EXPR2   EXPR1 -or EXPR2   EXPR1 , EXPR2

Positional options (always true):
      -daystart -follow -nowarn -regextype -warn

Normal options (always true, specified before other expressions):
      -depth -files0-from FILE -maxdepth LEVELS -mindepth LEVELS
      -mount -noleaf -xdev -ignore_readdir_race -noignore_readdir_race

Tests (N can be +N or -N or N):
      -amin N -anewer FILE -atime N -cmin N -cnewer FILE -context CONTEXT
      -ctime N -empty -false -fstype TYPE -gid N -group NAME -ilname PATTERN
      -iname PATTERN -inum N -iwholename PATTERN -iregex PATTERN
      -links N -lname PATTERN -mmin N -mtime N -name PATTERN -newer FILE
      -nouser -nogroup -path PATTERN -perm [-/]MODE -regex PATTERN
      -readable -writable -executable
      -wholename PATTERN -size N[bcwkMG] -true -type [bcdpflsD] -uid N
      -used N -user NAME -xtype [bcdpfls]

Actions:
      -delete -print0 -printf FORMAT -fprintf FILE FORMAT -print 
      -fprint0 FILE -fprint FILE -ls -fls FILE -prune -quit
      -exec COMMAND ; -exec COMMAND {} + -ok COMMAND ;
      -execdir COMMAND ; -execdir COMMAND {} + -okdir COMMAND ;

Other common options:
      --help                   display this help and exit
      --version                output version information and exit

Valid arguments for -D:
exec, opt, rates, search, stat, time, tree, all, help
Use '-D help' for a description of the options, or see find(1)

Please see also the documentation at https://www.gnu.org/software/findutils/.
```
> La sección de `-help` es algo que tienen casi todos los comandos, es bueno tenerla a la mano por si no tienes una terminal cerca.
## Uso del comando
```bash
find [ruta] [expresión]
```
> `find` tiene como ruta por defecto el directorio actual `./`. Esto quiere decir que si no especificas una ruta, find buscará en el directorio actual en el que lo estés ejecutando.
### Ejemplo de uso
![[Pasted image 20260413105548.webp]]
> Aquí estamos llamando a `find` para buscar archivos dentro de la ruta relativa `./inhere`. Además, aplicamos el filtro `-type f` para que solo enseñe archivos regulares.
## Parámetros
### Buscar por tamaño de archivo
```bash
find -size N[bcwkMG]
```
> Donde 'N' es "número" o "tamaño" a buscar.

- **`N`**: Exactamente N unidades.
- **`+N`**: Más grande que N.
- **`-N`**: Más pequeño que N.

- **`b`**: bloques de 512 bytes (por defecto).
- **`c`**: bytes.
- **`k`**: Kilobytes.
- **`M`**: Megabytes.
- **`G`**: Gigabytes.

> [!ERROR] Bug Actuales  
> A día de hoy, `find` tiene un bug de interpretación cuando intentas buscar archivos con un valor de `1` para los tamaños `M` y `G`. Más información en https://savannah.gnu.org/bugs/?group=findutils.
#### Ejemplo
![[Pasted image 20260413111236.webp]]
> Uso de `find` para la búsqueda de archivos con un peso de `+2M`. Al no encontrar resultados cambiamos el filtro a `-` para ver resultados con un peso inferior.
### Busca por tipo de archivo.
```bash
find -type [bcdpflsD]
```

- **`f`**: _regular file_ (archivo normal).
- **`d`**: _directory_ (carpetas).
- **`l`**: _symbolic link_ (accesos directos).

> [!INFO] Información importante  
> Recuerda que en Linux **todo es un archivo**, debido a esto es importante especificar el tipo del mismo para hacer búsquedas especificas.
#### Ejemplo
![[Pasted image 20260413111529.webp]]
> `find` listando los directorios de la carpeta `./inhere`.

### Buscar por Permisos
```bash
find [expresión]
```
> En el `help` aparecen bajo la sección **Tests**.

- **`-readable`**: Archivos que el usuario actual puede leer.
- **`-writable`**: Archivos que el usuario actual puede modificar.
- **`-executable`**: Archivos que el usuario puede ejecutar (o entrar, si es un directorio).
#### Ejemplo 
![[Pasted image 20260413111704.webp]]
> `find` dando una lista de archivos a los cuales nuestro usuario tiene acceso al permiso de ejecución.
#### Más información sobre los Permisos de Linux

<iframe title="¡ARCHIVOS OCULTOS y PERMISOS DE LINUX | OverTheWire: Bandit3" src="https://www.youtube.com/embed/jrftRDEuy1o?feature=oembed" height="113" width="200" allowfullscreen="" allow="fullscreen" style="aspect-ratio: 1.76991 / 1; width: 100%; height: 100%;"></iframe>
> En este video hablamos de como funcionan los permisos en Linux, indispensable para saber a que nos estamos refiriendo en esta sección.

### Operadores Lógicos
Sirven para combinar varios filtros o invertir su significado. Siguen un **orden de precedencia** (el sistema evalúa primero los `NOT`, luego los `AND` y al final los `OR`).

- **`! [EXPR]`** o **`-not [EXPR]`** (Negación): Invierte el resultado.
    - _Traducción mental:_ "Que **no** cumpla lo siguiente".
	    - _Ejemplo:_ `! -executable` → Que **no** sea un archivo ejecutable.

- **`[EXPR1] -a [EXPR2]`** o **`-and`** (Conjunción): Ambas condiciones deben ser verdaderas. **Es el operador por defecto** (si pones dos filtros juntos sin definir un operador, `find` asume que hay un `AND` entre ambos).
    - _Traducción mental:_ "Que cumpla esto **y** también esto otro".
	    - _Ejemplo:_ `-type f -readable` → Busca un archivo que sea regular **y** que además lo pueda leer.

- **`[EXPR1] -o [EXPR2]`** o **`-or`** (Disyunción): Basta con que una de las dos sea verdadera.
    - _Traducción mental:_ "Que cumpla esto **o** que cumpla aquello".
	    - _Ejemplo:_ `-name "*.txt" -o -name "*.md"` → Busca archivos que terminen en .txt **o** en .md.

- **`( [EXPR] )`** (Agrupamiento): Se usa para consultas complejas.
    - _Traducción mental:_ "Primero resuelve lo que está aquí adentro".
	    - **Importante:** En la terminal se escriben como `\( \)` para que la Shell (Bash/Zsh) no intente ejecutarlos como un subproceso debido a que es un **caracter especial** (Ver video de los caracteres especiales).
#### Más información de los caracteres especiales
<iframe title="CARACTERES ESPECIALES y más info de las RUTAS DE LINUX | OverTheWire: bandit2" src="https://www.youtube.com/embed/H36oDKPKkE8?feature=oembed" height="113" width="200" allowfullscreen="" allow="fullscreen" style="aspect-ratio: 1.76991 / 1; width: 100%; height: 100%;"></iframe>
> En este video hablamos de como funcionan los caracteres especiales en Linux, indispensable para saber a que nos estamos refiriendo en esta sección.
## Llamada a la acción

```bash
find [expresión] -exec COMMAND {} +
```
Esta es la parte más potente del `find`. Permite "hacer algo" con lo que encontraste.

- **`COMMAND`**: El comando que quieres ejecutar (ej. `cat` o `file`).
- **`{}`**: Es un **placeholder**. `find` lo reemplaza automáticamente por la ruta del archivo que encontró.

- **`+` (Terminador de lista)**:    
    - **Funcionamiento**: Agrupa todos los archivos encontrados y los pasa como argumentos al comando en una **única ejecución**.
    - **Ejemplo**: Si `find` encuentra 100 archivos, ejecuta `COMMAND archivo1 archivo2 ... archivo100`.
    - **Ventaja**: Es mucho más eficiente y rápido, ya que solo inicia el proceso del comando una vez.
Ejemplo:
![[Pasted image 20260413115123.webp]]
![[Pasted image 20260413115300.webp]]
> `find`, luego de encontrar archivos superiores a 1033 bytes, procediendo a ejecutar el comando `file` **una sola vez**. El equivalente a `file archivo1 archivo2 archivo3`.

- **`;` (Terminador individual)**:
    - **Funcionamiento**: Ejecuta el comando **una vez por cada archivo** encontrado. (Funciona mejor cuando necesitas ejecutar un comando que tenga parámetros, Ej `ls -Ra`).
    - **Ejemplo**: Si `find` encuentra 100 archivos, ejecuta `COMMAND archivo1`, luego `COMMAND archivo2`... y así 100 veces.       
    - **Ventaja**: Necesario cuando el comando destino no puede procesar múltiples archivos a la vez o requiere que cada ejecución sea independiente.
    - **Nota técnica**: En la terminal, debes escribirlo como `\;`. Esto es obligatorio porque el punto y coma (`;`) es un carácter especial que la _Shell_ usa para separar comandos, y si no lo escapas, la _Shell_ intentará ejecutar el comando por sí misma en lugar de pasárselo a `find`.
Ejemplo:
![[Pasted image 20260413115009.webp]]
![[Pasted image 20260413115411.webp]]
> `find`, luego de encontrar archivos superiores a 1033 bytes, procediendo a ejecutar el comando `file` la misma cantidad de veces que archivos encontrados. El equivalente a primero hacer `file archivo1`, luego `file archivo2`, luego `file archivo3` y así sucesivamente.
---
