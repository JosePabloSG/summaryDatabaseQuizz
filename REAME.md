# Ejercicio 1:

## Función que recibe un ID de título y devuelve información relacionada.

### Ejemplo:

```sql
USE pubs;
GO

-- Eliminar la función si ya existe
IF OBJECT_ID('dbo.fn_Libro_Inform', 'TF') IS NOT NULL
    DROP FUNCTION dbo.fn_Libro_Inform;
GO

-- Crear la función
CREATE FUNCTION dbo.fn_Libro_Inform (@titleID VARCHAR(20))
RETURNS TABLE
AS
RETURN
(
    SELECT 
        T.title AS Titulo_Libro,
        T.price AS Precio,
        A.au_fname + ' ' + A.au_lname AS Autor,
        A.phone AS Telefono,
        A.city AS Ciudad,
        A.state AS Estado
    FROM 
        titles T
    INNER JOIN 
        titleauthor TA ON T.title_id = TA.title_id
    INNER JOIN 
        authors A ON TA.au_id = A.au_id
    WHERE 
        T.title_id = @titleID
);
GO

-- Confirmar que la función fue creada exitosamente
IF OBJECT_ID('dbo.fn_Libro_Inform', 'TF') IS NOT NULL
    PRINT '<<< FUNCIÓN dbo.fn_Libro_Inform CREADA EXITOSAMENTE >>>';
ELSE
    PRINT '<<< ERROR AL CREAR LA FUNCIÓN dbo.fn_Libro_Inform >>>';
GO

```

### Explicación:

- Contexto de la base de datos: Se establece el uso de la base de datos pubs con USE pubs;.

- Eliminación de la función existente: Verifica si la función fn_Libro_Inform ya existe y la elimina para evitar conflictos al recrearla.

- Creación de la función: Define la función fn_Libro_Inform que recibe un parámetro @titleID y retorna una tabla con información detallada del libro y su autor.

- Selección de datos: Obtiene el título del libro, precio, nombre completo del autor (concatenando nombre y apellido), teléfono, ciudad y estado del autor.

- Uniones de tablas: Realiza INNER JOIN entre las tablas titles, titleauthor y authors para relacionar libros con sus autores.

- Condición de filtrado: Utiliza WHERE T.title_id = @titleID para obtener solo el libro cuyo title_id coincide con el parámetro proporcionado.

- Confirmación de creación: Después de crear la función, verifica si se creó correctamente y muestra un mensaje indicándolo.


# Ejercicio 2: 

## Vista que muestra publicaciones de editores de USA.

### Ejemplo:

```sql
USE pubs;
GO

-- Eliminar la vista si ya existe
IF OBJECT_ID('dbo.AuthorPubUSA', 'V') IS NOT NULL
    DROP VIEW dbo.AuthorPubUSA;
GO

-- Crear la vista
CREATE VIEW dbo.AuthorPubUSA AS
SELECT 
    p.pub_name AS Nombre_Publicacion,
    p.city AS Ciudad,
    p.state AS Estado,
    t.title AS Titulo_Libro,
    t.price AS Precio
FROM 
    dbo.publishers p
INNER JOIN 
    dbo.titles t ON p.pub_id = t.pub_id
WHERE 
    p.country = 'USA';
GO

-- Confirmar la creación de la vista
PRINT 'Vista AuthorPubUSA creada correctamente.';
GO

-- Probar la vista
BEGIN TRY
    SELECT * FROM dbo.AuthorPubUSA;
END TRY
BEGIN CATCH
    PRINT 'Error al seleccionar los datos de la vista AuthorPubUSA: ' + ERROR_MESSAGE();
END CATCH
GO

```

### Explicación:

- Contexto de la base de datos: Utiliza la base de datos pubs con USE pubs;.
- Eliminación de la vista existente: Si la vista AuthorPubUSA existe, se elimina para evitar conflictos al recrearla.
- Creación de la vista: Define la vista AuthorPubUSA que muestra información sobre publicaciones y títulos de editores en USA.
- Selección de datos: Incluye el nombre de la publicación, ciudad, estado, título del libro y precio.
- Unión de tablas: Realiza un INNER JOIN entre publishers y titles usando el pub_id común a ambas tablas.
- Condición de filtrado: Utiliza WHERE p.country = 'USA' para filtrar los editores que están en Estados Unidos.
- Confirmación de creación y prueba de la vista: Muestra un mensaje de confirmación y luego intenta seleccionar datos de la vista, manejando cualquier error que pueda ocurrir.



# Ejercicio 3: 
## Crear una vista infoClien que muestra clientes y sus saldos en el distrito de Nicoya.

### Ejemplo:

