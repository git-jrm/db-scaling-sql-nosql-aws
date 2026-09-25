# Bases de Datos para alta escalabilidad

Este repositorio documenta tres análisis de arquitectura de bases de datos SQL vs NoSQL aplicados a casos inspirados en empresas reales, con foco en escalabilidad y disponibilidad. Trabajando con Amazon DynamoDB, Cassandra y AWS Well-Architected Framework.

## Índice
- [I. 📱 Fracttal: Cuando el éxito satura MySQL](#i--fracttal-cuando-el-éxito-satura-mysql)
  - [Introducción](#introducción)
  - [Análisis situación actual](#análisis-situación-actual)
  - [Comparación de tecnologías](#comparación-de-tecnologías)
  - [Propuesta de solución](#propuesta-de-solución)
  - [Recomendación](#recomendación)
  - [Estrategia de migración](#estrategia-de-migración)
  - [Beneficios esperados](#beneficios-esperados)
  - [Conclusión Análisis I](#conclusión-análisis)
- [II. 🎧 MOBI: Streaming a escala, de SQL Server a Cassandra](#ii--mobi-streaming-a-escala-de-sql-server-a-cassandra)
  - [Introducción](#introducción-1)
  - [Análisis situación actual](#análisis-situación-actual-1)
  - [Comparación de tecnologías](#comparación-de-tecnologías-1)
  - [Propuesta de solución](#propuesta-de-solución-1)
  - [Recomendación](#recomendación-1)
  - [Estrategia de migración](#estrategia-de-migración-1)
  - [Beneficios esperados](#beneficios-esperados-1)
  - [Conclusión Análisis II](#conclusión-análisis-1)
- [III. 🛒 PCFactory: Diseñando Claves de Partición para E-commerce](#iii--pcfactory-diseñando-claves-de-partición-para-e-commerce)
  - [Introducción](#introducción-2)
  - [Análisis situación actual](#análisis-situación-actual-2)
  - [Diseño de base de datos en DynamoDB](#diseño-de-base-de-datos-en-dynamodb)
  - [Ventajas y desventajas de migración a DynamoDB](#ventajas-y-desventajas-de-migración-a-dynamodb)
  - [Estrategia de optimización y escalabilidad](#estrategia-de-optimización-y-escalabilidad)
  - [Conclusión Análisis III](#conclusión-análisis-2)
- [Conclusión General](#conclusión-general)
- [Bibliografía](#bibliografía)

---

# I. 📱 Fracttal: Cuando el éxito satura MySQL

## Introducción

La empresa tecnológica chilena Fracttal (gestión del mantenimiento), gracias a su enfoque Mobile First, ha tenido un gran éxito y auge en su rápida expansión internacional. Debido a esto, en los últimos meses se ha vuelto más frecuente la ralentización del sistema y se prevé que la cantidad de usuarios aumente de forma acelerada.

Como arquitecto/a de bases de datos, el objetivo es evaluar y analizar las diferentes tecnologías de bases de datos disponibles y presentar una propuesta que se adapte a los requerimientos específicos del negocio.

## Análisis situación actual

La situación actual en Fracttal es que la App utiliza una base de datos relacional MySQL con motor de almacenamiento InnoDB para optimización de inserción y actualización de datos.

La instancia de base de datos fue escalada verticalmente cada vez que el sistema comenzó a ralentizarse, llegando al máximo soportado a nivel de hardware.

La App actualmente tiene +40K clientes que generan entre todos sus dispositivos +100K registros/hora y +10M registros/mes. Se prevé que la App triplique sus clientes en un año.

## Comparación de tecnologías

Para este análisis se incluyen los siguientes elementos:

- Comparación entre bases de datos relacionales (SQL) y no relacionales (NoSQL).
- Puntos débiles de la solución actual.
- Recomendaciones.
- Justificación.

| Criterio | SQL (Relacional) | NoSQL (No relacional) |
|---|---|---|
| Modelo de datos | Tablas con esquema fijo | Documentos, clave-valor, columnas o grafos |
| Escalabilidad | Vertical (más recursos por servidor) | Horizontal (más nodos) |
| Consistencia | Fuerte (ACID) | Eventual (BASE), configurable según motor |
| Rendimiento en escrituras masivas | Limitado por hardware del nodo | Alto, distribuido entre nodos |
| Esquema | Rígido, requiere migraciones | Flexible, sin esquema fijo |
| Costos | Infraestructura propia (CapEx) | Modelo gestionado, pago por uso (OpEx) |
| Casos de uso típicos | Transacciones financieras, datos relacionales críticos | Alto volumen, IoT, sesiones, catálogos |
| Ejemplos | MySQL, PostgreSQL, SQL Server | DynamoDB, MongoDB, Cassandra |

### Evaluar aspectos clave

El análisis de la situación revela que la arquitectura actual basada en MySQL con escalamiento vertical ha llegado a su límite de capacidad. Se prevé que el peak de volumen de inserciones potencialmente alcance los +300K registros/hora dentro de un año, y la proyección de crecimiento exige un sistema capaz de escalar horizontalmente de manera simple y eficiente, sin interrupciones significativas en el servicio.

Es fundamental considerar:

- **Escalabilidad:** la solución debe permitir distribuir la carga en múltiples nodos sin complejas reestructuraciones.
- **Rendimiento en escrituras:** dada la alta tasa de inserciones, el motor elegido debe manejar escrituras concurrentes masivas sin degradar el tiempo de respuesta.
- **Consistencia vs. disponibilidad:** el modelo de consistencia eventual podría ser aceptable para parte de los datos, siempre que la información crítica mantenga garantías ACID.
- **Costos operativos:** se busca reducir al máximo el gasto de infraestructura propia y la carga operativa.
- **Flexibilidad tecnológica:** evaluar si conviene un único motor de base de datos o un enfoque híbrido que combine lo mejor de SQL y NoSQL.

## Propuesta de solución

Se propone un enfoque conservador: una arquitectura híbrida que mantenga lo que ya está funcionando y combine las fortalezas de las bases de datos relacionales y no relacionales.

Mantener la BD relacional (SQL) actual, MySQL, como la principal, e implementar una BD no relacional (NoSQL) para migrar el almacenamiento de registros operativos, permitiendo alta escalabilidad horizontal de forma nativa.

## Recomendación

Para la BD no relacional (NoSQL) se recomienda **DynamoDB** por estas ventajas:

- **Escalado horizontal:** permite escalado casi ilimitado, soporta millones de registros/hora.
- **Cobro por uso:** ventaja de modelo de negocio dado que la App tiene peaks de uso.
- **Reduce OpEx:** reduce drásticamente la carga operativa y el mantenimiento requerido.
- **Elimina CapEx:** no requiere inversión inicial en infraestructura.

## Estrategia de migración

- **Fase 1:** integrar la nueva base NoSQL para operaciones de escritura intensiva.
- **Fase 2:** migrar datos históricos y ajustar servicios dependientes.
- **Fase 3:** optimizar y ajustar índices, escalado y monitoreo.

## Beneficios esperados

- Reducción de la latencia y eliminación de cuellos de botella.
- Escalabilidad prácticamente ilimitada.
- Menor riesgo de interrupciones ante picos de carga.
- Infraestructura lista para el crecimiento proyectado y nuevos casos de uso.
- Habilita análisis con herramientas de AWS como Amazon QuickSight (dashboards ejecutivos) y Amazon Redshift (data warehousing de registros históricos).

## Conclusión Análisis

El caso muestra que el éxito inicial de un producto puede convertirse en un desafío técnico si no se planifica para el crecimiento, y que los paradigmas modernos de bases de datos ofrecen respuestas efectivas a estos retos.

DynamoDB destaca por su escalabilidad, su modelo de cobro por uso y su integración nativa con las herramientas de inteligencia de negocio de AWS. La solución híbrida propuesta abre la puerta a una evolución continua, donde cada decisión tecnológica se convierte en un habilitador para nuevos mercados y casos de uso.

[Volver al índice](#índice)

---

# II. 🎧 MOBI: Streaming a escala, de SQL Server a Cassandra

## Introducción

Tras recibir venture capital por USD $100 millones en mayo de 2025, la empresa MOBI ha crecido de forma exponencial: 12M de usuarios en 2024, 20M en 2025, y se prevé que triplique su base de usuarios en menos de un año.

Como arquitecto/a de bases de datos, el objetivo es analizar la situación de la empresa y las diferentes tecnologías NoSQL disponibles para presentar una propuesta acorde a las necesidades del negocio.

## Análisis situación actual

Actualmente la plataforma de streaming de MOBI utiliza una base de datos relacional SQL Server. Todos los usuarios generan 1GB de datos por día en métricas de uso; el tamaño de la BD es de 1,5PB y se estima que en un año podría acercarse a los 5PB. Esto no supone una limitante técnica en SQL Server, que soporta más de 500PB en almacenamiento.

El problema es de concurrencia: SQL Server tiene un límite de cerca de 32 mil conexiones simultáneas; al superar este umbral las conexiones se comparten, degradando la experiencia en horarios peak.

Por lo anterior, se precisa elegir la solución que mejor cumpla con los criterios de alta disponibilidad y concurrencia.

## Comparación de tecnologías

Para este análisis se incluyen: comparación entre bases de datos relacionales (SQL) y no relacionales (NoSQL), puntos débiles de la solución actual, recomendaciones y justificación.

> **[Insertar tabla comparativa de tecnologías NoSQL]**

### Evaluar aspectos clave

Desde la perspectiva del teorema CAP, se establece optar por un enfoque que priorice la disponibilidad de lecturas por sobre la consistencia.

Es fundamental considerar:

- **Consistencia vs. disponibilidad:** consistencia eventual aceptable con alta disponibilidad.
- **Escalabilidad:** distintos enfoques para implementar alta escalabilidad.
- **Costos operativos:** comparar el gasto de infraestructura propia frente a opciones gestionadas que reduzcan la carga operativa.
- **Flexibilidad:** evaluar si conviene un único motor de base de datos o un enfoque híbrido.
- **Comunidad:** Mongo y DynamoDB cuentan con comunidades grandes, Cassandra mediana y Neo4j pequeña.

## Propuesta de solución

Se recomienda una arquitectura híbrida que preserve la infraestructura actual funcionando correctamente y aproveche las capacidades específicas de bases de datos NoSQL para resolver el problema de concurrencia.

Conservar la BD relacional SQL Server para datos estructurados críticos, e integrar una BD no relacional (NoSQL) especializada para manejar las conexiones concurrentes masivas y datos de streaming.

## Recomendación

Para la BD no relacional (NoSQL) se propone **Cassandra** por estas características clave:

- **Sin punto único de fallo:** arquitectura distribuida que garantiza disponibilidad continua ante fallos.
- **Concurrencia muy alta:** maneja millones de conexiones simultáneas sin degradación de performance.
- **Escrituras siempre disponibles:** ideal para logs de streaming y datos de sesión en tiempo real.
- **Tolerancia a particiones:** mantiene operación normal aunque se pierda conectividad entre regiones.

## Estrategia de migración

- **Fase 1:** desplegar cluster Cassandra para datos de sesiones y streaming activo.
- **Fase 2:** migrar logs operativos y métricas de uso en tiempo real.
- **Fase 3:** configurar replicación multi-datacenter y ajustar consistency levels.

## Beneficios esperados

- Eliminación completa del cuello de botella de 32K conexiones.
- Disponibilidad 24/7 sin interrupciones por mantenimiento o fallos.
- Escalabilidad lineal preparada para crecimiento exponencial de usuarios.
- Latencia consistente independiente del volumen de tráfico concurrente.

## Conclusión Análisis

El caso resalta la importancia de evaluar tecnologías NoSQL no solo por su capacidad de almacenamiento, sino por su arquitectura para resolver problemas específicos de concurrencia y disponibilidad.

Cassandra destaca por su capacidad de manejar conexiones concurrentes masivas, su arquitectura sin punto único de fallo y su modelo de consistencia tunable, que permite balancear performance y garantías según el tipo de dato — un aspecto decisivo para los requerimientos de disponibilidad 24/7 del streaming.

[Volver al índice](#índice)

---

# III. 🛒 PCFactory: Diseñando Claves de Partición para E-commerce


## Introducción

Debido al alto crecimiento de la industria del e-commerce en Chile, PCFactory se encuentra en expansión acelerada en los últimos años.

Como arquitecto/a de bases de datos en la nube, el objetivo es analizar la situación y proponer una solución basada en DynamoDB, alineada con las buenas prácticas del Well-Architected Framework de AWS.

## Análisis situación actual

En los últimos meses se han presentado problemas en determinados momentos del día: cuando coinciden muchos pedidos simultáneos, el sistema genera latencias que afectan la experiencia del usuario, aumentando la probabilidad de abandono del carro de compra.

Hasta ahora ha ocurrido puntualmente en fechas clave, pero por el crecimiento del negocio existe riesgo de que la frecuencia aumente.

Para resolver el problema de latencias en horarios peak, se propone un diseño optimizado en Amazon DynamoDB basado en un modelo de clave-partición que permita escalar horizontalmente de forma automática.

## Diseño de base de datos en DynamoDB

El diseño utiliza una clave compuesta que combina `ClienteID` como clave de partición y `PedidoID` como clave de ordenamiento, asegurando una distribución eficiente y escalabilidad automática al particionar los registros por cliente.

- Se definió el LSI `PedidosPorCliente` para que cada cliente acceda eficientemente a su información.
- Se definió el GSI `PedidosPorTienda` para que sucursales y casa matriz obtengan eficientemente información contable con granularidad diaria.

A continuación, la estructura de creación de la tabla y un CRUD completo a modo de ejemplo.

**Creación de la tabla Pedidos:**
```bash
$ aws dynamodb create-table \
  --table-name Pedidos \
  --attribute-definitions \
    AttributeName=ClienteID,AttributeType=S \
    AttributeName=PedidoID,AttributeType=S \
  --key-schema \
    AttributeName=ClienteID,KeyType=HASH \
    AttributeName=PedidoID,KeyType=RANGE \
  --local-secondary-indexes '[
    {
      "IndexName": "PedidosPorCliente",
      "KeySchema": [
        {"AttributeName": "ClienteID", "KeyType": "HASH"},
        {"AttributeName": "Fecha", "KeyType": "RANGE"}
      ],
      "Projection": {
        "ProjectionType": "ALL"
      }
    }
  ]' \
  --global-secondary-indexes '[
    {
      "IndexName": "PedidosPorTienda",
      "KeySchema": [
        {"AttributeName": "TiendaID", "KeyType": "HASH"},
        {"AttributeName": "Fecha", "KeyType": "RANGE"}
      ],
      "Projection": {
        "ProjectionType": "INCLUDE",
        "NonKeyAttributes": ["Cantidad", "ValorUnidad"]
      },
      "ProvisionedThroughput": {
        "ReadCapacityUnits": 10
      }
    }
  ]' \
  --provisioned-throughput WriteCapacityUnits=10000
```

**Crea Pedido:**
```bash
$ aws dynamodb put-item \
  --table-name Pedidos \
  --item '{
    "ClienteID": {"S": "C1985"},
    "PedidoID": {"S": "9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d"},
    "Fecha": {"S": "2025-08-10"},
    "ProductoID": {"N": "42"},
    "Cantidad": {"N": "2"},
    "ValorUnidad": {"N": "699000"},
    "TiendaID": {"N": "137"}
  }'
```

**Consulta Pedidos por cliente:**
```bash
$ aws dynamodb query \
  --table-name Pedidos \
  --key-condition-expression "ClienteID = :id" \
  --expression-attribute-values '{":id":{"S": "C1985"}}'
```

Salida:
```json
{
    "Items": [
        {
            "ClienteID": {"S": "C1985"},
            "PedidoID": {"S": "9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d"},
            "Fecha": {"S": "2025-08-10"},
            "ProductoID": {"N": "42"},
            "Cantidad": {"N": "2"},
            "ValorUnidad": {"N": "699000"},
            "TiendaID": {"N": "137"}
        }
    ],
    "Count": 1,
    "ScannedCount": 1,
    "ConsumedCapacity": null
}
```

**Actualiza Pedido:**
```bash
$ aws dynamodb update-item \
  --table-name Pedidos \
  --key '{
    "ClienteID": {"S": "C1985"},
    "PedidoID": {"S": "9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d"}
  }' \
  --update-expression "SET TiendaID = :t" \
  --expression-attribute-values '{
    ":t": {"S": "CL137"}
  }' \
  --return-values ALL_NEW
```

Salida:
```json
{
    "Attributes": {
        "ClienteID": {"S": "C1985"},
        "PedidoID": {"S": "9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d"},
        "Fecha": {"S": "2025-08-10"},
        "ProductoID": {"N": "42"},
        "Cantidad": {"N": "2"},
        "ValorUnidad": {"N": "699000"},
        "TiendaID": {"S": "CL137"}
    }
}
```

**Elimina Pedido:**
```bash
$ aws dynamodb delete-item \
  --table-name Pedidos \
  --key '{
    "ClienteID": {"S": "C1985"},
    "PedidoID": {"S": "9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d"}
  }' \
  --return-values ALL_OLD
```

Salida:
```json
{
    "Attributes": {
        "ClienteID": {"S": "C1985"},
        "PedidoID": {"S": "9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d"},
        "Fecha": {"S": "2025-08-10"},
        "ProductoID": {"N": "42"},
        "Cantidad": {"N": "2"},
        "ValorUnidad": {"N": "699000"},
        "TiendaID": {"S": "CL137"}
    }
}
```

Este diseño permite manejar grandes volúmenes de pedidos distribuyendo uniformemente la carga de lectura y escritura gracias a la clave compuesta. Los LSI facilitan ordenar pedidos por fecha dentro de la misma tienda, mientras que los GSI habilitan consultas por cliente y por estado sin afectar el rendimiento de la tabla principal.

## Ventajas y desventajas de migración a DynamoDB

**Ventajas:**
- **Escalabilidad automática:** crece sin reconfiguración manual.
- **Baja latencia:** respuestas rápidas en milisegundos.
- **Alta disponibilidad:** datos replicados en regiones.

**Desventajas:**
- **Costo impredecible:** solo se puede estimar; varía con el uso y el tráfico.
- **Límites por ítem:** 400 KB máximo por registro.
- **Límite de CapacityUnits:** 40.000 para lectura y escritura (ampliable bajo solicitud).

## Estrategia de optimización y escalabilidad

**¿Cuál estrategia de autoescalado de capacidad en DynamoDB es mejor?**

Se recomienda **Auto Scaling on-demand (On-Demand Capacity Mode)**, ya que ajusta automáticamente la capacidad de lectura y escritura según la demanda real, eliminando la necesidad de estimaciones previas y reduciendo riesgos de throttling en picos de tráfico inesperados.

**¿Con qué servicios se integra DynamoDB?**

- **AWS Lambda:** funciones serverless.
- **API Gateway:** gobernanza de API.
- **AWS Glue:** procesos ETL.
- **Amazon S3:** exportación/importación masiva de datos.
- **Amazon CloudWatch:** monitoreo, alarmas y métricas.
- **AWS Step Functions:** orquestación de flujos de trabajo complejos.

## Conclusión Análisis 

DynamoDB ofrece a PCFactory una plataforma de base de datos altamente escalable y de baja latencia, capaz de absorber peaks de carga sin degradar la experiencia del cliente. Su modelo serverless reduce la complejidad operativa y facilita la evolución futura del sistema sin interrumpir el servicio.

La migración implica desafíos como la adaptación del modelo de datos y la gestión de costos variables, pero los beneficios en rendimiento, disponibilidad e integración nativa con AWS consolidan la propuesta.

[Volver al índice](#índice)

---

# Conclusión General

Los tres casos permiten comparar en profundidad los dos paradigmas principales de bases de datos —relacional y NoSQL— e identificar sus fortalezas y limitaciones.

En los tres análisis, la alta disponibilidad, la flexibilidad y la arquitectura de escalado fueron los elementos de decisión clave.

Las soluciones híbridas propuestas ilustran cómo una organización puede resolver un cuello de botella técnico específico sin descartar la infraestructura relacional existente, cuando esta última ya no es suficiente por sí sola para sostener el crecimiento proyectado.

[Volver al índice](#índice)

# Bibliografía

- https://www.ecommerceccs.cl/ecommerce-en-chile-2025-ventas-digitales-recuperan-niveles-historicos-con-mas-de-25-billones-en-el-primer-cuatrimestre/
- https://docs.aws.amazon.com/es_es/amazondynamodb/latest/developerguide/LSI.html

[Volver al índice](#índice)
