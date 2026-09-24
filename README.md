# Informe de Ejercicios de Base de Datos PostgreSQL

**Autor:** Kyrylo Chvanov  

---

## 1. Creación de la Base de Datos

### Enunciado
Crear una base de datos llamada `biblioteca`.

### Sentencia SQL
```sql
CREATE DATABASE biblioteca;
```

### Salida
```text
CREATE DATABASE
```

---

## 2. Creación y Gestión de Usuarios y Roles

### Enunciado
1. Crear el usuario `admin_biblio` con permisos de administrador.
2. Crear el usuario `usuario_biblio` con permisos de lectura.
3. Crear un rol (ej. `lectores`) con permisos de consulta sobre las tablas y asignarlo a `usuario_biblio`.
4. Consultar las tablas del sistema para listar todos los usuarios/roles creados (`pg_roles`).
5. Cambiar la contraseña del usuario `usuario_biblio`.
6. Configurar permisos para asegurar que `usuario_biblio` únicamente disponga de permisos de lectura.

### Creación del administrador `admin_biblio`

#### Sentencias SQL
```sql
CREATE USER admin_biblio WITH SUPERUSER PASSWORD 'admin';
```

#### Salida
```text
CREATE ROLE
```

### Configuración de `usuario_biblio` y asignación de rol de lectura

#### Sentencias SQL
```sql
CREATE USER usuario_biblio WITH PASSWORD 'usuario';
GRANT CONNECT ON DATABASE biblioteca TO usuario_biblio;
GRANT USAGE ON SCHEMA public TO usuario_biblio;

CREATE ROLE lectores;
GRANT USAGE ON SCHEMA public TO lectores;

ALTER DEFAULT PRIVILEGES IN SCHEMA public
   GRANT SELECT ON TABLES TO lectores;

GRANT lectores TO usuario_biblio;
```

#### Salida
```text
CREATE ROLE
GRANT
GRANT
CREATE ROLE
GRANT
ALTER DEFAULT PRIVILEGES
GRANT ROLE
```

### Consulta de roles del sistema (`pg_roles`)

#### Sentencia SQL
```sql
SELECT * FROM pg_roles;
```

#### Salida
```text
           rolname           | rolsuper | rolinherit | rolcreaterole | rolcreatedb | rolcanlogin | rolreplication | rolconnlimit | rolpassword | rolvaliduntil | rolbypassrls | rolconfig |  oid
-----------------------------+----------+------------+---------------+-------------+-------------+----------------+--------------+-------------+---------------+--------------+-----------+-------
 pg_database_owner           | f        | t          | f             | f           | f           | f              |           -1 | ********    |               | f            |           |  6171
 pg_read_all_data            | f        | t          | f             | f           | f           | f              |           -1 | ********    |               | f            |           |  6181
 pg_write_all_data           | f        | t          | f             | f           | f           | f              |           -1 | ********    |               | f            |           |  6182
 pg_monitor                  | f        | t          | f             | f           | f           | f              |           -1 | ********    |               | f            |           |  3373
 pg_read_all_settings        | f        | t          | f             | f           | f           | f              |           -1 | ********    |               | f            |           |  3374
 pg_read_all_stats           | f        | t          | f             | f           | f           | f              |           -1 | ********    |               | f            |           |  3375
 pg_stat_scan_tables         | f        | t          | f             | f           | f           | f              |           -1 | ********    |               | f            |           |  3377
 pg_read_server_files        | f        | t          | f             | f           | f           | f              |           -1 | ********    |               | f            |           |  4569
 pg_write_server_files       | f        | t          | f             | f           | f           | f              |           -1 | ********    |               | f            |           |  4570
 pg_execute_server_program   | f        | t          | f             | f           | f           | f              |           -1 | ********    |               | f            |           |  4571
 pg_signal_backend           | f        | t          | f             | f           | f           | f              |           -1 | ********    |               | f            |           |  4200
 pg_checkpoint               | f        | t          | f             | f           | f           | f              |           -1 | ********    |               | f            |           |  4544
 pg_use_reserved_connections | f        | t          | f             | f           | f           | f              |           -1 | ********    |               | f            |           |  4550
 pg_create_subscription      | f        | t          | f             | f           | f           | f              |           -1 | ********    |               | f            |           |  6304
 postgres                    | t        | t          | t             | t           | t           | t              |           -1 | ********    |               | t            |           |    10
 admin_biblio                | t        | t          | f             | f           | t           | f              |           -1 | ********    |               | f            |           | 16398
 usuario_biblio              | f        | t          | f             | f           | t           | f              |           -1 | ********    |               | f            |           | 16399
 lectores                   | f        | t          | f             | f           | f           | f              |           -1 | ********    |               | f            |           | 16400
```

