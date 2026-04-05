# L02: Uso de la Terminal

## Introducción

Esta sesión introduce el entorno de trabajo que se usará durante todo el semestre: la terminal Linux. El dominio de la terminal es una habilidad fundamental para cualquier desarrollador de software.

En este laboratorio, se ha tomado el testamento de don Federico Santa María Carrera, del que deriva la actual **Universidad Técnica Federico Santa María**. El testamento ha sido dividido por párrafos y distribuido en un árbol de archivos con una estructura específica. Siguiendo las instrucciones, usted deberá ser capaz de reconstruir el testamento a partir de los fragmentos, aplicando comandos de terminal para inspeccionar, modificar y transformar el árbol de archivos. Al finalizar, un script de verificación comprobará que el resultado es correcto.

### Prerrequisitos

- Tener sesión iniciada en la VM Lubuntu del equipo asignado
- Conocer el propio RUT (sin puntos ni guión, dígito verificador en mayúscula si es K)

### Objetivo general

- Dado un árbol de archivos con una estructura y contenido específicos, aplicar comandos de terminal para inspeccionarlo, modificarlo y transformarlo hasta obtener un resultado final que cumpla con las condiciones indicadas en el script de verificación.

### Objetivos específicos

- Ubicarse en el sistema de archivos (`pwd`, `ls`, `cd`)
- Descargar y descomprimir archivos desde la terminal (`wget`, `tar`)
- Inspeccionar y filtrar archivos de texto (`cat`, `grep`, `wc`, `sort`, `uniq`, `head`, `tail`)
- Encadenar y transformar salidas con tuberías (`|`, `xargs`)
- Manipular archivos y directorios (`cp`, `mv`, `rm`, `mkdir`, `find`)
- Comprender y modificar permisos de archivos (`chmod`)

### Metodología

A lo largo de este procedimiento se describirán múltiples comandos y su forma de uso. Para cada uno de ellos se debe leer la explicación y ejecutar los ejemplos en la terminal. Siguiendo paso a paso la metodología, el objetivo final es aplicar todos los comandos sobre el árbol de trabajo personal para que el script `.check.sh` retorne que el resultado es correcto.

### Contexto

Cada estudiante trabaja con un árbol de directorios propio, descargable desde una url específica. La estructura y la dificultad de desarrollo es la misma para todos, pero los nombres de archivos y contenidos varían.

Las instrucciones para obtener el árbol de directorios están en la sección 5, pero se describen al inicio para facilitar el entendimiento futuro. 
El árbol de directorios que se utilizará como parte de este laboratorio tiene la siguiente forma:

```
lab02/
├── .check.sh
├── .index.txt
├── .move.txt
├── .remove.txt
├── <sigla>.txt
├── ...
├── <SIGLA>/
│   ├── .index.txt
│   ├── <sigla>.txt
│   └── <sigla>.txt
├── <SIGLA>/
│   ├── .index.txt
│   ├── <sigla>.txt
│   └── <sigla>.txt
└── ...
```

- `<SIGLA>` es una secuencia de **exactamente 6 letras aleatorias** (por ejemplo `xkqrtm`, `bvoplz`).
- El árbol tiene **un solo nivel de subdirectorios** dentro de `lab02/`.
- Puede haber archivos `.txt` sueltos directamente en la raíz de `lab02/`.
- Cada subdirectorio contiene únicamente archivos `.txt` con nombres de 6 letras.

### Archivos especiales

#### `.index.txt` (en la raíz)

Orden de ensamblado global: lista de siglas de carpetas y nombres de archivos sueltos en la raíz, uno por línea, en el orden en que deben concatenarse.

#### `.index.txt` (dentro de cada carpeta)

Orden de ensamblado de los fragmentos de esa carpeta. Si no existe se concatenan en orden alfabético.

#### `.move.txt`

Lista de pares `<origen> <destino>`, uno por línea. Indica qué archivos deben moverse.

#### `.remove.txt`

