# Conflicto de fusión resuelto

## 1. ¿Qué archivo entró en conflicto y por qué?
El archivo que presentó el conflicto fue Tareas/Tarea1/README.md. Esto ocurrió porque ambos miembros del grupo agregamos un archivo con el mismo nombre y en la misma ubicación, pero cada uno tenía una versión diferente.

Git identificó el problema como un conflicto add/add, ya que el archivo se había creado de forma independiente en ambas ramas. Al intentar unirlas, Git no pudo determinar automáticamente cuál de las dos versiones debía conservarse.

Este tipo de situación puede ocurrir cuando varios integrantes trabajan en paralelo y realizan cambios sin coordinar previamente los archivos que van a agregar al proyecto.

## 2. ¿Qué mostraba Git exactamente?

```text
La rama no estaba en el clon principal
Situación: se pensó integrar la rama en el repositorio privado Programacion-II.
Causa: la rama vive en el repositorio público Programacion-II-Publico, que es el de la Tarea 1. La carpeta local Programacion-II-Publico no tiene .git.
Solución: se trabajó directamente sobre el repositorio público y su solicitud #1.
```
```text
Descargas por SSH que se quedan detenidas
Síntoma: git fetch y git checkout por SSH se quedaron detenidos en unos 440 KB (warning: garbage found: .git/objects/pack/tmp_pack_...). Las transferencias pequeñas (lista de ramas, commits, push) sí funcionan.
Solución: lectura por HTTPS (repositorio público, sin credenciales) y escritura por SSH: git remote set-url origin https://github.com/eduardo-planelles/Programacion-II-Publico.git git remote set-url --push origin git@github.com:eduardo-planelles/Programacion-II-Publico.git
Pendiente: revisar la red o el SSH de este equipo si vuelve a pasar.
```
```text
Bloqueo index.lock tras detener el checkout
Mensaje: fatal: Unable to create '.../Programacion-II-Gabriel/.git/index.lock': File exists.
Causa: el checkout detenido en E6 dejó el archivo de bloqueo.
Solución: como era un clon recién creado, sin archivos del usuario, se borró index.lock y se repitió git checkout -f main, esta vez por HTTPS.
```

 ## 3. ¿Cómo decidimos qué versión conservar, o cómo combinamos ambas?

En este caso, decidimos mantener en main el README inicial como referencia y dejar en la rama Avance-Eduardo la versión completa como una actualización independiente.

No combinamos los dos textos en un solo archivo, sino que mantuvimos las versiones en sus respectivas ramas para poder comparar los cambios mediante el diff de Git.

De esta manera, conservamos el archivo inicial del proyecto y también el trabajo desarrollado en la otra rama, sin mezclar ambos contenidos.

## 4. ¿Qué haríamos distinto para evitarlo la próxima vez?

Para evitar este tipo de conflictos en futuros proyectos, lo mejor sería coordinar desde el principio quién se encarga de modificar los archivos compartidos. También sería conveniente actualizar nuestra rama con los cambios de main antes de empezar a trabajar o antes de realizar una fusión.

Otra opción sería trabajar en archivos separados cuando los aportes sean independientes y luego integrarlos mediante una revisión. Además, hacer commits pequeños y revisar las diferencias antes de fusionar las ramas facilitaría la identificación de posibles problemas.

En conclusión, el conflicto se pudo resolver sin perder el trabajo de ninguno de los integrantes. La experiencia demuestra que Git permite trabajar en paralelo, pero también es importante mantener una buena coordinación y revisar los cambios antes de integrarlos.