### Cambio de contraseña de `usuario_biblio`

#### Sentencia SQL
```sql
ALTER USER usuario_biblio WITH PASSWORD 'pass';
```

#### Salida
```text
ALTER ROLE
```

---

## 3. Creación de Tablas

### Enunciado
Crear las siguientes tablas estableciendo claves primarias y foráneas correspondientes:
* `autores` (`id_autor`, `nombre`, `nacionalidad`)
* `libros` (`id_libro`, `titulo`, `ano_publicacion`, `id_autor`)
* `prestamos` (`id_prestamo`, `id_libro`, `fecha_prestamo`, `fecha_devolucion`, `usuario_prestario`)

### Sentencias SQL
```sql
CREATE TABLE autores (
   id_autor serial PRIMARY KEY,
   nombre VARCHAR (50),
   nacionalidad VARCHAR (50)
);

CREATE TABLE libros (
   id_libro serial PRIMARY KEY,
   titulo VARCHAR (100),
   ano_publicacion int,
   id_autor serial references autores(id_autor)
);

CREATE TABLE prestamos (
   id_prestamo serial PRIMARY KEY,
   id_libro serial references libros(id_libro),
   fecha_prestamo date,
   fecha_devolucion date,
   usuario_prestario VARCHAR (100)
);
```

### Salida
```text
CREATE TABLE
CREATE TABLE
CREATE TABLE
```

---

## 4. Inserción de Datos

### Enunciado
Insertar al menos 5 autores, 8 libros y 5 préstamos de ejemplo.

### Sentencias SQL
```sql
INSERT INTO autores (nombre, nacionalidad) VALUES
  ('Gabriel García Márquez', 'Colombiana'),
  ('Isabel Allende', 'Chilena'),
  ('Jorge Luis Borges', 'Argentina'),
  ('Mario Vargas Llosa', 'Peruana'),
  ('Julio Cortázar', 'Argentina');

INSERT INTO libros (titulo, ano_publicacion, id_autor) VALUES
  ('Cien años de soledad', 1967, 1),
  ('El amor en los tiempos del cólera', 1985, 1),
  ('La casa de los espíritus', 1982, 2),
  ('Largo petalo de mar', 2019, 2),
  ('Ficciones', 1944, 3),
  ('El Aleph', 1949, 3),
  ('La ciudad y los perros', 1963, 4),
  ('Rayuela', 1963, 5);

INSERT INTO prestamos (id_libro, fecha_prestamo, fecha_devolucion, usuario_prestario) VALUES
  (1, '2026-09-01', '2026-09-15', 'Carlos Mendoza'),
  (3, '2026-09-05', '2026-09-19', 'Ana Sofía Gómez'),
  (5, '2026-09-10', NULL, 'Luis Rodríguez'),
  (8, '2026-09-12', '2026-09-22', 'Maria Fernanda López'),
  (2, '2026-09-20', NULL, 'Javier Martínez');
```

### Salida
```text
INSERT 0 5
INSERT 0 8
INSERT 0 5
```

---

## 5. Consultas Básicas

### 5.1. Listar todos los libros con su autor correspondiente

#### Sentencia SQL
```sql
SELECT *
FROM libros
NATURAL JOIN autores;
```

