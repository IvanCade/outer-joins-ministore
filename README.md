1. ¿Por qué LEFT JOIN y no INNER JOIN en la Consulta 1?
Se utilizó `LEFT JOIN` tomando la tabla `productos` a la izquierda para garantizar que todos los productos del catálogo estuvieran presentes en el resultado, sin importar si registraban transacciones o no. 

Si hubiésemos utilizado un `INNER JOIN`, la consulta habría descartado automáticamente los registros sin coincidencia en ambas tablas. Como consecuencia, se habrían perdido los productos 108 (Hub USB-C 7p) y 109 (Parlante Bluetooth), impidiendo responder la pregunta de negocio sobre qué artículos no tienen demanda.

2. Estructura de tablas en la Consulta 2 (RIGHT JOIN)
En la Consulta 2:
Tabla a la izquierda (`FROM`): `productos`
Tabla a la derecha (`RIGHT JOIN`): `ventas`

Elegimos `RIGHT JOIN` para preservar el 100% de los registros de la tabla `ventas` (derecha), obligando a traer los datos del catálogo únicamente cuando exista coincidencia. Esto permite poner el foco en la tabla de transacciones e identificar errores operativos en los registros de facturación.

3. Significado de los valores `NULL`
Los valores `NULL` generados por una unión externa no representan fallas en el motor de base de datos, sino **ausencia de coincidencia relacional**:

`venta_id IS NULL` (Consulta 1): Significa que el producto existe en el catálogo pero **nunca ha sido vendido. Por ejemplo, para el producto `108` (`Hub USB-C 7p`), la columna `venta_id` devuelve `NULL` porque no existe ningún registro en la tabla `ventas` cuya clave foránea coincida con ese `producto_id`.
`producto_id IS NULL` en catálogo (Consulta 2): Significa que la venta fue procesada pero **no existe ningún producto asociado en el catálogo. En el registro de la venta `10` con `producto_id = 999`, las columnas pertenecientes a `productos` devuelven `NULL` porque `999` no es una clave primaria válida en el maestro de artículos.

 4. Casos de uso reales para FULL OUTER JOIN
El `FULL OUTER JOIN` es indispensable en procesos de **auditoría de datos, migración de sistemas y reconciliación financiera:

1. Conciliación bancaria vs. ERP:Comparar la lista de pagos registrados en el sistema contable contra el extracto del banco para identificar transacciones en el banco no asentadas en el ERP, y viceversa.
2. Fusión/Migración de bases de datos: Comparar datos provenientes de dos sistemas legacy para verificar inconsistencias en la unificación de cuentas de clientes.
3. Auditoría de inventario físico vs. digital: Cruzar la foto del stock teórico contra las lecturas de los escáneres de depósito para detectar tanto faltantes como stock no registrado.