```sql
USE Datos;
GO

-- Eliminar la vista si ya existe
IF OBJECT_ID('dbo.infoClien', 'V') IS NOT NULL
    DROP VIEW dbo.infoClien;
GO

-- Crear la vista
CREATE VIEW infoClien AS
SELECT 
    C.NNOMBRE AS Nombre,
    C.IAPELLI AS Apellido1,
    C.IIAPELL AS Apellido2,
    S.SAMONTO AS Saldo,
    R.DISTRITO AS Distrito
FROM 
    saldos S
JOIN 
    clientes C ON S.NCEDULA = C.NCEDULA
JOIN 
    rutas R ON R.CODRUTA = C.CODRUTA
WHERE 
    R.DISTRITO = 'Nicoya';
GO

-- Confirmar que la vista fue creada
IF OBJECT_ID('dbo.infoClien', 'V') IS NOT NULL
    PRINT '<<< VISTA dbo.infoClien CREADA EXITOSAMENTE >>>';
ELSE
    PRINT '<<< ERROR AL CREAR LA VISTA dbo.infoClien >>>';
GO

```

### Explicación:

- Contexto de la base de datos: Se establece el uso de la base de datos Datos.
- Eliminación de la vista existente: Si la vista infoClien existe, se elimina para evitar conflictos al recrearla.
- Creación de la vista infoClien: La vista muestra un listado de clientes y sus saldos para aquellos que se encuentran en el distrito de Nicoya.
- Selección de datos: Incluye el nombre y apellidos del cliente, el saldo y el distrito.
- Uniones de tablas: Une las tablas saldos, clientes y rutas mediante las columnas NCEDULA y CODRUTA.
- Condición de filtrado: Utiliza WHERE R.DISTRITO = 'Nicoya' para filtrar los clientes que pertenecen al distrito de Nicoya.
- Confirmación y visualización: Verifica la creación exitosa de la vista y luego selecciona todos los registros para mostrarlos.

# Ejercicio 4: 
## Crear y modificar la vista vUsuariosFUN para evitar duplicación de información.

### Ejemplo:

```sql
USE DATOSOFIS;
GO

-- Eliminar la vista si ya existe
IF OBJECT_ID('dbo.vUsuariosFUN', 'V') IS NOT NULL
    DROP VIEW dbo.vUsuariosFUN;
GO

-- Crear la vista vUsuariosFUN
CREATE VIEW dbo.vUsuariosFUN AS
SELECT DISTINCT
    U.NOMBRE AS Nombre,
    U.APE1 AS PrimerApellido,
    U.APE2 AS SegundoApellido,
    U.EMAIL AS Email,
    U.CODSEX AS Sexo,
    F.CODPLAZAFD AS CodigoPlazaFD
FROM
    dbo.Usuarios AS U
INNER JOIN 
    dbo.Permisos AS P ON U.IDUSU = P.IDUSU
INNER JOIN 
    dbo.Funcionarios AS F ON P.CODFUNCIONARIO = F.CODFUNCIONARIO
WHERE
    P.CODTITULO = 'FUN';
GO

-- Confirmar que la vista fue creada exitosamente
IF OBJECT_ID('dbo.vUsuariosFUN', 'V') IS NOT NULL
    PRINT '<<< VISTA dbo.vUsuariosFUN CREADA EXITOSAMENTE >>>';
ELSE
    PRINT '<<< ERROR AL CREAR LA VISTA dbo.vUsuariosFUN >>>';
GO
```

### Explicación:

- Contexto de la base de datos: Utiliza la base de datos DATOSOFIS.
- Eliminación de la vista existente: Si la vista vUsuariosFUN existe, se elimina.
- Creación de la vista: La vista muestra nombres, apellidos, email, sexo y código de plaza de los usuarios cuyo código de título es 'FUN'.
- Eliminación de duplicados: Se agrega SELECT DISTINCT para eliminar registros duplicados en el resultado.
- Uniones de tablas: Realiza INNER JOIN entre Usuarios, Permisos y Funcionarios utilizando las claves foráneas adecuadas.
- Condición de filtrado: Utiliza WHERE P.CODTITULO = 'FUN' para seleccionar solo los usuarios con el código de título 'FUN'.
- Confirmación de creación: Verifica si la vista se creó exitosamente y muestra un mensaje.

# Ejercicio 5: 
## Función que determina si un alumno aprueba o reprueba un curso.

### Ejemplo:

```sql
CREATE FUNCTION f_NotaFinal (@Nota1 DECIMAL(4,2), @Nota2 DECIMAL(4,2), @Nota3 DECIMAL(4,2))
RETURNS VARCHAR(10)
AS
BEGIN 
    DECLARE @Promedio DECIMAL(6,2);
    DECLARE @resultado VARCHAR(10);

    -- Cálculo del promedio
    SELECT @Promedio = (@Nota1 + @Nota2 + @Nota3) / 3;

    -- Determinar si aprueba o reprueba
    IF @Promedio >= 4.0
        SET @resultado = 'Aprobaste';
    ELSE 
        SET @resultado = 'Reprobaste';

    RETURN @resultado;
END
GO

-- Probar la función
SELECT dbo.f_NotaFinal(4.5, 3.0, 5.0) AS Resultado;
````

### Explicación:

- Creación de la función f_NotaFinal: La función recibe tres notas y calcula el promedio.
- Declaración de variables: Se declaran @Promedio para almacenar el promedio y @resultado para el mensaje de aprobación o reprobación.
- Cálculo del promedio: Suma las tres notas y divide entre 3.
- Determinación del resultado: Si el promedio es mayor o igual a 4.0, asigna 'Aprobaste'; de lo contrario, 'Reprobaste'.
- Retorno del resultado: La función devuelve el mensaje correspondiente.
- Prueba de la función: Se llama a la función con tres notas de ejemplo y se muestra el resultado.


# Ejercicio 6:
## Función que devuelve el cuadrado y cubo de 5 números.

```sql
CREATE FUNCTION f_cuadrado_cubo (
    @num1 DECIMAL(10,2),
    @num2 DECIMAL(10,2),
    @num3 DECIMAL(10,2),
    @num4 DECIMAL(10,2),
    @num5 DECIMAL(10,2)
)
RETURNS @resultados TABLE (
    numero DECIMAL(10,2),
    cuadrado DECIMAL(10,2),
    cubo DECIMAL(10,2)
)
AS
BEGIN
    -- Insertar los cálculos en la tabla de resultados
    INSERT INTO @resultados
    SELECT @num1, POWER(@num1, 2), POWER(@num1, 3)
    UNION ALL
    SELECT @num2, POWER(@num2, 2), POWER(@num2, 3)
    UNION ALL
    SELECT @num3, POWER(@num3, 2), POWER(@num3, 3)
    UNION ALL
    SELECT @num4, POWER(@num4, 2), POWER(@num4, 3)
    UNION ALL
    SELECT @num5, POWER(@num5, 2), POWER(@num5, 3);

    RETURN;
END;
GO

-- Probar la función
SELECT * FROM dbo.f_cuadrado_cubo(1, 2, 4, 8, 16);
GO
````

### Explicación:

- Creación de la función f_cuadrado_cubo: La función recibe cinco números y devuelve una tabla con cada número, su cuadrado y su cubo.
- Tabla de retorno @resultados: Define una tabla para almacenar los resultados.
- Cálculos matemáticos: Utiliza la función POWER para calcular el cuadrado (exponente 2) y el cubo (exponente 3) de cada número.
- Inserción de datos: Inserta cada número y sus potencias en la tabla de resultados utilizando UNION ALL.
- Retorno de la función: Devuelve la tabla completa con los cálculos.
- Prueba de la función: Llama a la función con números de ejemplo y muestra los resultados.

# Ejercicio 7:
### Procedimiento almacenado que lee números mientras no sean negativos

```sql
CREATE PROCEDURE InsertarNumerosPositivos
    @numero INT
AS
BEGIN
    -- Iniciar la transacción
    BEGIN TRANSACTION;

    BEGIN TRY
        -- Verificar si la tabla ya existe, si no, crearla
        IF OBJECT_ID('dbo.NumerosPositivos', 'U') IS NULL
        BEGIN
            CREATE TABLE NumerosPositivos (
                IdNumero INT IDENTITY(1,1) PRIMARY KEY,
                NumeroIngresado INT
            );
        END

        -- Insertar el número si es positivo o cero
        IF @numero >= 0
        BEGIN
            INSERT INTO NumerosPositivos (NumeroIngresado)
            VALUES (@numero);
            PRINT 'Número insertado con éxito.';
            
            -- Confirmar la transacción
            COMMIT TRANSACTION;
        END
        ELSE
        BEGIN
            PRINT 'El número es negativo. Procedimiento finalizado.';
            
            -- Revertir la transacción
            ROLLBACK TRANSACTION;
        END
    END TRY
    BEGIN CATCH
        -- En caso de error, revertir la transacción
        PRINT 'Ocurrió un error. La transacción ha sido revertida.';
        ROLLBACK TRANSACTION;
    END CATCH
END;
GO

-- Ejemplos de uso
EXEC InsertarNumerosPositivos @numero = 7;   -- Insertará el número
EXEC InsertarNumerosPositivos @numero = -1;  -- No insertará y finalizará
GO

-- Verificar los números insertados
SELECT * FROM NumerosPositivos;
```

### Explicación:


- Creación del procedimiento InsertarNumerosPositivos: Permite insertar números positivos en una tabla mientras no sean negativos.
- Inicio de transacción: Utiliza BEGIN TRANSACTION para asegurar la integridad de los datos.
- Manejo de errores: Implementa TRY...CATCH para manejar excepciones y asegurar que la transacción se revierta en caso de error.
- Verificación y creación de la tabla: Si la tabla NumerosPositivos no existe, la crea.
-Condición para insertar: Si el número es mayor o igual a cero, lo inserta y confirma la transacción; si es negativo, imprime un mensaje y revierte la transacción.
- Prueba del procedimiento: Se ejecuta con un número positivo y luego con uno negativo para demostrar el comportamiento.
- Verificación de datos: Selecciona los registros de la tabla para mostrar los números insertados.