#### Salida
```text
 id_autor | id_libro |               titulo               | ano_publicacion |         nombre         | nacionalidad
----------+----------+-----------------------------------+-----------------+------------------------+--------------
        1 |        1 | Cien años de soledad               |            1967 | Gabriel García Márquez | Colombiana
        1 |        2 | El amor en los tiempos del cólera |            1985 | Gabriel García Márquez | Colombiana
        2 |        3 | La casa de los espíritus          |            1982 | Isabel Allende         | Chilena
        2 |        4 | Largo petalo de mar               |            2019 | Isabel Allende         | Chilena
        3 |        5 | Ficciones                         |            1944 | Jorge Luis Borges      | Argentina
        3 |        6 | El Aleph                          |            1949 | Jorge Luis Borges      | Argentina
        4 |        7 | La ciudad y los perros            |            1963 | Mario Vargas Llosa     | Peruana
        5 |        8 | Rayuela                           |            1963 | Julio Cortázar         | Argentina
(8 rows)
```

### 5.2. Mostrar préstamos que aún no tienen fecha de devolución

#### Sentencia SQL
```sql
SELECT *
FROM prestamos
WHERE fecha_devolucion IS NULL;
```

#### Salida
```text
 id_prestamo | id_libro | fecha_prestamo | fecha_devolucion | usuario_prestario
-------------+----------+----------------+------------------+-------------------
           3 |        5 | 2026-09-10     |                  | Luis Rodríguez
           5 |        2 | 2026-09-20     |                  | Javier Martínez
(2 rows)
```

### 5.3. Obtener los autores que tienen más de un libro registrado

#### Sentencia SQL
```sql
SELECT id_autor, nombre, nacionalidad
FROM autores
NATURAL JOIN libros
GROUP BY id_autor
HAVING COUNT(*) > 1;
```

#### Salida
```text
 id_autor |         nombre         | nacionalidad
----------+------------------------+--------------
        2 | Isabel Allende         | Chilena
        1 | Gabriel García Márquez | Colombiana
        3 | Jorge Luis Borges      | Argentina
(3 rows)
```

---

## 6. Consultas con Agregación

### 6.1. Calcular el número total de préstamos realizados

#### Sentencia SQL
```sql
SELECT COUNT(*)
FROM prestamos;
```

#### Salida
```text
 count
-------
     5
(1 row)
```

### 6.2. Obtener el número de libros prestados por cada usuario

#### Sentencia SQL
```sql
SELECT COUNT(*), usuario_prestario
FROM prestamos
GROUP BY usuario_prestario;
```

#### Salida
```text
 count |  usuario_prestario
-------+----------------------
     1 | Ana Sofía Gómez
     1 | Carlos Mendoza
     1 | Javier Martínez
     1 | Luis Rodríguez
     1 | Maria Fernanda López
(5 rows)
```

---

## 7. Modificación y Eliminación de Datos

### 7.1. Actualizar la fecha de devolución de un préstamo pendiente

#### Sentencia SQL
```sql
UPDATE prestamos
SET fecha_devolucion = '2026-09-16'
WHERE id_prestamo = 3;
```

#### Salida
```text
UPDATE 1
```

### 7.2. Intentar eliminar un libro y verificar la integridad referencial

#### Sentencia SQL
```sql
DELETE FROM libros WHERE id_libro = 2;
```

#### Salida
```text
ERROR:  update or delete on table "libros" violates foreign key constraint "prestamos_id_libro_fkey" on table "prestamos"
DETAIL:  Key (id_libro)=(2) is still referenced from table "prestamos".
```

El borrado falló porque la tabla prestamos contiene registros vinculados al libro con id_libro = 2.
Al no haberse definido una regla especial de eliminación (como ON DELETE CASCADE), PostgreSQL aplica la
restricción por defecto (RESTRICT / NO ACTION), la cual protege la integridad referencial impidiendo
la eliminación de un registro padre si existen datos hijos que dependen directamente de él.

---

## 8. Creación de Vistas

### Enunciado
Crear una vista llamada `vista_libros_prestados` que muestre: título del libro, nombre del autor y usuario prestatario.

### Sentencia SQL
```sql
CREATE VIEW vista_libros_prestados AS
SELECT l.titulo, a.nombre, p.usuario_prestario
FROM libros AS l
NATURAL JOIN autores as a
NATURAL JOIN prestamos as p;
```

