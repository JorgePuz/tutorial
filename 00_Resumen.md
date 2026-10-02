# Resumen General — Bases de Datos I

Documento de síntesis propia para repasar antes de un parcial o final. No es transcripción fiel de
un PDF puntual: conecta la teoría de las cinco unidades entre sí y con los TPs ya resueltos, y
agrega interpretación propia (marcada como tal) para facilitar el estudio.

## Tabla de contenidos

- [Unidad 1 — Introducción a las Bases de Datos](#unidad-1--introducción-a-las-bases-de-datos)
  - [Dato, información y sistema de información](#dato-información-y-sistema-de-información)
  - [¿Qué es una base de datos y un SGBD?](#qué-es-una-base-de-datos-y-un-sgbd)
  - [Evolución histórica de los modelos de datos](#evolución-histórica-de-los-modelos-de-datos)
  - [Ventajas de una BD frente a archivos planos](#ventajas-de-una-bd-frente-a-archivos-planos)
  - [SQL vs. NoSQL](#sql-vs-nosql)
- [Unidad 2 — Modelo Conceptual (MER/DER)](#unidad-2--modelo-conceptual-merder)
  - [Entidades, atributos y relaciones](#entidades-atributos-y-relaciones)
  - [Cardinalidades](#cardinalidades)
  - [Del MER al DER, y el DER en MySQL Workbench](#del-mer-al-der-y-el-der-en-mysql-workbench)
- [Unidad 3 — Modelo Relacional](#unidad-3--modelo-relacional)
  - [Dominio, relación, tupla y atributo](#dominio-relación-tupla-y-atributo)
  - [Claves: candidata, primaria, foránea, natural vs. sustituta](#claves-candidata-primaria-foránea-natural-vs-sustituta)
  - [Restricciones de integridad](#restricciones-de-integridad)
  - [Acciones referenciales (ON DELETE / ON UPDATE)](#acciones-referenciales-on-delete--on-update)
  - [Tipos de datos](#tipos-de-datos)
  - [Del modelo conceptual al relacional](#del-modelo-conceptual-al-relacional)
- [Unidad 4 — Normalización y Desnormalización](#unidad-4--normalización-y-desnormalización)
  - [Por qué normalizar](#por-qué-normalizar)
  - [1FN, 2FN y 3FN paso a paso](#1fn-2fn-y-3fn-paso-a-paso)
  - [Dependencias funcionales, parciales y transitivas](#dependencias-funcionales-parciales-y-transitivas)
  - [Desnormalización: cuándo y por qué](#desnormalización-cuándo-y-por-qué)
- [Unidad 5 — SQL](#unidad-5--sql)
  - [Introducción a SQL y sublenguajes](#introducción-a-sql-y-sublenguajes)
  - [DDL — Definición de datos](#ddl--definición-de-datos)
  - [DCL y TCL — Permisos y transacciones](#dcl-y-tcl--permisos-y-transacciones)
  - [DML y DQL — Manipulación y consulta](#dml-y-dql--manipulación-y-consulta)
  - [Funciones (agregación, texto, fecha, numéricas)](#funciones-agregación-texto-fecha-numéricas)
  - [JOINS](#joins)
  - [Subconsultas](#subconsultas)
  - [Vistas](#vistas)
- [Errores comunes / puntos de examen](#errores-comunes--puntos-de-examen)

## Unidad 1 — Introducción a las Bases de Datos

### Dato, información y sistema de información

- **Dato**: representación simbólica sin significado propio ("35", "Ana", "2025-04-27").
- **Información**: datos procesados, organizados y contextualizados, con valor para tomar
  decisiones ("Ana tiene 25 años").
- **Sistema de Información (SI)**: conjunto organizado de personas, datos, procesos y tecnología
  que recolecta, procesa, almacena y distribuye información. Sigue el ciclo *Entrada →
  Procesamiento → Salida → Retroalimentación*.
- **Base de Datos (BD)**: colección organizada de datos estructurados, almacenados y gestionados
  electrónicamente, con persistencia, integridad, seguridad, independencia de datos y soporte de
  concurrencia.

> **Interpretación propia**: en el resto de la cursada, "sistema de información" es el paraguas
> (incluye personas y procesos) y "base de datos" es solo la pieza de almacenamiento estructurado
> dentro de ese sistema. Un error típico de examen es tratarlos como sinónimos.

### ¿Qué es una base de datos y un SGBD?

Un **SGBD** (Sistema Gestor de Bases de Datos, DBMS en inglés) es el software intermediario entre
usuarios/aplicaciones y los datos almacenados: permite crear, mantener y consultar la base de
forma segura y eficiente. Funciones clave: almacenamiento y recuperación, integridad, seguridad,
gestión de transacciones y control de concurrencia.

Motores relacionales de referencia: MySQL, PostgreSQL, Oracle, SQL Server, SQLite.

### Evolución histórica de los modelos de datos

| Época | Modelo | Idea central | Limitación principal |
|---|---|---|---|
| 1960-1965 | Archivos planos | Texto plano, sin estructura relacional | Redundancia alta, mantenimiento difícil |
| 1966-1970 | Jerárquico (IBM IMS) | Árbol, cada hijo tiene un único padre | No modela bien M:N |
| 1969-1973 | Red (CODASYL) | Grafo con punteros, un hijo puede tener varios padres | Navegación procedural compleja |
| 1970 | **Relacional** (Codd) | Tablas basadas en teoría de conjuntos | Costo de JOINs en tablas grandes |
| 1990s+ | Orientado a objetos / NoSQL | Objetos, documentos, grafos, vectores | Menor estandarización, consistencia eventual |

El **modelo relacional** fue propuesto por **Edgar F. Codd en 1970** ("A Relational Model of Data
for Large Shared Data Banks", IBM). Su fundamento matemático es la teoría de conjuntos: una tabla
es un conjunto de tuplas, y las operaciones de consulta (selección, proyección, unión,
intersección, producto cartesiano) se corresponden con operaciones de conjuntos.

| Teoría de conjuntos | Modelo relacional / SQL |
|---|---|
| Conjunto | Relación (tabla) |
| Elemento del conjunto | Tupla (fila) |
| Atributo | Columna (campo) |
| Producto cartesiano | JOIN |
| Proyección | `SELECT columna1, columna2...` |
| Selección | `WHERE` |
| Unión / Intersección / Diferencia | `UNION` / `INTERSECT` / `EXCEPT` (`MINUS`) |

### Ventajas de una BD frente a archivos planos

| Característica | Archivos planos | Base de datos relacional |
|---|---|---|
| Estructura | Simple, sin relaciones | Compleja, organizada en tablas relacionadas |
| Acceso | Secuencial o aleatorio | Aleatorio, vía consultas declarativas |
| Redundancia | Alta | Baja, gracias a la normalización |
| Integridad | Depende de la aplicación | Garantizada por restricciones del SGBD |
| Seguridad | Depende del sistema operativo | Gestionada por el propio SGBD (usuarios, permisos) |

### SQL vs. NoSQL

| Criterio | SQL (relacional) | NoSQL |
|---|---|---|
| Esquema | Rígido, predefinido | Flexible / dinámico |
| Integridad referencial | Garantizada | No garantizada nativamente |
| Transacciones | ACID | Generalmente consistencia eventual |
| Escalamiento | Vertical (más recursos en un servidor) | Horizontal (más servidores) |
| Casos de uso típicos | Sistemas financieros, ERP, transaccionales (OLTP) | Redes sociales, IoT, Big Data |
| Ejemplos | MySQL, PostgreSQL, Oracle | MongoDB, Cassandra, Neo4j |

> **Interpretación propia**: la materia se centra en el modelo relacional (MySQL), pero conviene
> tener este cuadro fresco porque suele aparecer como pregunta de "panorama general" al inicio de
> un examen.

## Unidad 2 — Modelo Conceptual (MER/DER)

### Entidades, atributos y relaciones

- **Entidad**: objeto del mundo real con existencia independiente e identificable de forma única.
  Físicas (persona, producto) o conceptuales (materia, departamento).
- **Atributo**: propiedad que describe a una entidad. Se clasifican en dos ejes independientes:
  - **Por estructura**: simple (DNI, edad — no se descompone) vs. compuesto (nombre completo =
    nombre + apellido).
  - **Por cardinalidad**: monovaluado (una fecha de nacimiento) vs. multivaluado (varios teléfonos
    de contacto).
- **Relación**: asociación entre dos o más entidades (un cliente "realiza" pedidos).

### Cardinalidades

| Cardinalidad | Descripción | Ejemplo |
|---|---|---|
| 1:1 (uno a uno) | Una instancia de A se relaciona con una única instancia de B | Persona ↔ DNI |
| 1:N (uno a muchos) | Una instancia de A se relaciona con varias de B | Cliente realiza muchos pedidos |
| N:M (muchos a muchos) | Varias instancias de A con varias de B | Estudiante ↔ Curso |

> **Interpretación propia**: la cardinalidad del modelo conceptual determina directamente cómo se
> implementa la relación en el modelo relacional (Unidad 3): 1:N se resuelve con una FK en el
> lado "muchos"; N:M requiere una tabla intermedia. Pensar la cardinalidad temprano evita rediseñar
> el esquema físico más adelante.

### Del MER al DER, y el DER en MySQL Workbench

- **MER (Modelo Entidad-Relación)**: modelo teórico y abstracto, independiente de la tecnología.
- **DER (Diagrama Entidad-Relación)**: su representación gráfica. Simbología clásica: rectángulos
  (entidades), elipses (atributos), rombos (relaciones), líneas (conexiones).
- En MySQL Workbench, el DER se genera desde una base de datos ya existente con
  **Database → Reverse Engineer** (Ctrl+R): se conecta al SGBD, se elige el esquema y se importan
  las tablas, generando el diagrama automáticamente a partir de las claves primarias y foráneas ya
  definidas. Se guarda como archivo `.mwb` con **File → Save Model**.

Las **restricciones de integridad referencial** (claves primarias y foráneas) ya se introducen
conceptualmente acá, pero se formalizan en la Unidad 3:

```sql
FOREIGN KEY (ClienteID) REFERENCES Clientes(ID)
```

## Unidad 3 — Modelo Relacional

### Dominio, relación, tupla y atributo

Formalmente, un **dominio** $D$ es un conjunto de valores atómicos homogéneos. Una **relación** $R$
sobre dominios $D_1, D_2, \dots, D_n$ es un subconjunto del producto cartesiano de esos dominios:

$$R \subseteq D_1 \times D_2 \times D_3 \times \dots \times D_n$$

Cada elemento de $R$ es una **tupla** (fila). El **grado** de la relación es su número de atributos
(columnas); la **cardinalidad** es su número de tuplas en un momento dado (no confundir con la
cardinalidad de una relación entre entidades de la Unidad 2, que usa la misma palabra para un
concepto distinto).

Ejemplo (adaptado de las fuentes): la entidad PRODUCTO se modela como

```text
PRODUCTO(id_producto, nombre_producto, precio, categoria)
```

y una tupla concreta sería `(1, "Laptop Dell XPS", 1200.00, "Electrónicos")`.

Propiedades de una relación: nombre único, atributos con nombre único, cada celda con un único
valor atómico, y sin tuplas duplicadas (no hay orden ni entre tuplas ni, en teoría, entre
atributos).

### Claves: candidata, primaria, foránea, natural vs. sustituta

- **Clave candidata**: cualquier atributo (o conjunto mínimo de atributos) que identifica de forma
  única cada tupla. Cumple unicidad y minimalidad.
- **Clave primaria (PK)**: la clave candidata elegida como identificador principal. Debe ser única,
  no nula, mínima y estable en el tiempo.
- **Clave alternativa**: las demás claves candidatas no elegidas como PK.
- **Clave foránea (FK)**: atributo (o conjunto) en una tabla que referencia la PK de otra tabla,
  implementando la relación entre entidades.

Jerarquía (interpretación propia, síntesis de las fuentes): **Atributos** ⊇ **Claves candidatas**
⊇ **{Clave primaria}**, con el resto de las candidatas como **claves alternativas**.

| | Clave natural | Clave sustituta (artificial) |
|---|---|---|
| Origen | Atributo inherente a la entidad (DNI, ISBN) | Generada por el sistema (ID autoincremental, UUID) |
| Significado para el usuario | Sí | No |
| Estabilidad | Puede cambiar o ser compuesta | Nunca cambia |
| Uso típico | Cuando el dominio ya define un identificador natural único | Cuando no existe identificador natural simple o se prioriza rendimiento |

Formalmente, para una relación $R$ con esquema $(A_1, \dots, A_n)$, un conjunto de atributos
$K = \{A_{i1}, \dots, A_{ik}\}$ es clave si:

- **Unicidad**: no existen $t_1 \neq t_2 \in R$ con $t_1[K] = t_2[K]$.
- **Minimalidad**: ningún subconjunto propio $K' \subset K$ cumple ya la unicidad.

### Restricciones de integridad

| Nivel | Garantiza | Mecanismos típicos |
|---|---|---|
| **Integridad de dominio** | Que cada valor de una columna pertenezca al conjunto de valores válidos | Tipo de dato, `CHECK`, `DEFAULT`, `NOT NULL` |
| **Integridad de entidad** | Que cada fila sea única e identificable | `PRIMARY KEY` (única, no nula) |
| **Integridad referencial** | Que las relaciones entre tablas sean siempre coherentes (sin huérfanos) | `FOREIGN KEY` + acciones `ON DELETE`/`ON UPDATE` |

Formalmente, la integridad referencial de una FK que en $R_1$ referencia la PK de $R_2$ se expresa:

$$\forall t_1 \in R_1: (t_1[FK] \neq NULL) \rightarrow (\exists t_2 \in R_2: t_1[FK] = t_2[PK])$$

### Acciones referenciales (ON DELETE / ON UPDATE)

| Acción | Efecto ante DELETE/UPDATE del padre |
|---|---|
| `CASCADE` | Propaga el cambio: elimina/actualiza también los hijos |
| `SET NULL` | La FK del hijo pasa a NULL (la columna debe admitirlo) |
| `SET DEFAULT` | La FK del hijo pasa a su valor por defecto (MySQL lo acepta en sintaxis pero **no lo aplica realmente**: se comporta como `RESTRICT`) |
| `RESTRICT` / `NO ACTION` | Bloquea la operación si hay hijos relacionados (comportamiento por defecto en MySQL) |

```sql
CREATE TABLE Pedidos (
  pedido_id INT PRIMARY KEY,
  cliente_id INT,
  CONSTRAINT fk_cliente FOREIGN KEY (cliente_id)
  REFERENCES Clientes(cliente_id)
  ON DELETE SET NULL
  ON UPDATE CASCADE
);
```

### Tipos de datos

| Familia | Tipos | Notas |
|---|---|---|
| Alfanuméricos | `CHAR(n)`, `VARCHAR(n)`, `TEXT` | `CHAR` longitud fija (rellena con espacios), `VARCHAR` variable, `TEXT` para contenido extenso |
| Numéricos enteros | `TINYINT`, `SMALLINT`, `INT`, `BIGINT` | Elegir el tamaño mínimo necesario |
| Numéricos decimales | `DECIMAL(p,s)`, `FLOAT`/`REAL`/`DOUBLE` | `DECIMAL` = precisión exacta (dinero); `FLOAT` = aproximado, no usar para cálculos financieros |
| Temporales | `DATE`, `TIME`, `DATETIME`/`TIMESTAMP` | `TIMESTAMP` habitual para auditoría (`fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP`) |
| Otros | `BOOLEAN`, `BLOB` (con variantes `TINYBLOB`…`LONGBLOB`), `JSON` | `BOOLEAN` se almacena internamente como `TINYINT` en MySQL |

### Del modelo conceptual al relacional

Proceso de transformación (resumen de Unidad 2 → Unidad 3):

1. **Entidad → Tabla**: el nombre de la entidad pasa a ser el nombre de la tabla; cada atributo, una
   columna, conservando su dominio.
2. **Identificador → Clave primaria**: el atributo identificador de la entidad se convierte en PK.
3. **Relación 1:N → Clave foránea**: la PK del lado "1" se agrega como FK en la tabla del lado "N".
4. **Relación N:M → Tabla intermedia**: se crea una tabla puente con las FK de ambas entidades,
   cuya combinación forma la PK compuesta (patrón que reaparece en la Unidad 5 con la tabla
   `Inscripciones` de Estudiantes↔Materias, y en el TP2 de Unidad 5 con `Matriculas`).
5. **Atributos compuestos**: se descomponen en columnas simples (nombre completo → nombre +
   apellido).
6. **Atributos multivalorados**: requieren tabla separada (no pueden ser una columna con varios
   valores — esto conecta directamente con la 1FN de la Unidad 4).

## Unidad 4 — Normalización y Desnormalización

### Por qué normalizar

La normalización es un proceso sistemático (Codd, 1972) para descomponer tablas y eliminar
redundancia y anomalías de **inserción**, **actualización** y **eliminación**.

| Ventajas | Desventajas |
|---|---|
| Menos redundancia, menos espacio | Consultas más complejas (más JOINs) |
| Mejor integridad y consistencia | Puede afectar rendimiento en bases muy grandes |
| Mantenimiento más simple y seguro | Más tablas para administrar |

### 1FN, 2FN y 3FN paso a paso

| Forma normal | Requisito | Elimina |
|---|---|---|
| **1FN** | Valores atómicos en cada celda; sin grupos repetitivos; cada registro único | Datos compuestos y grupos repetidos |
| **2FN** | Cumple 1FN + todo atributo no clave depende de **toda** la PK (no solo de una parte) | Dependencias parciales (solo aplica si la PK es compuesta) |
| **3FN** | Cumple 2FN + ningún atributo no clave depende de otro atributo no clave | Dependencias transitivas |

Ejemplo integrador (adaptado del apunte de Unidad 4, caso de una distribuidora): partiendo de una
tabla `PEDIDOS_NO_NORMALIZADA` con datos de cliente, factura y artículos todos mezclados en una
sola tabla:

**1FN** — se separa cada ítem de factura en su propia fila y se atomizan los datos compuestos
(cliente → nombre + apellido; dirección → calle + número + código postal + localidad). La PK queda
como `(numero_factura, item_factura)`.

**2FN** — como la PK es compuesta, se detectan dependencias parciales: `fecha` y los datos del
cliente dependen solo de `numero_factura` (no de `item_factura`); `artículo` y `precio` dependen
solo de `código_artículo`. Se separan en tablas `FACTURAS`, `DETALLE_FACTURA`, `CLIENTES` y
`ARTICULOS`.

**3FN** — en la tabla `CLIENTES` resultante, `localidad` depende de `codigo_postal`, que a su vez
depende de la PK `número_cliente`: es una dependencia transitiva. Se separa en una tabla
`LOCALIDADES(codigo_postal, localidad)` aparte.

```sql
-- Esquema final en 3FN (resumen)
FACTURAS(numero_factura PK, fecha, numero_cliente FK)
DETALLE_FACTURA(numero_factura PK FK, item_factura PK, codigo_articulo FK, unidades)
CLIENTES(numero_cliente PK, apellido, nombre, calle, numero_calle, codigo_postal FK)
LOCALIDADES(codigo_postal PK, localidad)
ARTICULOS(codigo_articulo PK, articulo, precio)
```

> **Interpretación propia**: en la práctica conviene chequear las tres formas normales en orden
> con una pregunta simple por cada una:
>
> - **1FN**: ¿cada celda tiene un único valor? ¿ninguna columna guarda una lista?
> - **2FN**: si la PK es compuesta, ¿hay algún atributo que "sepa" su valor con solo una parte de
>   la PK?
> - **3FN**: ¿hay algún atributo no clave que en realidad dependa de *otro* atributo no clave, y no
>   directamente de la PK?

### Dependencias funcionales, parciales y transitivas

Una **dependencia funcional** $A \rightarrow B$ significa que el valor de $A$ determina de forma
única el valor de $B$.

- **Dependencia parcial**: un atributo no clave depende solo de una parte de una PK compuesta (viola
  2FN). Ejemplo: en `(numero_factura, item_factura)` como PK, `fecha` depende solo de
  `numero_factura`.
- **Dependencia transitiva**: $A \rightarrow B$ y $B \rightarrow C$, por lo que $A \rightarrow C$ de
  forma indirecta, sin que $C$ dependa directamente de la PK (viola 3FN). Ejemplo:
  `numero_cliente → codigo_postal → localidad`.

### Desnormalización: cuándo y por qué

La desnormalización reintroduce redundancia **de forma deliberada y controlada** para mejorar el
rendimiento de lectura, principalmente en sistemas analíticos (OLAP, reportes, data warehouses).

| Criterio | Normalización | Desnormalización |
|---|---|---|
| Rendimiento de lectura | Menor (requiere JOINs) | Mayor (acceso directo) |
| Rendimiento de escritura | Mayor (menos actualizaciones) | Menor (hay que actualizar copias) |
| Consistencia de datos | Alta | Menor si está mal gestionada |
| Uso de almacenamiento | Eficiente | Mayor (redundancia) |
| Orientado a | Transacciones (OLTP) | Consultas/reportes (OLAP) |

Cuándo conviene: JOINs frecuentes que afectan el rendimiento, lectura intensiva con escritura poco
frecuente, sistemas de reportes, agregaciones repetitivas.

Técnicas comunes: duplicar atributos (ej. copiar `provincia` en `Clientes` para evitar el JOIN con
`Localidades`), campos precalculados (`total_pedidos`), tablas combinadas, tablas de resumen/
históricas.

```sql
-- Normalizado: requiere JOIN
SELECT c.nombre, l.provincia
FROM Clientes c
JOIN Localidades l ON c.id_localidad = l.id;

-- Desnormalizado: provincia duplicada en Clientes
SELECT nombre, provincia
FROM Clientes;
```

> **Frase de las fuentes que vale la pena recordar**: *"La normalización es ciencia. La
> desnormalización es arte."* Siempre debe justificarse con métricas y documentarse, nunca aplicarse
> como atajo improvisado.

## Unidad 5 — SQL

### Introducción a SQL y sublenguajes

SQL (Structured Query Language) es el lenguaje **declarativo** estándar para bases de datos
relacionales: se especifica *qué* se quiere obtener, no *cómo* obtenerlo (eso lo decide el motor,
optimizando el plan de ejecución). Nació como SEQUEL en IBM (1974-75, proyecto System R) y se
renombró a SQL por un conflicto de marca registrada.

| Sublenguaje | Sigla | Instrucciones | Función |
|---|---|---|---|
| Definición de datos | **DDL** | `CREATE`, `ALTER`, `DROP`, `TRUNCATE` | Define/modifica la estructura |
| Manipulación de datos | **DML** | `INSERT`, `UPDATE`, `DELETE` | Modifica el contenido de las tablas |
| Consulta de datos | **DQL** | `SELECT` | Recupera información, sin modificarla |
| Control de datos | **DCL** | `GRANT`, `REVOKE` | Gestiona permisos y accesos |
| Control de transacciones | **TCL** | `COMMIT`, `ROLLBACK`, `SAVEPOINT` | Agrupa operaciones como unidad atómica |

> **Interpretación propia**: DQL suele agruparse informalmente dentro del DML, pero para examen
> conviene distinguirlos: el DML **cambia** datos (y es reversible con `ROLLBACK` dentro de una
> transacción), el DQL solo **lee** datos (y por eso un `SELECT` nunca es riesgoso). El DDL, en
> cambio, genera **commit implícito** en MySQL: no se puede revertir con `ROLLBACK`.

### DDL — Definición de datos

**Crear base y tabla:**

```sql
CREATE DATABASE IF NOT EXISTS institucion
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_general_ci;

USE institucion;

CREATE TABLE Estudiantes (
  Legajo INT AUTO_INCREMENT PRIMARY KEY,
  Nombre VARCHAR(50) NOT NULL,
  DNI VARCHAR(15) UNIQUE,
  Activo BOOLEAN NOT NULL DEFAULT TRUE,
  IDSede INT,
  FOREIGN KEY (IDSede) REFERENCES Sedes(IDSede)
    ON DELETE SET NULL
    ON UPDATE CASCADE
) ENGINE=InnoDB;
```

**Restricciones (constraints) más usadas:**

| Restricción | Qué garantiza |
|---|---|
| `PRIMARY KEY` | Identifica de forma única cada fila; no admite NULL; una sola por tabla (puede ser compuesta) |
| `FOREIGN KEY` | Referencia a la PK de otra tabla; requiere motor `InnoDB` para validarse realmente |
| `UNIQUE` | Valores no repetidos; a diferencia de PK, puede haber varias por tabla y sí admite NULL |
| `NOT NULL` | Obliga a que el campo tenga valor |
| `DEFAULT` | Valor asignado si no se especifica ninguno |
| `CHECK` | Valida una condición lógica (en MySQL, ignorada silenciosamente antes de 8.0.16) |
| `AUTO_INCREMENT` | Genera un consecutivo automático (no reutiliza huecos tras un DELETE) |

**Modificar y eliminar estructura:**

```sql
ALTER TABLE Estudiantes ADD COLUMN Email VARCHAR(100);
ALTER TABLE Estudiantes MODIFY COLUMN Email VARCHAR(150) NOT NULL;
ALTER TABLE Estudiantes CHANGE COLUMN Email Correo VARCHAR(150);
ALTER TABLE Estudiantes DROP COLUMN Correo;
ALTER TABLE Estudiantes RENAME TO Alumnos;

DROP TABLE IF EXISTS Alumnos;
DROP DATABASE IF EXISTS institucion;
TRUNCATE TABLE Alumnos;
```

`DROP TABLE`/`DROP DATABASE` son irreversibles y `TRUNCATE` no puede revertirse con `ROLLBACK` (a
diferencia de `DELETE`).

### DCL y TCL — Permisos y transacciones

**DCL** (Data Control Language) gestiona quién puede hacer qué sobre la base:

```sql
-- otorgar permisos de lectura y escritura sobre una tabla a un usuario
GRANT SELECT, INSERT, UPDATE ON institucion.Estudiantes TO 'lector'@'localhost';

-- otorgar todos los privilegios sobre toda la base
GRANT ALL PRIVILEGES ON institucion.* TO 'admin'@'localhost';

-- quitar un permiso previamente otorgado
REVOKE INSERT ON institucion.Estudiantes FROM 'lector'@'localhost';
```

**TCL** (Transaction Control Language) agrupa una o más instrucciones DML como una unidad atómica
(todo se aplica o nada se aplica):

```sql
START TRANSACTION;

UPDATE Cuentas SET saldo = saldo - 500 WHERE id = 1;
UPDATE Cuentas SET saldo = saldo + 500 WHERE id = 2;

SAVEPOINT antes_de_comision;

UPDATE Cuentas SET saldo = saldo - 10 WHERE id = 1;  -- comisión

-- si algo sale mal, se puede volver solo hasta el savepoint...
ROLLBACK TO antes_de_comision;

-- ...o confirmar todo lo hecho hasta ahora de forma definitiva
COMMIT;
```

- `COMMIT`: hace permanentes todos los cambios de la transacción; a partir de ahí ya no se puede
  hacer `ROLLBACK`.
- `ROLLBACK`: deshace los cambios de la transacción (o hasta un `SAVEPOINT` puntual si se indica).
- `SAVEPOINT`: marca un punto intermedio dentro de la transacción al cual se puede volver sin
  deshacer todo lo anterior.

> **Interpretación propia**: DCL y TCL casi no aparecen en los TPs resueltos (que se centran en
> DDL/DML/DQL), pero son candidatos típicos de pregunta teórica corta ("¿qué diferencia hay entre
> DML y TCL?", "¿para qué sirve un SAVEPOINT?").

### DML y DQL — Manipulación y consulta

**INSERT:**

```sql
INSERT INTO Alumnos (nombre, apellido, edad, id_carrera)
VALUES ('Ana', 'Gómez', 20, 1);

-- inserción múltiple, más eficiente que varios INSERT sueltos
INSERT INTO Alumnos (nombre, apellido, edad, id_carrera) VALUES
  ('Juan', 'Pérez', 21, 2),
  ('Sol', 'Martínez', 22, NULL);
```

**UPDATE / DELETE** (siempre con `WHERE`, salvo intención explícita de afectar toda la tabla):

```sql
UPDATE Alumnos SET nombre = 'Juan Jose' WHERE id = 101;

DELETE FROM Alumnos WHERE id = 103;
```

Ejemplo real de las resoluciones de TP (Unidad 5, TP1): la tabla base `GestionAcademica` con
`Carreras`, `Alumnos` y `Asignaturas` se construye enteramente con estas tres instrucciones, y se
recomienda probar los `DELETE` dentro de una transacción para poder deshacerlos sin pérdida de
datos:

```sql
START TRANSACTION;
DELETE FROM Alumnos WHERE id = 103;
SELECT * FROM Alumnos;  -- se ve el borrado dentro de la transacción
ROLLBACK;                -- deshace el DELETE
```

**SELECT con filtros:**

```sql
SELECT Nombre, Apellido FROM Estudiantes
WHERE IDSede = 1 AND Activo = TRUE;

SELECT Nombre FROM Estudiantes WHERE IDSede IS NULL;          -- nunca "= NULL"
SELECT Nombre FROM Estudiantes WHERE IDSede IN (1, 2, 4);
SELECT Nombre FROM Estudiantes WHERE Nombre LIKE 'A%';
SELECT Nombre FROM Estudiantes
ORDER BY Apellido ASC, Nombre ASC
LIMIT 5;

SELECT DISTINCT IDSede FROM Estudiantes;
```

### Funciones (agregación, texto, fecha, numéricas)

**Agregación** (resumen de un conjunto de filas en un único valor por grupo):

```sql
SELECT IDSede, COUNT(*) AS Cantidad
FROM Estudiantes
GROUP BY IDSede
HAVING COUNT(*) > 5;
```

`WHERE` filtra **antes** de agrupar; `HAVING` filtra **después** (sobre el resultado ya agregado).
`COUNT(*)` cuenta todas las filas (incluso con NULL en otras columnas); `COUNT(columna)` ignora los
NULL de esa columna; `COUNT(DISTINCT columna)` cuenta valores únicos no nulos. El resto de las
funciones de agregación (`SUM`, `AVG`, `MAX`, `MIN`) ignoran los NULL.

**Texto, fecha y numéricas** (tabla comparativa de las más usadas):

| Categoría | Función | Ejemplo | Resultado |
|---|---|---|---|
| Texto | `CONCAT` | `CONCAT(Nombre, ' ', Apellido)` | Une cadenas |
| Texto | `UPPER` / `LOWER` | `UPPER(Nombre)` | Mayúsculas/minúsculas |
| Texto | `SUBSTRING` | `SUBSTRING(Nombre, 1, 3)` | Extrae desde posición 1 (no 0) |
| Fecha | `TIMESTAMPDIFF` | `TIMESTAMPDIFF(YEAR, FechaNac, CURDATE())` | Edad en años |
| Fecha | `DATE_FORMAT` | `DATE_FORMAT(f, '%d/%m/%Y')` | Formato de presentación |
| Numérica | `ROUND` | `ROUND(AVG(Monto), 2)` | Redondeo a 2 decimales |
| NULL | `IFNULL` / `COALESCE` | `COALESCE(IDSede, -1)` | Reemplaza NULL por un valor por defecto |

`COALESCE` es estándar SQL y admite $n$ argumentos; `IFNULL` es propio de MySQL y admite
exactamente dos.

### JOINS

Un JOIN combina filas de dos o más tablas relacionadas, generalmente por FK = PK. Es una operación
de lectura (DQL): no modifica las tablas.

| Tipo | Devuelve | Cuándo usarlo |
|---|---|---|
| `INNER JOIN` | Solo filas con correspondencia en ambas tablas | Cuando ambas partes de la relación son obligatorias |
| `LEFT JOIN` | Todas las filas de la izquierda, con o sin correspondencia (NULL si no hay) | Para no perder registros del lado "principal" (ej. materias sin inscriptos) |
| `RIGHT JOIN` | Todas las filas de la derecha, con o sin correspondencia | Equivalente a invertir el orden en un LEFT JOIN |
| `CROSS JOIN` | Producto cartesiano completo (todas las combinaciones) | Generar combinaciones exhaustivas a propósito |
| Self JOIN | Una tabla contra sí misma, con alias obligatorios | Comparar filas de la misma tabla entre sí |

MySQL **no tiene `FULL OUTER JOIN`**: se simula con `LEFT JOIN UNION RIGHT JOIN`.

```sql
SELECT e.Nombre, s.Nombre AS Sede
FROM Estudiantes e
INNER JOIN Sedes s ON e.IDSede = s.IDSede;

SELECT e.Nombre, s.Nombre AS Sede
FROM Estudiantes e
LEFT JOIN Sedes s ON e.IDSede = s.IDSede
WHERE s.IDSede IS NULL;          -- estudiantes SIN sede asignada
```

Ejemplo integrador (LEFT JOIN + agregación), muy usado en TPs para no perder filas sin relación:

```sql
SELECT m.NombreMateria, COUNT(i.LegajoEstudiante) AS CantidadInscriptos
FROM Materias m
LEFT JOIN Inscripciones i ON i.IDMateria = m.IDMateria
GROUP BY m.IDMateria, m.NombreMateria;
```

Del TP2 de Unidad 5 (resolución real), un JOIN con tres tablas para traer alumno, carrera y
duración solo cuando hay carrera asignada:

```sql
SELECT CONCAT(a.nombre, ' ', a.apellido) AS nombre_completo, c.duracion
FROM Alumnos a
INNER JOIN Carreras c ON a.id_carrera = c.id;
```

Sintaxis moderna recomendada: `JOIN ... ON` (o `USING (columna)` cuando el nombre coincide en
ambas tablas), en vez de la sintaxis implícita antigua con coma + `WHERE`, que es más propensa a
generar un producto cartesiano accidental si se olvida la condición.

### Subconsultas

Una subconsulta es un `SELECT` completo anidado dentro de otra instrucción SQL (`WHERE`, `SELECT`,
`FROM`, `HAVING`, incluso dentro de `INSERT`/`UPDATE`/`DELETE`).

| Tipo | Devuelve | Operadores típicos |
|---|---|---|
| Escalar | Un único valor (1 fila, 1 columna) | `=`, `>`, `<` |
| Multi-fila | Varios valores | `IN`, `ANY`/`SOME`, `ALL` |
| Correlacionada | Depende de una columna de la consulta externa; se reevalúa por cada fila externa | Suele combinarse con `EXISTS` |

```sql
-- Escalar: estudiantes por encima del promedio de edad
SELECT Nombre FROM Estudiantes
WHERE TIMESTAMPDIFF(YEAR, FechaNacimiento, CURDATE()) >
  (SELECT AVG(TIMESTAMPDIFF(YEAR, FechaNacimiento, CURDATE())) FROM Estudiantes);

-- Multi-fila con IN
SELECT Nombre FROM Estudiantes
WHERE Legajo IN (SELECT LegajoEstudiante FROM Inscripciones WHERE IDMateria = 201);

-- Correlacionada + EXISTS: preferible a NOT IN cuando puede haber NULL
SELECT e.Nombre FROM Estudiantes e
WHERE NOT EXISTS (
  SELECT 1 FROM Inscripciones i WHERE i.LegajoEstudiante = e.Legajo
);
```

Del TP4 de Unidad 5 (resolución real): carreras sin ninguna asignatura cargada.

```sql
SELECT id, nombre_carrera
FROM Carreras
WHERE id NOT IN (SELECT DISTINCT id_carrera FROM Asignaturas WHERE id_carrera IS NOT NULL);
```

**Subconsulta vs. JOIN**: usar JOIN cuando se necesitan columnas de ambas tablas en el resultado;
usar subconsulta cuando solo se necesita filtrar o calcular un valor a partir de otra tabla; usar
`EXISTS`/`NOT EXISTS` cuando lo único que importa es si existe o no una relación.

### Vistas

Una **vista** (`VIEW`) es una consulta almacenada que se comporta como tabla virtual: no guarda
datos físicamente, se recalcula en cada acceso.

```sql
CREATE VIEW vista_estudiantes_activos AS
SELECT Legajo, Nombre, Apellido, IDSede
FROM Estudiantes
WHERE Activo = TRUE;

CREATE OR REPLACE VIEW vista_estudiantes_activos AS
SELECT Legajo, Nombre, Apellido, IDSede, DNI
FROM Estudiantes
WHERE Activo = TRUE;

DROP VIEW IF EXISTS vista_estudiantes_activos;
```

| | Vista simple | Vista compleja |
|---|---|---|
| Tablas de origen | Una sola | Dos o más (con JOIN) |
| Agregación / `GROUP BY` / `DISTINCT` | No | Sí |
| ¿Actualizable (INSERT/UPDATE/DELETE)? | Generalmente sí | Generalmente no (solo lectura) |

`WITH CHECK OPTION` evita que una modificación a través de una vista con `WHERE` genere una fila
que "desaparezca" de esa misma vista:

```sql
CREATE VIEW vista_estudiantes_sede1 AS
SELECT Legajo, Nombre, IDSede
FROM Estudiantes
WHERE IDSede = 1
WITH CHECK OPTION;
```

MySQL **no tiene vistas materializadas** nativas (sí PostgreSQL/Oracle): para lograr un efecto
similar se refresca una tabla real vía evento programado o procedimiento almacenado.

Ejemplo real del TP3 de Unidad 5, vista con `LEFT JOIN` + `COALESCE` para que las carreras sin
asignaturas figuren con `0` en vez de desaparecer:

```sql
CREATE VIEW vista_creditos_por_alumno AS
SELECT al.id, al.nombre, al.apellido, COALESCE(SUM(a.creditos), 0) AS total_creditos
FROM Alumnos al
LEFT JOIN Asignaturas a ON a.id_carrera = al.id_carrera
GROUP BY al.id, al.nombre, al.apellido;
```

## Errores comunes / puntos de examen

Síntesis propia de las trampas más frecuentes vistas en las fuentes de las cinco unidades:

- **`UPDATE`/`DELETE` sin `WHERE`**: afecta a *todas* las filas de la tabla. Antes de ejecutar,
  validar siempre la condición con un `SELECT` equivalente. `TRUNCATE` es aún más drástico: vacía
  toda la tabla, reinicia el `AUTO_INCREMENT` y no puede revertirse con `ROLLBACK` (a diferencia de
  `DELETE`, que sí es reversible dentro de una transacción).
- **`= NULL` nunca es verdadero**: hay que usar `IS NULL` / `IS NOT NULL`. Esto también afecta a
  `NOT IN` con subconsultas: si la subconsulta devuelve algún NULL, `NOT IN` puede devolver cero
  filas de forma silenciosa; `NOT EXISTS` es la alternativa segura porque nunca compara valores.
- **`WHERE` no puede filtrar sobre una función de agregación**: `WHERE COUNT(*) > 5` es un error;
  hay que usar `HAVING`, que se evalúa después de `GROUP BY`.
- **INNER JOIN vs. LEFT JOIN**: INNER JOIN descarta filas sin correspondencia en ambos lados (por
  ejemplo, un estudiante sin sede asignada desaparece). LEFT JOIN conserva todas las filas de la
  tabla izquierda aunque no haya correspondencia (columnas de la derecha en NULL). Es un error
  típico usar INNER JOIN en un reporte que debería mostrar "también los que tienen cero" (ver el
  ejemplo de materias sin inscriptos).
- **Producto cartesiano accidental**: olvidar la condición `ON` en un JOIN, o la condición en el
  `WHERE` con la sintaxis implícita antigua (`FROM A, B`), no genera error pero sí un resultado
  gigantesco e incorrecto. La sintaxis `JOIN ... ON` explícita reduce este riesgo.
- **2FN vs. 3FN**: 2FN solo tiene sentido evaluarla cuando la PK es **compuesta** (si la PK es
  simple, la tabla ya cumple 2FN automáticamente). 2FN elimina dependencias de **una parte** de la
  PK; 3FN elimina dependencias entre atributos **no clave** (transitivas). Confundir "depende de
  parte de la clave" (2FN) con "depende de otro atributo no clave" (3FN) es el error más común al
  normalizar a mano.
- **PRIMARY KEY vs. UNIQUE**: una tabla tiene una sola PK (nunca NULL), pero puede tener varias
  columnas `UNIQUE` (que sí admiten NULL, incluso más de uno según la versión de MySQL).
- **FOREIGN KEY sin InnoDB**: en MySQL, las claves foráneas solo se validan de verdad con el motor
  `InnoDB`; con `MyISAM` la sintaxis se acepta pero no se aplica ninguna restricción real.
- **DDL vs. DML y `ROLLBACK`**: las instrucciones DDL (`CREATE`, `ALTER`, `DROP`, `TRUNCATE`)
  generan *commit implícito* en MySQL y no se pueden revertir. Las DML (`INSERT`, `UPDATE`,
  `DELETE`) sí pueden revertirse con `ROLLBACK` si se ejecutan dentro de una transacción explícita
  (`START TRANSACTION`).
- **Vista actualizable**: solo es posible hacer `INSERT`/`UPDATE`/`DELETE` a través de una vista si
  se basa en una sola tabla, sin agregaciones, `DISTINCT`, `GROUP BY`, `HAVING`, `UNION` ni
  subconsultas en el `SELECT`. Cualquier JOIN o función de agregación vuelve la vista de solo
  lectura.
- **Normalización vs. desnormalización no son opuestos absolutos**: la desnormalización es válida
  cuando está justificada con métricas de rendimiento y documentada (sistemas OLAP, reportes); no
  es "diseñar mal", pero tampoco es gratis: introduce riesgo de inconsistencia si no se mantienen
  sincronizadas las copias redundantes.