Lista de siglas (6 letras), una por línea. Indica qué nombres deben eliminarse del árbol en todas sus ocurrencias. Puede corresponder a archivos `.txt` o a carpetas enteras. Si el nombre del archivo está dentro de algún archivo .index.txt, la entrada del archivo debe ser eliminada de ese .index.txt también.

#### `.check.sh`

Script de verificación del resultado final. Este script concatena los diferentes archivos .txt presentes en las carpetas y directorios siguiendo el orden indicado por los `.index.txt` generando el archivo `.check.txt`. Luego calcula el hash del archivo `.check.txt` y lo compara con el hash esperado. Si el hash coincide, el script imprime "¡Correcto!" y devuelve un código de salida 0. Si no coincide, imprime "Incorrecto" y devuelve un código de salida 1.

Hash es el nombre que recibe el resultado de aplicar una función matemática a un conjunto de datos, generando una cadena de caracteres única que representa ese conjunto. Si el contenido del archivo cambia, aunque sea mínimamente, el hash resultante también cambiará, lo que permite verificar la integridad del archivo.

## Actividad

### 1. `pwd`

`pwd` (_print working directory_) muestra la ruta absoluta del directorio en el que se está trabajando actualmente.

```bash
pwd
```

La **ruta absoluta** comienza siempre desde la raíz del sistema de archivos (`/`) y es válida desde cualquier lugar del sistema. Después de iniciar sesión, la terminal muestra el directorio personal del usuario, típicamente `/home/alumno`.

### 2. Rutas y comodines (_wildcards_)

En la terminal, las rutas y los comodines permiten referirse a archivos y directorios con expresiones concisas.

**Rutas especiales:**

| Expresión | Significado                           |
| --------- | ------------------------------------- |
| `.`       | El directorio actual                  |
| `..`      | El directorio padre (un nivel arriba) |
| `~`       | El directorio personal del usuario    |
| `/`       | La raíz del sistema de archivos       |

```bash
ls .    # Contenido del directorio actual
ls ..   # Contenido del directorio padre
ls ~    # Contenido del directorio personal
```

**Comodines (_wildcards_):** La shell los expande antes de ejecutar el comando, por lo que funcionan con cualquier comando que acepte rutas.

| Comodín | Significado                                                     |
| ------- | --------------------------------------------------------------- |
| `*`     | Cualquier secuencia de caracteres (incluida la secuencia vacía) |
| `?`     | Exactamente un carácter cualquiera                              |
| `[abc]` | Exactamente uno de los caracteres listados                      |
| `[a-z]` | Exactamente un carácter en el rango indicado                    |

Los comodines **no atraviesan directorios**: `*.txt` solo coincide con archivos en el directorio actual, no en subdirectorios.

### 3. `ls`

`ls` lista el contenido del directorio actual (o del directorio indicado como argumento).

```bash
ls
ls -l
ls -la
ls -lh
ls /etc
ls .*
```

| Opción | Efecto                                                |
| ------ | ----------------------------------------------------- |
| `-l`   | Lista detallada: permisos, propietario, tamaño, fecha |
| `-a`   | Incluye archivos ocultos (nombre comienza con `.`)    |
| `-h`   | Tamaños en formato legible (KB, MB, ...)              |

La primera columna de `ls -l` muestra los permisos del archivo. La letra inicial indica el tipo: `d` para directorio, `-` para archivo regular.

### 4. `cd`

`cd` (_change directory_) cambia el directorio de trabajo actual.

```bash
cd /etc           # Ruta absoluta: válida desde cualquier lugar
pwd
cd ..             # Subir un nivel (directorio padre)
pwd
cd ~              # Ir al directorio personal del usuario
pwd
cd -              # Volver al directorio anterior
pwd
```

Al usar la terminal, el usuario tiene una carpeta denominada _personal_ en la que puede trabajar libremente. El símbolo `~` es un alias del directorio personal del usuario. Es la forma más rápida de regresar al punto de partida desde cualquier ubicación.

Navegar al directorio `/usr/share/doc`, verificar la ubicación, y volver al directorio personal:

```bash
cd /usr/share/doc
pwd
ls
cd ~
pwd
cd /etc
pwd
cd
pwd
```

### 5. `wget`