### Salida
```text
CREATE VIEW
```

---

## 9. Funciones y Consultas Avanzadas

### 9.1. Crear una función que devuelva el título de los libros dados por el autor

#### Sentencia SQL
```sql
CREATE OR REPLACE FUNCTION libros_del_autor(nombre_autor VARCHAR (50))
RETURNS VARCHAR (100)
LANGUAGE plpgsql
AS $$
DECLARE
  result VARCHAR (100);
BEGIN
  SELECT titulo
  INTO result
  FROM libros
  NATURAL JOIN autores
  WHERE nombre = nombre_autor;

  RETURN result;
END;
$$;
```

#### Salida
```text
CREATE FUNCTION
```

### 9.2. Consulta para obtener los tres libros más prestados

#### Sentencia SQL
```sql
SELECT titulo
FROM libros
NATURAL JOIN prestamos
GROUP BY id_libro
ORDER BY COUNT(*) DESC
LIMIT 3;
```

#### Salida
```text
               titulo
-----------------------------------
 Ficciones
 El amor en los tiempos del cólera
 Cien años de soledad
(3 rows)
```

---

## 10. Exportación e Importación de Datos

### 10.1. Exportar la tabla `libros` a un archivo CSV

#### Sentencia SQL / Meta-comando
```sql
\copy libros to ./libros.csv CSV HEADER;
```

#### Contenido generado (`libros.csv`)
```text
id_libro,titulo,ano_publicacion,id_autor
1,Cien años de soledad,1967,1
2,El amor en los tiempos del cólera,1985,1
3,La casa de los espíritus,1982,2
4,Largo petalo de mar,2019,2
5,Ficciones,1944,3
6,El Aleph,1949,3
7,La ciudad y los perros,1963,4
8,Rayuela,1963,5
```

### 10.2. Importar nuevos registros desde un archivo CSV externo

#### Archivo de entrada (`libros_nuevos.csv`)
```text
titulo,ano_publicacion,id_autor
Crónica de una muerte anunciada,1981,1
El general en su laberinto,1989,1
Paula,1994,2
Eva Luna,1987,2
Inés del alma mía,2006,2
El hacedor,1960,3
La fiesta del Chivo,2000,4
Conversación en La Catedral,1969,4
La tía Julia y el escribidor,1977,4
Bestiario,1951,5
Historias de cronopios y de famas,1962,5
Todos los fuegos el fuego,1966,5
```

#### Sentencia SQL / Meta-comando
```sql
\copy libros(titulo, ano_publicacion, id_autor) FROM ./libros_nuevos.csv CSV HEADER;
```

### 10.3. Verificación final de la tabla `libros`

#### Sentencia SQL
```sql
SELECT *
FROM libros;
```

#### Salida
```text
 id_libro |               titulo               | ano_publicacion | id_autor
----------+-----------------------------------+-----------------+----------
        1 | Cien años de soledad               |            1967 |        1
        2 | El amor en los tiempos del cólera |            1985 |        1
        3 | La casa de los espíritus          |            1982 |        2
        4 | Largo petalo de mar               |            2019 |        2
        5 | Ficciones                         |            1944 |        3
        6 | El Aleph                          |            1949 |        3
        7 | La ciudad y los perros            |            1963 |        4
        8 | Rayuela                           |            1963 |        5
        9 | Crónica de una muerte anunciada   |            1981 |        1
       10 | El general en su laberinto        |            1989 |        1
       11 | Paula                             |            1994 |        2
       12 | Eva Luna                          |            1987 |        2
       13 | Inés del alma mía                 |            2006 |        2
       14 | El hacedor                        |            1960 |        3
       15 | La fiesta del Chivo               |            2000 |        4
       16 | Conversación en La Catedral       |            1969 |        4
       17 | La tía Julia y el escribidor      |            1977 |        4
       18 | Bestiario                         |            1951 |        5
       19 | Historias de cronopios y de famas |            1962 |        5
       20 | Todos los fuegos el fuego         |            1966 |        5
(20 rows)
```
