# Bases de datos para ingenieros de datos

En este espacio exploraremos los principales aspectos aprendidos planteandolo de una manera **atractiva** mediante escenarios actuales de empresas y planteando un caso hipotetico con un fin edicativo.

## Índice:
- [Análisis de caso - Tecnologías de base de datos](#análisis-de-caso---tecnologías-de-base-de-datos)
- [Análisis de caso - Bases de datos no relacionales](#análisis-de-caso---bases-de-datos-no-relacionales)
- [Análisis de caso - DynamoDB](#análisis-de-caso---dynamodb)
- [Evolución del Aprendizaje](#evoluci%C3%B3n-del-aprendizaje)
- [Conclusiones](#conclusiones-3)

## Análisis de caso - Tecnologías de base de datos

### Introducción

La empresa tecnológica chilena Fracttal (gestión del mantenimiento) gracias a su enfoque Mobile First ha tenido un gran éxito y auge en su rápida expansión internacional, debido a esto en los últimos meses se ha vuelto más frecuente la ralentización del sistema y se prevé que la cantidad de usuarios aumente de forma acelerada. 

Es por esto que se nos asigna la labor de evaluar y analizar las diferentes tecnologías de bases de datos disponibles y presentar una propuesta para poder determinar la que mejor se adapte a los requerimientos específicos del negocio.

### Análisis situación actual

La situación actual en Fracttal es que la App utiliza una base de datos relacional MySQL con motor de almacenamiento InnoDB para optimización de inserción y actualización de datos. 

La instancia de base de datos fue escalada verticalmente cada vez que el sistema comenzó a ralentizarse llegando a lo máximo soportado a nivel de hardware.

La App actualmente tiene +40K clientes que llegan a generar entre todos sus dispositivos +100K registros/hora y +10M registros/mes. Se prevé que la App triplique sus clientes en un año.

### Comparación de tecnologías

Para este análisis se incluirán los siguientes elementos:

· Comparación entre bases de datos relacionales (SQL) y no relacionales (NoSQL).
· Puntos débiles solución actual.
· Recomendaciones.
· Justificación.

A continuación se presenta una tabla comparativa con toda la síntesis de toda la información obtenida en la fase de investigación:

Tabla comparativa de tecnologías de bases de datos

Evaluar aspectos clave

El análisis de la situación revela que la arquitectura actual basada en MySQL con escalamiento vertical ha llegado a su límite de capacidad. Se prevé que el peak de volumen de inserciones potencialmente alcance los +300K registros/hora dentro de un año, y la proyección de crecimiento exige un sistema capaz de escalar horizontalmente de manera simple y eficiente, sin interrupciones significativas en el servicio.

Es fundamental considerar:

Escalabilidad: La solución debe permitir distribuir la carga en múltiples nodos sin complejas reestructuraciones.

Rendimiento en escrituras: Dada la alta tasa de inserciones, el motor elegido debe manejar escrituras concurrentes masivas sin degradar el tiempo de respuesta.

Consistencia vs. Disponibilidad: El modelo de consistencia eventual podría ser aceptable para parte de los datos, siempre que la información crítica mantenga garantías ACID.

Costos operativos: Se busca reducir al máximo el gasto de infraestructura propia y la carga operativa para lograr mayor eficiencia.

Flexibilidad tecnológica: Evaluar si conviene un único motor de base de datos o un enfoque mixto o híbrido que combine lo mejor de SQL y NoSQL.

### Propuesta de solución

Se propone un enfoque conservador adoptando una arquitectura híbrida que mantenga lo que ya está funcionando y combine las fortalezas de las bases de datos relacionales y no relacionales:

Mantener la BD Relacional (SQL) actual MySQL como la principal e implementar una BD No relacional (NoSQL) para migrar el almacenamiento de registros operativos que permite alta escalabilidad horizontal de forma nativa.

Recomendación

Para la DB No relacional (NoSQL) se recomienda DynamoDB por estas ventajas:
· Escalado horizontal: permite escalado casi ilimitado, soporta millones de registros/hora.
· Cobro por uso: ventaja modelo de negocio ya que la App tiene peaks de uso.
· Reduce OpEx: reduce drásticamente la carga operativa y el mantenimiento requerido.
· Elimina CaPex: no requiere inversión inicial en infraestructura.

Estrategia de migración

· Fase 1: Integrar la nueva base NoSQL para operaciones de escritura intensiva.
· Fase 2: Migrar datos históricos y ajustar servicios dependientes.
· Fase 3: Optimizar y ajustar índices, escalado y monitoreo.

Beneficios esperados

· Reducción de la latencia y eliminación de cuellos de botella.
· Escalabilidad prácticamente ilimitada.
· Menor riesgo de interrupciones ante picos de carga.
· Infraestructura lista para el crecimiento proyectado y nuevos casos de uso.
· Infraestructura permite análisis con herramientas de AWS como por ej: Amazon QuickSight para dashboards ejecutivos y Amazon Redshift para data warehousing de registros históricos.

### Conclusiones

Este trabajo nos permitió aprender sobre la relevancia de evaluar tecnologías no solo por su capacidad técnica, sino por su potencial de adaptación a escenarios cambiantes. El caso analizado fue clave porque nos enseñó que el éxito inicial de un producto puede convertirse en un desafío técnico si no se planifica para el crecimiento y cómo los paradigmas modernos de bases de datos ofrecen respuestas efectivas a estos retos.

Dentro de las alternativas, DynamoDB destaca no solo por su escalabilidad y modelo de cobro por uso, sino también por su integración nativa con las herramientas de inteligencia de negocio de AWS, un aspecto que puede resultar decisivo y que se alinea con el roadmap de Gobernanza de Datos de la organización.

La solución híbrida propuesta abre la puerta a una evolución continua, donde cada decisión tecnológica se convierte en un habilitador para nuevos mercados y casos de uso. Así, la empresa no solo resuelve un problema inmediato, sino que construye una base sólida para innovar y liderar su sector tanto a corto como a largo plazo.

[Volver](#m4-bases-de-datos-para-ingenieros-de-datos)

## Análisis de caso - Bases de datos no relacionales

### Introducción

Debido al venture capital de USD $100 million en mayo de 2025 la empresa MOBI ha crecido de forma exponencial los últimos meses, con 12M de usuarios en 2024, 20M en 2025 se prevé que se triplique la cantidad de usuarios en menos de un año.

Es por esto que, como Arquitecto de bases de datos, se nos ha encomendado la tarea de analizar la situación de la empresa y las diferentes tecnologías de bases de datos NoSQL disponibles para presentar una propuesta acorde a las necesidades actuales específicas del negocio.

### Análisis situación actual

Actualmente la plataforma de streaming de MOBI utiliza una base de datos relacional SQL Server donde, todos los usuarios generan 1GB de datos por día en métricas de uso, el tamaño de la BD es de 1,5PB y se estima que en un año podría acercarse a los 5PB, esto no supone una limitante técnica en SQL Server que soporta más 500PB en almacenamiento.

El problema es de concurrencia ya que SQL Server tiene un límite de cerca de 32 mil conexiones simultáneas superando este umbral las conexiones se empiezan a compartir lo que degrada la experiencia en horarios peak.

Por lo anterior es que se precisa elegir la solución que mejor cumpla con el criterio de alta disponibilidad y concurrencia.

### Comparación de tecnologías

Para este análisis se incluirán los siguientes elementos: comparación entre bases de datos relacionales (SQL) y no relacionales (NoSQL), puntos débiles solución actual, recomendaciones y justificación.

A continuación se presenta una tabla comparativa una síntesis de la información obtenida en la investigación:

Tabla comparativa de tecnologías de bases de datos NoSQL



Evaluar aspectos clave

Según los antecedentes obtenidos desde la perspectiva del teorema CAP se establece optar por un enfoque que priorice la disponibilidad de lecturas por sobre la consistencia.

Es fundamental considerar:

Consistencia vs. Disponibilidad: consistencia eventual aceptable con alta disponibilidad.

Escalabilidad: diferente enfoque para implementar alta escalabilidad.

Costos operativos: Comparar el gasto de infraestructura propia frente a opciones gestionadas que reduzcan la carga operativa y el tiempo de administración.

Flexibilidad: las 4 alternativas cuentan con Evaluar si conviene un único motor de base de datos o un enfoque mixto o híbrido que combine lo mejor de SQL y NoSQL.

Comunidad: Mongo y DynamoDB cuentan con comunidades grandes, Cassandra mediana y neo4j pequeña.

### Propuesta de solución

Se recomienda una arquitectura híbrida que preserve la infraestructura actual funcionando correctamente y aproveche las capacidades específicas de bases de datos NoSQL para resolver el problema de concurrencia:

Conservar la BD Relacional SQL Server actual para datos estructurados críticos e integrar una BD No relacional (NoSQL) especializada para manejar las conexiones concurrentes masivas y datos de streaming que requieren alta disponibilidad.

Recomendación:

Para la DB No relacional (NoSQL) se propone Cassandra por estas características clave:

· Sin punto único de fallo: arquitectura distribuida que garantiza disponibilidad continua ante fallos.
· Concurrencia muy alta: pumaneja millones de conexiones simultáneas sin degradación de performance.
· Escrituras siempre disponibles: ideal para logs de streaming y datos de sesión en tiempo real.
· Tolerancia a particiones: mantiene operación normal aunque se pierda conectividad entre regiones.

Estrategia de migración:

· Fase 1: Desplegar cluster Cassandra para datos de sesiones y streaming activo.
· Fase 2: Migrar logs operativos y métricas de uso en tiempo real.
· Fase 3: Configurar replicación multi-datacenter y ajustar consistency levels.

Beneficios esperados:

· Eliminación completa del cuello de botella de 32K conexiones.
· Disponibilidad 24/7 sin interrupciones por mantenimiento o fallos.
· Escalabilidad lineal preparada para crecimiento exponencial de usuarios.
· Latencia consistente independiente del volumen de tráfico concurrente.

### Conclusiones

Este trabajo nos permitió comprender la importancia de evaluar tecnologías NoSQL no solo por su capacidad de almacenamiento, sino por su arquitectura para resolver problemas específicos de concurrencia y disponibilidad.

Entre las alternativas analizadas, Cassandra se destaca no solo por su capacidad de manejar conexiones concurrentes masivas y su arquitectura sin punto único de fallo, sino también por su modelo de consistencia tunable que permite balancear performance y garantías según el tipo de dato, un aspecto decisivo que se alinea con los requerimientos de disponibilidad 24/7 del streaming.

La solución híbrida propuesta establece las bases para una evolución tecnológica sostenible, donde cada decisión arquitectural se convierte en un habilitador para soportar el crecimiento exponencial proyectado. De esta manera, MOBI no solo resuelve el problema inmediato de concurrencia, sino que construye una infraestructura resiliente para consolidarse como líder en su mercado de streaming de nicho.

[Volver](#m4-bases-de-datos-para-ingenieros-de-datos)

## Análisis de caso - DynamoDB

### Introducción

Debido al alto crecimiento de la industria del e-commerce en Chile PCFactory se encuentra en crecimiento acelerado en los últimos años.

Como Arquitecto de bases de datos en la nube debemos analizar la situación y proponer una solución basada en DynamoDB. El diseño debe estar siempre alineado con las buenas prácticas del Well-Architected Framework de AWS.

### Análisis situación actual

En los últimos meses se han ido presentando problemas en determinados momentos del día cuando coinciden muchos pedidos de forma simultánea el sistema comienza a provocar latencias que afectan la experiencia del usuario aumentando la probabilidad de abandonar el carro de compra.

Actualmente ha pasado puntualmente en fechas clave sin embargo por el auge existe el riesgo de que aumente la frecuencia.

Para resolver el problema del ecommerce sobre latencias en horarios peaks de pedidos simultáneos, se propone un diseño optimizado en Amazon DynamoDB basado en un modelo de clave-partición que permita escalar horizontalmente de forma automática.

### Diseño de base de datos en DynamoDB

El diseño utiliza una clave compuesta que combina ClienteID como clave de partición y PedidoID como clave de ordenamiento, de esta manera se asegura una distribución eficiente y escalabilidad automática que particiona los registros por clientes.

Se definió el LSI PedidosPorCliente para que cada cliente acceda eficientemente a su información.
Se definió el GSI PedidosPorTienda para que sucursales y matriz obtengan eficientemente información contable con granularidad diaria.

A continuación se muestra la estructura de creación de la tabla y un CRUD completo a modo de ejemplos.

Creación de la tabla Pedidos:
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
        "ReadCapacityUnits": 10      }
    }
  ]' \
  --provisioned-throughput WriteCapacityUnits=10000
```

Crea Pedido:
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

Consulta Pedidos para clientes:
```bash
$ aws dynamodb query \
  --table-name Pedidos \
  --key-condition-expression "ClienteID = :id" \
  --expression-attribute-values '{":id":{"S": "C1985"}}'
```

Salida:
```bash
{
    "Items": [
        {
            "ClienteID": {
                "S": "C1985"
            },
            "PedidoID": {
                "S": "9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d"
            },
            "Fecha": {
                "S": "2025-08-10"
            },
            "ProductoID": {
                "N": "42"
            },
            "Cantidad": {
                "N": "2"
            },
            "ValorUnidad": {
                "N": "699000"
            },
            "TiendaID": {
                "N": "137"
            }
        }
    ],
    "Count": 1,
    "ScannedCount": 1,
    "ConsumedCapacity": null
}
```

Actualiza Pedido y retorna para verificar:
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
```bash
{
    "Attributes": {
        "ClienteID": {
            "S": "C1985"
        },
        "PedidoID": {
            "S": "9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d"
        },
        "Fecha": {
            "S": "2025-08-10"
        },
        "ProductoID": {
            "N": "42"
        },
        "Cantidad": {
            "N": "2"
        },
        "ValorUnidad": {
            "N": "699000"
        },
        "TiendaID": {
            "S": "CL137"
        }
    }
}
```

Elimina Pedido:
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
```bash
{
    "Attributes": {
        "ClienteID": {
            "S": "C1985"
        },
        "PedidoID": {
            "S": "9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d"
        },
        "Fecha": {
            "S": "2025-08-10"
        },
        "ProductoID": {
            "N": "42"
        },
        "Cantidad": {
            "N": "2"
        },
        "ValorUnidad": {
            "N": "699000"
        },
        "TiendaID": {
            "S": "CL137"
        }
    }
}
```

Este diseño permite manejar grandes volúmenes de pedidos distribuyendo uniformemente la carga de lectura y escritura gracias a la clave compuesta. Los LSI facilitan ordenar pedidos por fecha dentro de la misma tienda, mientras que los GSI habilitan consultas por cliente y por estado sin afectar el rendimiento de la tabla principal. El uso de WriteCapacityUnits garantizan alta concurrencia para usuarios y el ReadCapacityUnits garantiza información de calidad para sucursales y casa matriz.

### Ventajas y desventajas de migración a DynamoDB

Ventajas:
· Escalabilidad automática: crece sin reconfiguración manual.
· Baja latencia: respuestas rápidas en milisegundos.
· Alta disponibilidad: datos replicados en regiones.

Desventajas:
· Costo impredecible: solo se puede estimar, varía con uso y tráfico.
· Límites por ítem: 400 KB máximo por registro.
· Límite de CapacityUnits: límite de 40000 para Read y Write (se puede solicitar aumento).

### Estrategia de optimización y escalabilidad

¿Cuál estrategia de autoescalado de capacidad en DynamoDB es mejor?

La estrategia recomendada es Auto Scaling on-demand (On-Demand Capacity Mode), ya que ajusta automáticamente la capacidad de lectura y escritura según la demanda real, eliminando la necesidad de estimaciones previas y reduciendo riesgos de throttling en picos de tráfico inesperados.

¿Con cuáles servicios DynamoDB se integra?

AWS Lambda: para programar funciones sin servidor.
API Gateway: para gobernanza de API.
AWS Glue: para implementar procesos ETL.
Amazon S3: para exportación/importación masiva de datos.
Amazon CloudWatch: para monitoreo, alarmas y otras métricas.
AWS Step Functions: para orquestar flujos de trabajo complejos.

[Volver](#m4-bases-de-datos-para-ingenieros-de-datos)

### Conclusiones

La adopción de DynamoDB ofrece a PCFactory una plataforma de base de datos altamente escalable y de baja latencia, capaz de absorber peaks de carga sin degradar la experiencia del cliente. Su modelo serverless reduce la complejidad operativa y facilita la evolución futura del sistema sin interrumpir el servicio.

Si bien la migración implica desafíos como la adaptación del modelo de datos y la gestión de costos variables, los beneficios en rendimiento, disponibilidad e integración nativa con otras herramientas de AWS, otorgan beneficios que consolidan la posición de líder de la empresa.

[Volver](#m4-bases-de-datos-para-ingenieros-de-datos)

### Bibliografía:

https://www.ecommerceccs.cl/ecommerce-en-chile-2025-ventas-digitales-recuperan-niveles-historicos-con-mas-de-25-billones-en-el-primer-cuatrimestre/?utm_source=chatgpt.com

https://docs.aws.amazon.com/es_es/amazondynamodb/latest/developerguide/LSI.html

[Volver](#m4-bases-de-datos-para-ingenieros-de-datos)

## Evolución del Aprendizaje

A lo largo de todas las actividades desarrolladas pudimos profundizar conceptos de arquitecturas de bases de datos y escenarios actuales complejos resueltos con tecnologías modernas.

[Volver](#m4-bases-de-datos-para-ingenieros-de-datos)

## Conclusiones

El aprendizaje del **módulo 4** nos permitió comparar en profundidad los aspectos de mayor relevancia de los dos paradigmas principales de bases de datos el Relacional y el NoSQL (o no relacional), permitiendonos identificar las fortalezas y limitaciones de ambos.

Los escenarios hipoteticos desarrollados nos permitieron plantear ejemplos donde la alta disponibilidad, la flexibilidad y la arquitectura de escalado eran elementos clave.

Las soluciones hibridas desarrolladas nos mostraron escenarios actuales en las que organizaciones han tenido que tomar decisiones decisivas en momentos en los que la infraestructura que sirvió para llegar ya no es sificiente.

[Volver](#m4-bases-de-datos-para-ingenieros-de-datos)