`wget` descarga un archivo desde una URL directamente en la terminal, sin necesidad de abrir un navegador.

Cada estudiante descarga su árbol de trabajo personal usando su **RUT como nombre de archivo**. El RUT debe escribirse **sin puntos y guión**, con el dígito verificador en **mayúscula** si es `K`.

Ejemplos de formato:

| RUT original   | Formato para la URL |
| -------------- | ------------------- |
| `12.345.678-9` | `12345678-9`        |
| `19.876.543-k` | `19876543-K`        |

El archivo se descarga desde:

```
https://ptoledo.org/courses/utfsm/inf20125l/20261/l02/<rut>.tar.gz
```

Reemplazar `<rut>` por el RUT propio con el formato indicado:

```bash
cd ~                                                                # Ir al directorio personal
wget https://ptoledo.org/courses/utfsm/inf20125l/20261/l02/<rut>.tar.gz   # Descargar el archivo
ls -lh                                                              # Verificar que el archivo se descargó correctamente
```

> **Importante**: Cada estudiante debe descargar su propio archivo, con su RUT. No se deben compartir archivos entre estudiantes, ni descargar el archivo de otro compañero. El archivo de cada estudiante es único y contiene un árbol de trabajo diferente, aunque con la misma estructura y dificultad.

Al descargar se verá un archivo con extensión `.tar.gz` en el directorio personal.

### 6. `tar`

`tar` empaqueta y desempaqueta conjuntos de archivos. Es el formato estándar de distribución de archivos en Linux.

Para descomprimir el archivo descargado con `wget`:

```bash
tar -xzf <archivo>.tar.gz
ls
```

| Opción | Efecto                                                |
| ------ | ----------------------------------------------------- |
| `-x`   | Extraer (_extract_)                                   |
| `-z`   | Descomprimir con gzip (necesario para archivos `.gz`) |
| `-f`   | El siguiente argumento es el nombre del archivo       |

De referencia, también existen opciones para crear archivos tar:

| Opción | Efecto                                              |
| ------ | --------------------------------------------------- |
| `-v`   | Modo detallado: muestra los archivos al procesarlos |
| `-c`   | Crear un archivo tar                                |

Se habrá creado el directorio `lab02/`. Entrar y explorar su contenido:

```bash
cd lab02
ls
ls -l
```

### 7. `cat`

`cat` (_concatenate_) imprime el contenido completo de uno o más archivos en la pantalla. Con varios archivos, los concatena en orden.

```bash
cat .move.txt
cat .remove.txt
```

Leer el `.index.txt` de un subdirectorio para ver el orden de ensamblado de sus fragmentos:

```bash
cat <SIGLA>/.index.txt
```

Leer uno de los archivos de fragmento (reemplazar `<SIGLA>` por nombres reales del árbol):

```bash
cat <SIGLA>/<SIGLA>.txt
```

Para concatenar los fragmentos de una carpeta hay que leer el `.index.txt`, ver el orden indicado y ejecutar `cat` con los archivos en ese orden:

```bash
cat <SIGLA>/<sigla1>.txt <SIGLA>/<sigla2>.txt <SIGLA>/<sigla3>.txt
```

### 8. `chmod`

`chmod` (_change mode_) modifica los permisos de un archivo o directorio.

Cada archivo tiene permisos para tres grupos: propietario (`u`), grupo (`g`) y otros (`o`). Los permisos son lectura (`r`), escritura (`w`) y ejecución (`x`).

Ver los permisos actuales:

```bash
ls -l <SIGLA>/<SIGLA>.txt
```

La cadena de permisos se lee así: `d rwx r-x r-x`

| Posición  | Significado                        |
| --------- | ---------------------------------- |
| `d` / `-` | Tipo: directorio o archivo regular |
| `rwx`     | Permisos del propietario (`u`)     |
| `r-x`     | Permisos del grupo (`g`)           |
| `r-x`     | Permisos de otros (`o`)            |

**Notación simbólica**:

