# Dashboards en Power BI: rentabilidad de una consultora de opinión pública

Trabajo práctico integrador de **Ingeniería en Sistemas Informáticos (Universidad Abierta Interamericana)**. Se desarrollaron y compararon dos dashboards sobre el mismo caso: uno **estático** (alimentado desde un CSV) y otro **dinámico** (conectado a **SQL Server** en modo **DirectQuery**).

> Trabajo grupal (Grupo 4). Mi rol: integración de los datos en Power BI, declaración de las medidas DAX y construcción completa de los dashboards. El modelo de datos, el análisis y la documentación se hicieron en equipo. Se usó Claude (Anthropic) como asistente durante el diseño y la implementación, como se detalla en el informe.

## El problema

Una consultora de opinión pública aumenta su facturación año a año, pero opera con **margen negativo**. El objetivo fue encontrar dónde se pierde dinero y por qué.

**Hallazgos principales**

* Facturó **$267.796** contra **$323.172** de costos: un margen global de **-$55.375**.
* El canal **telefónico** (-$37.572) y la **intercepción en la calle** (-$27.363) son los que más pierden. **Focus group** (+$14.766) y **presencial en domicilio** (+$4.995) sostienen el negocio.
* La tasa de éxito mejoró (35,4% en 2020 a 40,8% en 2026) pero la rentabilidad empeoró: la eficiencia operativa no se tradujo en ganancia.
* El **56%** del costo se gasta en relevamientos fallidos ($180.833), y casi el 70% de esa pérdida viene de rechazos y llamadas no atendidas.

## Herramientas

Power BI Desktop, DAX, SQL Server (SSMS), DirectQuery, modelo dimensional tipo copo de nieve (24 tablas, 20.000 registros de hechos).

## Parte A: dashboard estático (CSV)

Alimentado desde `relevamientos.csv` (20.000 registros). KPIs: rentabilidad neta, costo hundido y tasa de conversión.

## Parte B: dashboard dinámico (SQL Server, DirectQuery)

Power BI se conecta directamente a la base `ConsultoraOpinionDW`, de modo que los visuales reflejan los cambios en el origen.

**Qué hice:** conexión al servidor y la base, selección de tablas, modelo de relaciones, medidas DAX (Facturación, Costo total, Margen, Tasa de efectividad, Costo desperdiciado %), una columna calculada `Anio` y el armado de todas las visualizaciones: tarjetas de KPI, evolución anual, margen por canal con formato condicional, ranking de encuestadores y resultado de intentos por canal.

!\[Conexión a SQL Server][`capturas/`](capturas/conexion\_sqlserver.png)
!\[Modelo de datos][`capturas/`](capturas/dinamico\_modelo.png)
!\[Dashboard antes de la carga][`capturas/`](capturas/dinamico\_dashboard\_antes.png)

### Prueba de actualización

Para verificar que el dashboard es dinámico, se insertaron en SQL Server 300 encuestas telefónicas completas  y se actualizó el reporte:

||Antes|Después|
|-|-|-|
|Intentos|20.000|20.300|
|Facturación|$267,80 mil|$312,80 mil|
|Margen|-$55,38 mil|-$13,38 mil|

!\[Resultado de la prueba][`capturas/`](capturas/dinamico\_prueba\_sql.png)
!\[Dashboard después de la carga][`capturas/`](capturas/dinamico\_dashboard\_despues.png)

### Medidas DAX principales

```

Facturacion = SUM ( hechos\_relevamiento\[importe\_facturado] )

Costo_total = SUM ( hechos\_relevamiento\[costo\_encuesta] )

Margen = [Facturacion] - [Costo\_total]

Tasa\_de\_efectividad = DIVIDE ( \[Encuestas\_completas], \[Intentos] )

Costo desperdiciado % = 

DIVIDE (

&#x20;   CALCULATE ( \[Costo\_total], hechos\_relevamiento\[cant\_encuesta] = 0 ),

&#x20;   \[Costo\_total]

)

Anio = QUOTIENT ( hechos\_relevamiento\[COD\_fecha], 10000 )
```

Los datos son **sintéticos**, generados con fines académicos.

## Equipo

Grupo 4: Ignacio Andrés Rodríguez (PM), Thiago Alberto Castellano Herrera, Diego Emanuel Nievas, Emiliano Lucas Orcinoli, Ever Vergara Ibarra y Ezequiel Pedrerol.

Informe completo: [`informe/`](informe/)

