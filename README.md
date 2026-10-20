# Sistema de autobuses

Aplicación académica de escritorio en C#, Windows Forms, .NET Framework 4.8 y SQL Server para registrar conductores, autobuses y rutas, y consultar sus asignaciones.

[Ver portafolio](https://jeison-rosario.github.io/proyectos/)

## Funcionalidades presentes

- Registrar y listar conductores con datos personales y estado.
- Registrar y listar autobuses con marca, modelo, placa, color y año.
- Registrar y listar rutas.
- Asignar conductor, autobús y ruta mediante sus identificadores.
- Consultar asignaciones mediante la vista `Asignados`.
- Mostrar mensajes cuando un conductor, autobús o ruta ya está asignado.

El estado se almacena como `BIT`; los registros nuevos se envían con 0 y el procedimiento actualiza a 1 cuando corresponde. No incluye GPS, mapas ni seguimiento de vehículos en tiempo real.

## Arquitectura y estructura

| Ubicación | Función |
| --- | --- |
| `sistema de autobuses/CapaPresentacio/` | Formularios Windows Forms |
| `sistema de autobuses/CapaNegocio/` | Intermediación de operaciones |
| `sistema de autobuses/capaEntidad/` | Datos compartidos |
| `sistema de autobuses/capaDatos/` | ADO.NET y procedimientos SQL |
| `Sistema de autobuses.sql` | Base, tablas, vista y procedimientos |

La solución principal referencia **`CapaPresentacio/CapaPresentacion.csproj`**. También hay una carpeta `capaPresentacion/` alternativa; no confundirla con el proyecto de inicio de la solución.

## Requisitos

Windows, Visual Studio con desarrollo de escritorio .NET, targeting pack de **.NET Framework 4.8**, SQL Server y un editor T-SQL. El usuario de desarrollo necesita permisos para preparar la base y ejecutar sus procedimientos.

Es una aplicación WinForms; no se publica directamente en GitHub Pages ni en un alojamiento Linux como las demos web.

## Preparación local

```sh
git clone https://github.com/Jeison-Rosario/Sistema-de-autobuses.git
```

1. Revisar `Sistema de autobuses.sql` y ejecutarlo en una instancia de pruebas. Crea **`Sistema_Autobuses`**; no es un script idempotente para repetir sobre una base existente.
2. Abrir **`sistema de autobuses/Parcial 2- sistema de autobuses.sln`**.
3. Cambiar la conexión **`conect`** en **`sistema de autobuses/CapaPresentacio/App.config`** a la instancia local. Ejemplo con autenticación integrada:

```xml
<connectionStrings>
  <add name="conect"
       connectionString="Server=SERVIDOR\INSTANCIA;Database=Sistema_Autobuses;Integrated Security=True;" />
</connectionStrings>
```

4. Establecer **CapaPresentacion** como proyecto de inicio, compilar y ejecutar. El usuario de Windows debe tener acceso a esa base.
5. Registrar primero conductores, autobuses y rutas. El script no incluye un catálogo de datos de ejemplo.

Si se cambia a autenticación SQL, no publicar contraseñas reales en la configuración.

## Modelo y procedimientos

| Objeto | Contenido |
| --- | --- |
| `Conductores` | ID, código calculado, datos personales y estado |
| `Autobuses` | ID, código calculado, características y estado |
| `Rutas` | ID, descripción y estado |
| `Asignar` | Relación con claves foráneas a conductor, autobús y ruta |
| `Asignados` | Vista de consulta combinada |

Los procedimientos `SP_REGISTRARCONDUCTOR`, `SP_REGISTRARAUTOBUS` y `SP_REGISTRARRUTA` insertan datos; sus `SP_LISTAR*` los consultan. `SP_ASIGNAR` devuelve `@Resultado`: 1 conductor asignado, 2 autobús asignado, 3 ruta asignada y 4 éxito. `SP_ASIGNADO` consulta la vista.

Estos controles no equivalen a una garantía de exclusión concurrente mediante restricciones únicas y transacciones.

## Recorrido y comprobación manual

1. Registrar un conductor, un autobús con año numérico y una ruta.
2. Anotar los IDs y abrir el formulario de asignación.
3. Introducir los tres IDs y asignar.
4. Consultar el listado combinado y los estados.
5. Intentar reutilizar cada recurso para comprobar los mensajes.

Probar además un ID inexistente, campos vacíos y un año no numérico. Comprobar que los estados de disponibilidad concuerden con las filas de `Asignar`.

## Problemas frecuentes

| Problema | Comprobación |
| --- | --- |
| No conecta | Instancia, servicio SQL Server, permisos y conexión del proyecto correcto |
| Procedimiento inexistente | Ejecutar y comprobar los bloques SQL en la base correcta |
| No compila para 4.8 | Instalar targeting pack |
| Error con el año | El flujo usa conversión directa a entero; introducir un valor numérico |

## Límites y mejoras

No se observa un flujo para finalizar/liberar asignaciones ni un CRUD completo de modificación y eliminación. Añadir transacciones, validaciones de datos, restricciones únicas según las reglas y cierre seguro de conexiones con `using`. Revisar la coherencia de estados ante errores y concurrencia. Excluir `bin/` y `obj/` del control de versiones y aclarar el proyecto de presentación alternativo.

## Validación, licencia y autor

Documentación contrastada con solución, proyecto, formularios, capa de datos, configuración y SQL. No se compiló en Windows ni se ejecutó SQL Server durante esta ampliación. Los casos manuales son propuestas de comprobación.

Consultar [LICENSE](LICENSE). Autor: [Jeison Rosario](https://github.com/Jeison-Rosario).