```bash
chmod u-w <SIGLA>/<SIGLA>.txt          # Quitar escritura al propietario
ls -l <SIGLA>/<SIGLA>.txt
echo "prueba" >> <SIGLA>/<SIGLA>.txt   # Intentar escribir → error
chmod u+w <SIGLA>/<SIGLA>.txt          # Restaurar escritura
```

**Notación octal** (`r=4`, `w=2`, `x=1`):

| Valor | Binario | Permisos |
| ----- | ------- | -------- |
| `7`   | `111`   | `rwx`    |
| `6`   | `110`   | `rw-`    |
| `5`   | `101`   | `r-x`    |
| `4`   | `100`   | `r--`    |
| `0`   | `000`   | `---`    |

```bash
chmod 644 <SIGLA>/<SIGLA>.txt   # rw-r--r--
chmod 755 <SIGLA>/              # rwxr-xr-x
ls -l <SIGLA>/<SIGLA>.txt
```

El árbol de trabajo incluye el script `.check.sh`, que reconstruye el testamento de don Federico y verifica que el contenido es el correcto. Se entrega **sin permiso de ejecución**. Hay que otorgar ese permiso ahora para poder ejecutarlo:

```bash
ls -l .check.sh       # Verificar que no tiene permiso de ejecución
chmod u+x .check.sh
ls -l .check.sh       # Confirmar el cambio
./.check.sh           # Ejecutar
```

En este punto la verificación **fallará** con el mensaje "El archivo NO ha sido reconstruido correctamente": el árbol todavía no ha sido transformado. Ese es el comportamiento esperado. El objetivo de las siguientes secciones es aplicar todas las transformaciones necesarias hasta que `.check.sh` retorne éxito. Al ejecutar el script se generará un archivo `.check.txt` con el resultado de la reconstrucción.

### 9. `grep`

`grep` busca líneas que coincidan con un patrón dentro de archivos o en la entrada estándar.

```bash
grep "palabra" <SIGLA>/<SIGLA>.txt      # Líneas que contienen "palabra"
grep -i "palabra" <SIGLA>/<SIGLA>.txt   # Sin distinguir mayúsculas/minúsculas
grep -n "palabra" <SIGLA>/<SIGLA>.txt   # Con número de línea
grep -r "palabra" .                     # Recursivo en todo el árbol
grep -l "palabra" <SIGLA>/*.txt         # Solo los nombres de archivos que la contienen
grep -c "palabra" <SIGLA>/*.txt         # Número de ocurrencias por archivo
grep -o "palabra" <SIGLA>/<SIGLA>.txt   # Solo la parte que coincide (una por línea)
```

El testamento contiene la frase **"llegar al más alto grado del saber humano"**. Mediante el uso de grep, localice el archivo que contiene esta frase.

### 10. `find`

`find` busca archivos y directorios dentro de un árbol de directorios, de forma recursiva.

```bash
find .                                              # Todo el contenido del directorio actual
find . -name "*.txt"                                # Archivos con nombre que termina en .txt
find . -type f                                      # Solo archivos (no directorios)
find . -type d                                      # Solo directorios
find . -name "abcdef.txt"                           # Buscar un archivo específico por nombre
find . \( -name "abcdef.txt" -o -name "abcdef" \)   # Nombre exacto o directorio con esa sigla
```

Para localizar dónde está un archivo determinado antes de moverlo:

```bash
find . -name "<sigla>.txt"
```

Para verificar que no queda ningún rastro de una sigla en el árbol:

```bash
find . \( -name "<sigla>.txt" -o -name "<sigla>" \)
```

### 11. `wc`

`wc` (_word count_) cuenta líneas, palabras o bytes en un archivo.

```bash
wc <SIGLA>/<SIGLA>.txt             # Líneas, palabras y bytes
wc -l <SIGLA>/<SIGLA>.txt          # Solo líneas
wc -w <SIGLA>/<SIGLA>.txt          # Solo palabras
wc -c <SIGLA>/<SIGLA>.txt          # Solo bytes
```

### 12. `|` (pipe)

El operador `|` encadena comandos: la salida estándar de un comando se convierte en la entrada del siguiente, sin necesidad de archivos intermedios.

