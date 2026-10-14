# CoopeTica R.L. — Modelo de dominio

BIS04 Programación II · Tarea 1 · Universidad Latina de Costa Rica

Modelo de clases del sistema que ordena la información de asociados, cuentas y movimientos de la cooperativa CoopeTica R.L., trabajado en pareja sobre un repositorio colaborativo.

## 1. Integrantes

| Integrante | Usuario de GitHub | Parte del modelo |
| --- | --- | --- |
| Eduardo Planelles Ramirez | [@eduardo-planelles](https://github.com/eduardo-planelles) | Asociado, Cuenta y los tres productos (rama `modelo-asociado`) |
| Gabriel Josue Rojas Marin | [@gabomrojas](https://github.com/gabomrojas) | Movimiento y los enumerados (rama `modelo-movimiento`) |

## 2. Descripción del dominio

CoopeTica R.L. es una cooperativa de ahorro y crédito costarricense de tamaño mediano que actualmente administra su operación en hojas de cálculo dispersas. Requiere un sistema que le permita ordenar la información de sus asociados, sus cuentas y sus movimientos.


Cada **asociado** de la cooperativa se identifica por su número de cédula, y de él se registra el nombre completo, el número de teléfono, el correo electrónico y la fecha en que ingresó a la cooperativa. Un asociado puede estar activo o inactivo, y la cooperativa necesita poder desactivarlo sin borrar su información histórica.

Un asociado puede abrir varias **cuentas**. Toda cuenta tiene un número único, un saldo y una fecha de apertura, y puede encontrarse abierta o cerrada. La cooperativa maneja tres **productos**: **cuenta de ahorro**, **cuenta corriente** y **certificado a plazo**. Los tres permiten consultar el saldo y recibir depósitos, pero cada uno calcula sus intereses con una tasa distinta y cobra comisiones diferentes. El certificado a plazo, además, tiene un plazo en meses y una fecha de vencimiento, y no permite retiros antes de esa fecha.

Cada **movimiento** que se realiza sobre una cuenta queda registrado con su fecha y hora, el tipo de movimiento, el monto y el saldo que quedó después de aplicarlo. Los tipos de movimiento que maneja la cooperativa son depósito, retiro, aplicación de intereses y cobro de comisión.

La cooperativa necesita poder consultar el **detalle de cualquier asociado**, **ver el estado de sus cuentas**, **registrar depósitos y retiros** **validando que haya saldo suficiente**, **calcular los intereses de un periodo** y **generar un reporte de movimientos por cuenta entre dos fechas**.


## 3. Diagrama de clases

Clases con sus atributos (tipo y visibilidad) y sus métodos (parámetros y tipo de retorno). **No incluye relaciones entre clases**, como pide el enunciado. Los diagramas se elaboraron utilizando [Miro](https://miro.com/) y se exportaron como PNG.

![Diagrama de clases del dominio CoopeTica, sin relaciones](<diagramas/5. Sistema - Clases sin relaciones (5A + 5B).png>)

**Notación:**

| Símbolo | Significado |
| --- | --- |
| `-` | privado |
| `+` | público |
| `#` | protegido |
| `$` | `{static}`: constante de clase |
| `*` | `{abstract}`: método sin cuerpo que cada producto implementa |
| «enumeration» | conjunto fijo de valores |

Se omiten los *getters* simples (`getSaldo()`, `getNumero()`, …) por legibilidad. Los constructores se muestran con el mismo nombre de la clase.

### Flujos que el modelo debe cumplir

Dos procesos del enunciado dibujados como apoyo para revisar que las clases y sus métodos alcanzan:

**Flujo de un retiro.** Las validaciones en orden: que exista la cuenta, que el titular esté activo, que la cuenta esté abierta, que el monto sea válido, que el producto permita retirar (el certificado no, antes de vencer) y que haya saldo suficiente. Si alguna falla, no se registra nada.

![Flujo de un retiro con sus validaciones](<diagramas/1. Proceso - Flujo de un retiro (validaciones).png>)

**Liquidación de intereses de un periodo.** Para cada cuenta abierta se calcula el interés del periodo sobre el saldo diario, se registra como movimiento de aplicación de intereses y se informa el resultado por cuenta.

![Liquidación de intereses de un periodo, por carriles](<diagramas/2. Proceso - Liquidación de intereses (carriles).png>)

## 4. Justificación del modelo

### Cómo identificamos las clases

Leímos el enunciado juntos y marcamos sustantivos (candidatos a clase o atributo) y verbos (candidatos a método). Un sustantivo quedó como **clase** cuando tiene datos propios y comportamiento, y se crean muchos ejemplares; como **atributo** cuando solo describe a otra cosa; y como **enumerado** cuando es un conjunto fijo de valores.

### Clases escogidas

| Clase | Por qué es clase |
| --- | --- |
| `Asociado` | Tiene identidad (la cédula), varios datos propios y reglas: se desactiva sin borrar su historia y, estando inactivo, no abre cuentas ni opera. |
| `Cuenta` (abstracta) | Tiene identidad (número único), saldo, fecha de apertura y estado, y concentra la regla central: el saldo solo cambia al registrar un movimiento, y un retiro exige saldo suficiente. Es abstracta porque no existe una "cuenta genérica": toda cuenta es de un producto. |
| `CuentaAhorro`, `CuentaCorriente`, `CertificadoPlazo` | Los tres comparten los datos y las operaciones de una cuenta, pero cada uno calcula intereses y comisiones de forma distinta, y el certificado además tiene plazo, vencimiento y prohíbe retiros antes de vencer. Por eso son tres clases y no un simple atributo "tipo". |
| `Movimiento` | Agrupa cuatro datos (fecha y hora, tipo, monto y saldo resultante), se crea muchas veces por cuenta y es un registro histórico que no debe cambiar. |
| `TipoMovimiento`, `EstadoCuenta`, `TipoProducto` (enumerados) | Son conjuntos fijos de valores: los cuatro tipos de movimiento, abierta o cerrada, y los tres productos. `TipoMovimiento` además sabe si el movimiento suma o resta al saldo. |

### Elementos del enunciado descartados o resueltos de otra forma

| Elemento del enunciado | Decisión | Motivo |
| --- | --- | --- |
| CoopeTica R.L. (la cooperativa) | No es clase | Es el sistema mismo: no tiene datos propios en el enunciado ni se crean varios ejemplares. |
| Hojas de cálculo, personal, entrevistas | Fuera del dominio | Describen la situación actual y la fuente de los requisitos, no forman parte del sistema. |
| Tipo de movimiento | Enumerado | Cuatro valores fijos; su único comportamiento es si suma o resta al saldo. |
| Abierta / cerrada · activo / inactivo | Enumerado / `boolean` | Son estados sin datos propios. |
| Interés | Método (`calcularIntereses`) | Es el resultado de un cálculo sobre el saldo de un periodo; no se guarda como dato. |
| Tasa y comisión | Constantes de cada producto | Describen al producto y no cambian de una cuenta a otra del mismo producto. |
| Periodo (de intereses o del reporte) | Parámetros `desde`, `hasta` | Es un par de fechas que se usa en una operación; no se guarda. |
| Reporte de movimientos | Resultado de una consulta (`obtenerMovimientos`) | Se arma cuando se pide, a partir de los movimientos; no se almacena. |
| Saldo | Atributo de `Cuenta` | Describe a la cuenta y solo cambia con un movimiento. |

### Elementos que agregamos y por qué

El enunciado no los nombra literalmente, pero hacen falta para cumplir lo que sí pide:

| Elemento | Para qué |
| --- | --- |
| `interesesLiquidadosHasta` (en `Cuenta`) | Recordar hasta qué fecha ya se pagaron intereses, para no pagar dos veces el mismo periodo. |
| `consultarSaldoAlCierre(fecha)` | Saber el saldo de un día: lo necesitan el cálculo de intereses (sobre el saldo diario) y el saldo inicial del reporte entre dos fechas. |
| `actualizarContacto(telefono, correo)` | El enunciado registra teléfono y correo del asociado; son datos que cambian y la cédula no. |
| `PLAZO_MINIMO_MESES`, `PLAZO_MAXIMO_MESES` y los valores de tasas y comisiones | El enunciado dice que cada producto tiene su tasa, su comisión y el certificado un plazo, pero no da cifras: son supuestos nuestros, escritos como constantes para no dejar números sueltos. |

<!-- Revisar esta tabla contra el diagrama de Miro: si alguno de estos elementos no está en el diagrama, quitar su fila. -->

### Excepciones a la regla de visibilidad

El enunciado pide atributos privados y métodos públicos, salvo justificación. Hay tres excepciones:

- **Constantes `public static`** (tasas, comisiones y límites de plazo): son valores fijos que no se pueden modificar y que otras clases necesitan consultar. Publicarlas no rompe el encapsulamiento.
- **Constructor de `Cuenta` protegido (`#`)**: no existe una cuenta genérica; solo los tres productos crean cuentas.
- **`validarRetiro(fecha)` protegido (`#`)**: es un punto de ajuste interno que el certificado redefine para impedir retiros antes de vencer; no es una operación que use el usuario.

## 5. Bitácora de uso de IA

<!-- Cada entrada: qué se preguntó, a qué herramienta y qué se hizo con la respuesta. Completar lo que está entre corchetes. -->

- **Teoría y terminología.** Eduardo consultó a **Claude Code** (Anthropic) el criterio para decidir si un sustantivo del enunciado es clase, atributo enumerado, y el significado de la visibilidad protegida. Aplicamos el criterio de "datos propios y comportamiento" para dejar `TipoMovimiento`, `EstadoCuenta` y `TipoProducto` como enumerados.
- **Líneas de diseño.** Eduardo discutió con **Claude Code** si los tres productos debían ser un atributo "tipo" o tres clases. Escogimos tres clases porque cada producto calcula intereses y comisiones distinto y el certificado tiene reglas propias.
- **Revisión de flujos.** Eduardo pidió a **Claude Code** revisar el flujo de un retiro y la liquidación de intereses contra el enunciado, para comprobar que el modelo tiene los métodos que esos procesos necesitan (por ejemplo, `consultarSaldoAlCierre` para el interés sobre el saldo diario).
- **Requisitos.** Eduardo consultó a **Gemini Notebook (NotebookLM)**, cargado con el enunciado, para verificar los requisitos y la rúbrica. Usamos las respuestas para revisar el alcance.
- **Borradores.** Claude Code también generó un borrador del modelo de clases, de las instrucciones para dibujar los diagramas y de este README. Lo usamos como punto de partida: revisamos cada clase, atributo y método contra el enunciado.
  [Completar: qué partes cambiaron o rehicieron ustedes mismos.]
- **Diagramas.** Se dibujaron en **Miro**. de Miro".]
- **Gabriel.** [Completar: qué consultó, a qué herramienta y qué hizo con la respuesta, o "Gabriel no utilizó herramientas de IA".]

## Trabajo colaborativo

- Rama principal `main` y una rama por integrante: `modelo-asociado` (Eduardo) y `modelo-movimiento` (Gabriel).
- Cada integrante abrió su solicitud de incorporación y el otro la revisó antes de integrarla. [Completar: números o enlaces de las dos PR.]
- El conflicto de fusión que provocamos y resolvimos está documentado en [CONFLICTO.md](CONFLICTO.md).

## Estructura del repositorio

```text
.
├─ README.md          este documento
├─ CONFLICTO.md       conflicto de fusión resuelto
├─ .gitignore         excluye carpetas de compilación
└─ diagramas/         diagrama de clases y flujos de apoyo (PNG)
```