```bash
comando1 | comando2
comando1 | comando2 | comando3
```

Por ejemplo, para contar cuántas líneas produce `cat` sobre toda una carpeta:

```bash
cat <SIGLA>/*.txt | wc -l
```

O para contar cuántos archivos `.txt` hay en el árbol:

```bash
find . -name "*.txt" | wc -l
```

### 13. `sort`

`sort` ordena las líneas de un archivo o de la entrada estándar.

```bash
sort <SIGLA>/<SIGLA>.txt           # Orden alfabético (ascendente)
sort -r <SIGLA>/<SIGLA>.txt        # Orden inverso
sort -n <SIGLA>/<SIGLA>.txt        # Orden numérico
sort -u <SIGLA>/<SIGLA>.txt        # Orden alfabético, eliminando duplicados
```

Listar todas las rutas de archivos `.txt` del árbol en orden alfabético:

```bash
find . -name "*.txt" | sort
```

### 14. `xargs`

`xargs` toma elementos de la entrada estándar (uno por línea) y los pasa como argumentos a un comando. Es muy útil combinado con `find` para operar sobre múltiples archivos.

```bash
find . -name "*.txt" | xargs wc -l           # Contar líneas en todos los archivos .txt
find . -name "*.txt" | sort | xargs cat      # Concatenar todos los archivos .txt en orden
```

Concatenar todos los archivos `.txt` del árbol en orden alfabético y contar sus líneas:

```bash
find . -name "*.txt" | sort | xargs cat | wc -l
```

### 15. `uniq`

`uniq` elimina o cuenta líneas duplicadas **consecutivas**. Debe combinarse con `sort` para que los duplicados queden juntos antes de procesarlos.

```bash
sort <SIGLA>/<SIGLA>.txt | uniq             # Sin duplicados
sort <SIGLA>/<SIGLA>.txt | uniq -d          # Solo las líneas duplicadas
sort <SIGLA>/<SIGLA>.txt | uniq -c          # Con conteo de repeticiones
```

El árbol contiene líneas duplicadas distribuidas entre distintos archivos. Se puede comprobar comparando el conteo de líneas antes y después de aplicar `uniq` sobre todos los archivos del árbol, incluyendo los de la raíz:

```bash
find . -name "*.txt" | sort | xargs cat | wc -l
find . -name "*.txt" | sort | xargs cat | sort | uniq | wc -l
```

Si el segundo número es menor, hay líneas repetidas.

### 16. `cp`

`cp` (_copy_) copia archivos o directorios.

```bash
cp <SIGLA>/<SIGLA>.txt copia.txt           # Copiar archivo con nuevo nombre
cp <SIGLA>/<SIGLA>.txt /tmp/               # Copiar a otro directorio
cp -r <SIGLA>/ <SIGLA>_backup/             # Copiar directorio completo (recursivo)
ls
ls <SIGLA>_backup/
```

Antes de modificar el árbol en las siguientes actividades, crear una copia de seguridad completa:

```bash
cd ~
cp -r lab02/ lab02_backup/
ls
```

### 17. `mkdir`

`mkdir` (_make directory_) crea uno o más directorios.

```bash
mkdir resultados                           # Crear un directorio
mkdir -p a/b/c                             # Crear árbol de directorios (con padres intermedios)
mkdir dir1 dir2 dir3                       # Crear varios a la vez
ls
```

### 18. `mv`

`mv` (_move_) mueve o renombra archivos y directorios.

```bash
mv copia.txt copia_renombrada.txt          # Renombrar
mv copia_renombrada.txt /tmp/              # Mover a otro directorio
```

Leer las instrucciones de `.move.txt` y ejecutar cada movimiento:

```bash
cd ~/lab02
cat .move.txt
```

Cada línea tiene el formato `<origen> <destino>`. Para cada par, ejecutar:

```bash
mv <origen> <destino>
```

Verificar cada movimiento con `ls` antes y después. El destino de algunos archivos es una carpeta que todavía no existe en el árbol; en ese caso hay que crearla primero con `mkdir`:

```bash
mkdir <directorio-nuevo>
mv <origen> <directorio-nuevo>/
```

Ejecutar **todos** los pares indicados en `.move.txt`.

### 19. `rm`

`rm` (_remove_) elimina archivos o directorios.

```bash
rm archivo.txt                              # Eliminar archivo
rm -r directorio/                           # Eliminar directorio y su contenido
rm -f archivo.txt                           # Forzar eliminación sin confirmación
```

> **Precaución**: `rm` no tiene papelera. Los archivos eliminados no se pueden recuperar. El directorio `lab02_backup/` permite restaurar el árbol en caso de error.

Leer las instrucciones de `.remove.txt`:

```bash
cat .remove.txt
```

Cada línea contiene una sigla de 6 letras. Algunas corresponden a archivos `.txt` duplicados dispersos en el árbol; otras pueden corresponder a una **carpeta entera**. Deben eliminarse **todas las ocurrencias** de cada sigla (tanto el archivo `<SIGLA>.txt` como el directorio `<SIGLA>/` si existiesen), sin importar en qué nivel se encuentren.

Junto con lo anterior, hay que revisar que si una sigla, como archivo o como carpeta, está referenciada en algún `.index.txt`, esa referencia también debe ser eliminada. Para esto se puede usar `grep` para localizar las líneas que contienen la sigla y luego editarlas con un editor de texto como `vim`.

Tras eliminar todas las siglas de `.remove.txt`, ejecutar `.check.sh` para verificar que el árbol fue procesado correctamente:

```bash
cd ~/lab02
./.check.sh
```

Si el resultado es **"El archivo ha sido reconstruido correctamente"**, el árbol fue reconstruido exitosamente. Si todavía falla, revisar que todos los pares de `.move.txt` fueron aplicados y todas las siglas de `.remove.txt` fueron eliminadas. El comando le otorgará alguna ayuda para revisar cual es el error.

### 20. `head`

`head` muestra las primeras líneas de un archivo. Por defecto muestra las primeras 10.

```bash
head <sigla>.txt          # Primeras 10 líneas
head -5 <sigla>.txt       # Primeras 5 líneas
head -1 <sigla>.txt       # Solo la primera línea
```

Ver la primera línea de todos los archivos `.txt` del árbol a la vez:

```bash
find . -name "*.txt" -exec head -1 {} +
```

### 21. `tail`

`tail` muestra las últimas líneas de un archivo. Por defecto muestra las últimas 10.

```bash
tail <sigla>.txt          # Últimas 10 líneas
tail -5 <sigla>.txt       # Últimas 5 líneas
tail -1 <sigla>.txt       # Solo la última línea
```

## Resumen de comandos

| Comando          | Para qué sirve                                                   |
| ---------------- | ---------------------------------------------------------------- |
| `pwd`            | Mostrar el directorio de trabajo actual                          |
| `.` `..` `~` `*` | Rutas especiales y comodines                                     |
| `ls`             | Listar contenido de un directorio                                |
| `cd`             | Cambiar de directorio                                            |
| `wget`           | Descargar un archivo desde una URL                               |
| `tar`            | Empaquetar y desempaquetar archivos                              |
| `cat`            | Mostrar y concatenar archivos de texto                           |
| `chmod`          | Modificar permisos de archivos                                   |
| `grep`           | Buscar líneas que coincidan con un patrón                        |
| `find`           | Buscar archivos y directorios en el árbol                        |
| `wc`             | Contar líneas, palabras o bytes                                  |
| `\|`             | Encadenar la salida de un comando como entrada del siguiente     |
| `sort`           | Ordenar líneas                                                   |
| `xargs`          | Pasar líneas de la entrada estándar como argumentos a un comando |
| `uniq`           | Eliminar o contar líneas duplicadas consecutivas                 |
| `cp`             | Copiar archivos o directorios                                    |
| `mkdir`          | Crear directorios                                                |
| `mv`             | Mover o renombrar archivos o directorios                         |
| `rm`             | Eliminar archivos o directorios                                  |
| `head`           | Ver las primeras líneas de un archivo                            |
| `tail`           | Ver las últimas líneas de un archivo                             |
