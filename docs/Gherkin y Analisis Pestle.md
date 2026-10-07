# Historias de usuario en Gherkin y análisis PESTLE

Requisitos funcionales mínimos del sistema de simulación (Centro de Monitoreo, Mapa de Tráfico y Panel de Incidentes).

---

## RF-01 – Gestionar los escenarios del sistema

### Gherkin

```gherkin
Feature: Gestionar los escenarios del sistema

  Como operador del centro de emergencias
  Quiero navegar entre el Centro de Monitoreo, el Mapa de Tráfico y el Panel de Incidentes
  Para acceder rápidamente a la información que necesito para atender emergencias

  Scenario: Navegar a un escenario disponible (caso feliz)
    Given el sistema está en ejecución y el operador está en el Centro de Monitoreo
    When el operador selecciona la opción "Mapa de Tráfico"
    Then el sistema muestra el escenario del Mapa de Tráfico
    And el escenario anterior queda oculto sin perder su estado

  Scenario: Regresar al escenario anterior (alternativo)
    Given el operador se encuentra en el Panel de Incidentes
    When el operador selecciona la opción "Centro de Monitoreo"
    Then el sistema muestra el Centro de Monitoreo con los indicadores actualizados
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Existen regulaciones que condicionen cómo se presenta la información de emergencias al operador? | Los centros de emergencia suelen regirse por protocolos institucionales de operación. | RNFP-01-01: "La navegación entre escenarios debe respetar el flujo operativo definido por el equipo para el centro de emergencias simulado." |
| E – Económico | ¿Genera costos directos la navegación entre escenarios? | No genera costos de licencias; solo consume recursos de memoria de la aplicación. | RNFE-01-02: "El sistema debe reutilizar los escenarios ya cargados para evitar consumo innecesario de memoria." |
| S – Social | ¿La interfaz de navegación es clara para el operador? | Una navegación confusa retrasa la atención de incidentes. | RFS-01-03: "El sistema debe mostrar opciones de navegación con etiquetas claras y consistentes en todos los escenarios." |
| T – Tecnológico | ¿Depende de una plataforma específica? | Depende de la JVM y de la librería gráfica usada (por ejemplo JavaFX). | RNFT-01-04: "El sistema debe ejecutarse en cualquier equipo con la versión de Java definida para el proyecto." |
| L – Legal | ¿Se debe garantizar trazabilidad de la navegación? | La navegación no maneja datos personales, pero puede registrarse en el historial. | RNFL-01-05: "El sistema debe registrar en el historial los cambios de escenario realizados por el operador." |
| E – Ético | ¿La información mostrada en cada escenario es veraz? | Un escenario desactualizado podría inducir decisiones erradas. | RNFET-01-06: "El sistema debe mostrar en cada escenario información actualizada al momento de la consulta." |

---

## RF-02 – Representar el mapa de la ciudad

### Gherkin

```gherkin
Feature: Representar el mapa de la ciudad

  Como operador del centro de emergencias
  Quiero ver el mapa con zonas residenciales y comerciales, vías, rutas, vehículos e incidentes activos
  Para tener una visión general de la ciudad y ubicar dónde ocurre cada emergencia

  Scenario: Visualizar el mapa completo (caso feliz)
    Given la configuración del mapa fue cargada correctamente
    When el operador abre el Mapa de Tráfico
    Then el sistema muestra las zonas residenciales y comerciales, las vías principales y las rutas
    And los vehículos e incidentes activos aparecen en su ubicación actual

  Scenario: Visualizar el mapa sin incidentes activos (alternativo)
    Given no existen incidentes activos en la simulación
    When el operador abre el Mapa de Tráfico
    Then el sistema muestra el mapa y los vehículos sin marcadores de incidentes
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Existen normas sobre el uso de cartografía o datos geográficos? | Un mapa real puede tener restricciones de uso; en la simulación el mapa es ficticio. | RNFP-02-01: "El sistema debe usar un mapa ficticio o de uso libre definido en el archivo de configuración." |
| E – Económico | ¿Genera costos el renderizado del mapa? | El dibujo constante del mapa consume CPU y memoria. | RNFE-02-02: "El sistema debe redibujar solo los elementos del mapa que cambien para optimizar recursos." |
| S – Social | ¿El mapa es comprensible para todos los operadores? | Colores poco distinguibles pueden excluir a personas con daltonismo. | RFS-02-03: "El mapa debe usar colores y símbolos distinguibles entre zonas, vehículos e incidentes." |
| T – Tecnológico | ¿Qué rendimiento debe garantizar la visualización? | Un mapa lento dificulta el seguimiento en tiempo real. | RNFT-02-04: "El mapa debe actualizarse sin retrasos perceptibles con la cantidad máxima de vehículos e incidentes definida." |
| L – Legal | ¿Hay datos personales en el mapa? | No se almacenan datos personales; solo ubicaciones de la simulación. | RNFL-02-05: "El mapa no debe mostrar datos personales de personas reales." |
| E – Ético | ¿El mapa podría engañar al operador? | Un vehículo mal ubicado llevaría a asignaciones incorrectas. | RNFET-02-06: "La posición mostrada de vehículos e incidentes debe corresponder al estado real de la simulación." |

---

## RF-03 – Gestionar incidentes

### Gherkin

```gherkin
Feature: Gestionar incidentes

  Como operador del centro de emergencias
  Quiero registrar, consultar y actualizar incidentes de tipo accidente, robo e incendio
  Para mantener un control claro de todas las emergencias de la ciudad

  Scenario: Registrar un incidente nuevo (caso feliz)
    Given el operador está en el Panel de Incidentes y los datos del incidente son válidos
    When el operador registra un incidente con tipo, ubicación, gravedad y descripción
    Then el sistema crea el incidente con un identificador único, fecha y hora de generación y estado "pendiente"
    And el incidente aparece en el Panel de Incidentes y en el mapa

  Scenario: Registrar un incidente con datos incompletos (alternativo)
    Given el operador está en el formulario de registro de incidentes
    When el operador intenta registrar un incidente sin ubicación
    Then el sistema rechaza el registro
    And el sistema muestra un mensaje indicando que la ubicación es obligatoria

  Scenario: Actualizar el estado de un incidente
    Given existe un incidente en estado "pendiente"
    When el operador cambia su estado a "en atención"
    Then el sistema guarda el nuevo estado y actualiza la información mostrada
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Existen normas sobre el registro de incidentes de emergencia? | En la realidad, los reportes deben seguir protocolos oficiales. | RNFP-03-01: "El registro de incidentes debe incluir los campos mínimos definidos por el protocolo del proyecto." |
| E – Económico | ¿Qué impacto tendría una falla en el registro? | Perder un incidente implicaría no atender una emergencia (en la simulación, perder puntaje). | RNFE-03-02: "El sistema no debe perder incidentes registrados durante la ejecución." |
| S – Social | ¿Los datos del incidente son comprensibles para el operador? | Descripciones ambiguas dificultan la decisión. | RFS-03-03: "El sistema debe mostrar el tipo, la gravedad y el estado de cada incidente con lenguaje claro." |
| T – Tecnológico | ¿Qué rendimiento se requiere al consultar incidentes? | Consultas lentas retrasan la atención. | RNFT-03-04: "La consulta de un incidente por identificador debe responder de forma inmediata para el operador." |
| L – Legal | ¿Se requiere trazabilidad de los incidentes? | Cada incidente debe poder auditarse con fecha y hora. | RNFL-03-05: "Cada incidente debe conservar su fecha, hora de generación y los cambios de estado." |
| E – Ético | ¿La información registrada es veraz? | Datos erróneos generan decisiones erróneas. | RNFET-03-06: "El sistema debe validar los datos del incidente antes de registrarlo." |

---

## RF-04 – Generar incidentes

### Gherkin

```gherkin
Feature: Generar incidentes

  Como operador del centro de emergencias
  Quiero que el sistema genere robos e incendios aleatoriamente y accidentes según criterios definidos
  Para que la simulación sea dinámica y refleje el comportamiento de una ciudad real

  Scenario: Generar un robo o incendio aleatorio (caso feliz)
    Given la simulación está en ejecución
    When el generador de eventos produce un incidente aleatorio de tipo robo o incendio
    Then el sistema crea el incidente con ubicación y gravedad aleatorias válidas
    And los indicadores del Centro de Monitoreo se actualizan

  Scenario: Generar un accidente por un criterio definido (caso feliz)
    Given se cumple uno de los criterios de accidente definidos por el equipo, por ejemplo alta cantidad de vehículos en una misma vía
    When el generador evalúa los criterios de accidentes
    Then el sistema genera un accidente en la vía que cumplió el criterio
    And el accidente se registra con el criterio que lo originó

  Scenario: No generar accidentes si no se cumple ningún criterio (alternativo)
    Given ninguna vía cumple los criterios de accidente
    When el generador evalúa los criterios de accidentes
    Then el sistema no genera ningún accidente
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Los criterios de accidentes deben basarse en normas viales? | Basar los criterios en normas de tránsito aporta realismo. | RNFP-04-01: "Los criterios de generación de accidentes deben estar documentados y justificados con base en la simulación." |
| E – Económico | ¿Genera costos la generación continua de eventos? | Consume CPU si la frecuencia es muy alta. | RNFE-04-02: "La frecuencia de generación de incidentes debe ser configurable para controlar el consumo de recursos." |
| S – Social | ¿La generación afecta negativamente a algún grupo de usuarios? | Una generación excesiva puede abrumar al operador. | RFS-04-03: "El sistema debe limitar la cantidad de incidentes simultáneos para mantener la jugabilidad." |
| T – Tecnológico | ¿Qué tecnologías se requieren? | Requiere generación de números aleatorios e hilos de ejecución. | RNFT-04-04: "La generación de incidentes debe ejecutarse en un hilo independiente de la interfaz." |
| L – Legal | ¿Se requiere trazabilidad de los eventos generados? | Debe poder explicarse por qué se generó cada incidente. | RNFL-04-05: "El sistema debe registrar el tipo y el criterio de origen de cada incidente generado." |
| E – Ético | ¿La generación es transparente para el usuario? | Un azar percibido como injusto reduce la confianza. | RNFET-04-06: "El sistema debe generar los incidentes de forma imparcial según las reglas definidas, sin favorecer al operador ni perjudicarlo." |

---

## RF-05 – Gestionar la prioridad de los incidentes

### Gherkin

```gherkin
Feature: Gestionar la prioridad de los incidentes

  Como operador del centro de emergencias
  Quiero que los incidentes activos se ordenen por gravedad con un criterio de desempate
  Para atender primero las emergencias más críticas

  Scenario: Consultar el incidente de mayor prioridad (caso feliz)
    Given existen incidentes activos con distinta gravedad
    When el operador consulta el incidente de mayor prioridad
    Then el sistema muestra el incidente con la gravedad más alta

  Scenario: Desempatar incidentes con igual gravedad (alternativo)
    Given existen dos incidentes activos con la misma gravedad
    When el operador consulta el incidente de mayor prioridad
    Then el sistema aplica el criterio de desempate definido, por ejemplo el de menor hora de generación
    And el incidente seleccionado queda marcado como el próximo a atender

  Scenario: Consultar prioridad sin incidentes (alternativo)
    Given no existen incidentes activos
    When el operador consulta el incidente de mayor prioridad
    Then el sistema informa que no hay incidentes pendientes
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Existen protocolos que definan la prioridad de las emergencias? | Los protocolos de triage definen qué se atiende primero. | RNFP-05-01: "La prioridad debe basarse en la gravedad definida y en un criterio de desempate documentado." |
| E – Económico | ¿Qué impacto tiene un orden incorrecto? | Atender un incidente leve antes que uno grave genera pérdidas (puntaje en la simulación). | RNFE-05-02: "El sistema debe garantizar que siempre se entregue el incidente de mayor prioridad." |
| S – Social | ¿El criterio de prioridad es justo para todas las zonas? | Priorizar solo ciertas zonas podría dejar otras desatendidas. | RFS-05-03: "El criterio de desempate no debe favorecer sistemáticamente a una zona de la ciudad." |
| T – Tecnológico | ¿Qué rendimiento se requiere? | La estructura debe reordenarse rápido al llegar nuevos incidentes. | RNFT-05-04: "El sistema debe usar una estructura eficiente (por ejemplo una cola de prioridad) para consultar el incidente más prioritario." |
| L – Legal | ¿Se debe registrar el orden de atención? | Permite auditar las decisiones. | RNFL-05-05: "El sistema debe registrar qué incidente se atendió y su prioridad en ese momento." |
| E – Ético | ¿La prioridad podría discriminar? | Un criterio mal definido podría sesgar la atención. | RNFET-05-06: "El sistema debe explicar al operador el criterio con el que se ordenaron los incidentes." |

---

## RF-06 – Gestionar vehículos de atención

### Gherkin

```gherkin
Feature: Gestionar vehículos de atención

  Como operador del centro de emergencias
  Quiero registrar y consultar vehículos con su tipo, ubicación y estado
  Para saber qué recursos tengo disponibles para atender incidentes

  Scenario: Consultar un vehículo disponible (caso feliz)
    Given existe un vehículo registrado en estado "disponible"
    When el operador consulta la información del vehículo
    Then el sistema muestra su tipo, ubicación y estado

  Scenario: Cambiar el estado del vehículo durante una atención
    Given un vehículo disponible es asignado a un incidente
    When el vehículo inicia el desplazamiento
    Then el sistema cambia su estado a "en camino"
    And el indicador de vehículos disponibles disminuye en uno

  Scenario: Registrar un vehículo con tipo inválido (alternativo)
    Given el operador está registrando un vehículo
    When ingresa un tipo de vehículo que no existe en el sistema
    Then el sistema rechaza el registro
    And muestra un mensaje de error claro
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Existen normas sobre los vehículos de emergencia? | En la realidad, ambulancias, bomberos y policía tienen regulaciones propias. | RNFP-06-01: "El sistema debe manejar únicamente los tipos de vehículo definidos en el modelo (por ejemplo ambulancia, patrulla y bomberos)." |
| E – Económico | ¿Qué impacto tiene que el estado del vehículo sea erróneo? | Un vehículo mal marcado como disponible provocaría asignaciones fallidas. | RNFE-06-02: "El sistema debe mantener el estado de los vehículos consistente con las atenciones en curso." |
| S – Social | ¿La información es clara? | Estados ambiguos confunden al operador. | RFS-06-03: "El sistema debe mostrar los estados de los vehículos con nombres claros y diferenciables." |
| T – Tecnológico | ¿Qué rendimiento se requiere? | Los estados cambian continuamente durante la simulación. | RNFT-06-04: "El sistema debe actualizar el estado de un vehículo sin bloquear la interfaz." |
| L – Legal | ¿Se requiere trazabilidad? | Permite revisar el uso de cada vehículo. | RNFL-06-05: "El sistema debe registrar los cambios de estado de cada vehículo." |
| E – Ético | ¿Se muestra información veraz? | Un estado incorrecto engaña al operador. | RNFET-06-06: "El estado mostrado de un vehículo debe coincidir con su estado real en la simulación." |

---

## RF-07 – Asignar vehículos a incidentes

### Gherkin

```gherkin
Feature: Asignar vehículos a incidentes

  Como operador del centro de emergencias
  Quiero asignar un vehículo disponible compatible a un incidente y recibir un vehículo candidato sugerido
  Para atender cada emergencia con el recurso adecuado

  Scenario: Asignar un vehículo compatible (caso feliz)
    Given existe un incidente de tipo incendio y un vehículo de bomberos disponible
    When el operador asigna el vehículo de bomberos al incidente
    Then el sistema registra la asignación
    And el vehículo pasa a estado "asignado" y el incidente muestra su vehículo asignado

  Scenario: Rechazar una asignación incompatible (alternativo)
    Given existe un incidente de tipo incendio y una ambulancia disponible
    When el operador intenta asignar la ambulancia al incendio
    Then el sistema rechaza la asignación
    And muestra un mensaje indicando que el vehículo no es compatible con el tipo de incidente

  Scenario: Proponer un vehículo candidato
    Given existen varios vehículos compatibles disponibles
    When el operador solicita una sugerencia de vehículo
    Then el sistema propone el vehículo compatible con menor tiempo estimado de llegada
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Hay normas sobre qué vehículo debe atender cada emergencia? | Los protocolos de emergencia definen el recurso adecuado por tipo de incidente. | RNFP-07-01: "El sistema debe validar la compatibilidad entre tipo de vehículo y tipo de incidente según una tabla definida." |
| E – Económico | ¿Qué impacto tiene una mala asignación? | Genera demoras y pérdida de puntaje. | RNFE-07-02: "El sistema debe sugerir el vehículo que minimice el costo o tiempo de respuesta." |
| S – Social | ¿El sistema evita asignaciones injustas entre zonas? | Siempre enviar el mismo vehículo puede dejar zonas descubiertas. | RFS-07-03: "El sistema debe advertir al operador cuando una asignación deje una zona sin cobertura." |
| T – Tecnológico | ¿Qué tecnologías se integran? | Se integra con el cálculo de rutas del grafo. | RNFT-07-04: "La sugerencia de candidato debe usar el módulo de rutas de menor costo." |
| L – Legal | ¿Se requiere trazabilidad? | Debe saberse quién asignó qué y cuándo. | RNFL-07-05: "El sistema debe registrar cada asignación en el historial de acciones." |
| E – Ético | ¿Podría el sistema manipular la decisión del operador? | La sugerencia no debe imponerse. | RNFET-07-06: "La sugerencia de vehículo debe ser opcional y el operador debe poder elegir otro vehículo válido." |

---

## RF-08 – Gestionar la atención de incidentes

### Gherkin

```gherkin
Feature: Gestionar la atención de incidentes

  Como operador del centro de emergencias
  Quiero iniciar y finalizar la atención de un incidente
  Para resolver las emergencias y liberar los vehículos para nuevas atenciones

  Scenario: Iniciar la atención de un incidente (caso feliz)
    Given un incidente tiene un vehículo asignado y el vehículo llegó a su ubicación
    When el operador inicia la atención
    Then el sistema cambia el estado del incidente a "en atención"
    And el vehículo cambia a estado "atendiendo"

  Scenario: Finalizar la atención y liberar el vehículo
    Given un incidente se encuentra en estado "en atención"
    When el operador finaliza la atención
    Then el sistema cambia el estado del incidente a "resuelto"
    And el vehículo vuelve a estar "disponible"
    And el sistema actualiza el puntaje del operador

  Scenario: Iniciar la atención sin vehículo asignado (alternativo)
    Given un incidente no tiene vehículo asignado
    When el operador intenta iniciar la atención
    Then el sistema rechaza la acción
    And muestra un mensaje indicando que primero debe asignarse un vehículo
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Existen protocolos para cerrar una emergencia? | Un cierre indebido puede dejar emergencias sin resolver. | RNFP-08-01: "Un incidente solo debe cerrarse cuando el vehículo asignado haya completado la atención." |
| E – Económico | ¿Qué impacto tiene que un vehículo no se libere? | Reduce los recursos disponibles y afecta el puntaje. | RNFE-08-02: "El sistema debe liberar siempre el vehículo al finalizar la atención." |
| S – Social | ¿El proceso es claro para el operador? | Pasos confusos generan errores. | RFS-08-03: "El sistema debe indicar claramente las acciones permitidas según el estado del incidente." |
| T – Tecnológico | ¿Qué rendimiento se requiere? | Los cambios de estado deben reflejarse al instante. | RNFT-08-04: "Los cambios de estado de incidente y vehículo deben aplicarse de forma atómica y segura entre hilos." |
| L – Legal | ¿Se requiere trazabilidad? | Debe auditarse el ciclo de vida del incidente. | RNFL-08-05: "El sistema debe registrar el inicio y el fin de cada atención con fecha y hora." |
| E – Ético | ¿La información es veraz? | Marcar como resuelto algo no resuelto engaña al operador. | RNFET-08-06: "El sistema no debe permitir marcar un incidente como resuelto sin haber sido atendido." |

---

## RF-09 – Gestionar rutas y desplazamientos

### Gherkin

```gherkin
Feature: Gestionar rutas y desplazamientos

  Como operador del centro de emergencias
  Quiero que el sistema represente las conexiones entre zonas y determine rutas hacia los incidentes
  Para que los vehículos lleguen a las emergencias por un camino válido

  Scenario: Calcular la ruta hacia un incidente (caso feliz)
    Given existe un vehículo asignado y una ruta que conecta su zona con la del incidente
    When el sistema calcula el desplazamiento
    Then el sistema determina la secuencia de zonas que recorrerá el vehículo
    And la ruta se muestra en el mapa

  Scenario: No existe ruta hacia el incidente (alternativo)
    Given la zona del incidente no está conectada con la del vehículo
    When el sistema intenta calcular el desplazamiento
    Then el sistema informa que no existe ruta disponible
    And sugiere seleccionar otro vehículo
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Existen normas de tránsito que condicionen las rutas? | Los vehículos de emergencia tienen prioridad vial en la realidad. | RNFP-09-01: "El sistema debe usar únicamente las vías definidas en la configuración del mapa." |
| E – Económico | ¿Qué impacto tiene una ruta ineficiente? | Mayor tiempo de respuesta y menor puntaje. | RNFE-09-02: "El sistema debe calcular rutas que minimicen el costo total del recorrido." |
| S – Social | ¿Se favorecen ciertas zonas? | Rutas mal modeladas pueden dejar zonas aisladas. | RFS-09-03: "El sistema debe permitir llegar a cualquier zona conectada de la ciudad." |
| T – Tecnológico | ¿Qué estructura de datos se necesita? | Se necesita un grafo ponderado. | RNFT-09-04: "El mapa debe representarse como un grafo con zonas como nodos y vías como aristas." |
| L – Legal | ¿Se debe registrar el recorrido? | Útil para revisar el desempeño. | RNFL-09-05: "El sistema debe registrar el recorrido realizado por cada vehículo." |
| E – Ético | ¿La ruta mostrada es veraz? | Mostrar una ruta distinta a la real confunde al operador. | RNFET-09-06: "La ruta mostrada debe corresponder a la ruta que realmente sigue el vehículo." |

---

## RF-10 – Analizar la red vial

### Gherkin

```gherkin
Feature: Analizar la red vial

  Como operador del centro de emergencias
  Quiero verificar la conectividad del mapa y encontrar rutas de menor costo entre vehículos e incidentes
  Para detectar zonas aisladas y elegir el mejor recorrido

  Scenario: Verificar que el mapa es conexo (caso feliz)
    Given todas las zonas del mapa están conectadas entre sí
    When el operador solicita el análisis de conectividad
    Then el sistema informa que el mapa es completamente conexo

  Scenario: Identificar zonas desconectadas (alternativo)
    Given el mapa tiene una zona sin vías hacia el resto de la ciudad
    When el operador solicita el análisis de conectividad
    Then el sistema identifica los componentes desconectados
    And muestra las zonas afectadas en el mapa

  Scenario: Calcular la ruta de menor costo
    Given existen un vehículo y un incidente en zonas conectadas
    When el operador solicita la ruta de menor costo
    Then el sistema muestra la ruta de menor costo y su valor total
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Hay normas sobre planeación vial que afecten el análisis? | En la realidad, la red vial está regulada por entidades de tránsito. | RNFP-10-01: "El análisis debe basarse únicamente en la red vial cargada desde el JSON." |
| E – Económico | ¿Qué impacto económico tiene un análisis lento? | Algoritmos ineficientes consumen recursos. | RNFE-10-02: "El análisis de rutas debe usar algoritmos de complejidad adecuada (por ejemplo Dijkstra)." |
| S – Social | ¿Se identifican zonas que quedarían desatendidas? | Zonas aisladas afectan a la población simulada. | RFS-10-03: "El sistema debe mostrar claramente las zonas sin conexión." |
| T – Tecnológico | ¿Qué rendimiento se requiere? | El análisis se repite con cada asignación. | RNFT-10-04: "El cálculo de rutas debe completarse en un tiempo que no interrumpa la simulación." |
| L – Legal | ¿Se requiere trazabilidad? | Permite verificar los resultados. | RNFL-10-05: "El sistema debe poder registrar el resultado de cada análisis de conectividad." |
| E – Ético | ¿Los resultados son veraces? | Un análisis incorrecto llevaría a decisiones erradas. | RNFET-10-06: "El análisis de conectividad debe reflejar fielmente la estructura del mapa." |

---

## RF-11 – Consultar información de movilidad

### Gherkin

```gherkin
Feature: Consultar información de movilidad

  Como operador del centro de emergencias
  Quiero consultar distancias y tiempos estimados de respuesta entre puntos del mapa
  Para decidir con mejor criterio qué vehículo asignar

  Scenario: Consultar el tiempo estimado de respuesta (caso feliz)
    Given existen un vehículo y un incidente conectados por al menos una ruta
    When el operador consulta el tiempo estimado entre ambos
    Then el sistema muestra la distancia y el tiempo estimado de llegada

  Scenario: Consultar entre puntos sin conexión (alternativo)
    Given los dos puntos no están conectados en el mapa
    When el operador consulta el tiempo estimado
    Then el sistema informa que no es posible calcular el tiempo
    And muestra un mensaje claro indicando el motivo
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Hay normas de velocidad que afecten los tiempos? | Los límites de velocidad condicionan los tiempos reales. | RNFP-11-01: "El cálculo de tiempos debe usar las velocidades y costos definidos en la configuración." |
| E – Económico | ¿Genera costos computacionales? | Calcular todas las rutas en cada consulta puede ser costoso. | RNFE-11-02: "El sistema debe reutilizar cálculos de distancias ya realizados cuando el mapa no cambie." |
| S – Social | ¿Los tiempos son comprensibles? | Unidades poco claras confunden al usuario. | RFS-11-03: "El sistema debe mostrar los tiempos con unidades claras." |
| T – Tecnológico | ¿Qué precisión se requiere? | Estimaciones erróneas llevan a malas asignaciones. | RNFT-11-04: "Los tiempos estimados deben calcularse con el mismo algoritmo que las rutas de desplazamiento." |
| L – Legal | ¿Se almacena información personal? | No; solo datos del mapa. | RNFL-11-05: "La consulta de movilidad no debe almacenar datos personales." |
| E – Ético | ¿La información es veraz? | Los tiempos mostrados deben coincidir con los reales. | RNFET-11-06: "El tiempo estimado mostrado debe ser consistente con el tiempo real de la simulación." |

---

## RF-12 – Visualizar corredores de emergencia

### Gherkin

```gherkin
Feature: Visualizar corredores de emergencia

  Como operador del centro de emergencias
  Quiero ver en el mapa una red mínima de vías que mantenga conectadas todas las zonas
  Para identificar las vías esenciales para la movilidad de emergencia

  Scenario: Mostrar los corredores de emergencia (caso feliz)
    Given el mapa es conexo
    When el operador activa la visualización de corredores de emergencia
    Then el sistema resalta en el mapa la red mínima que conecta todas las zonas
    And muestra el costo total de la red

  Scenario: Mapa no conexo (alternativo)
    Given el mapa tiene zonas desconectadas
    When el operador activa la visualización de corredores
    Then el sistema muestra los corredores de cada componente conexo
    And advierte que no es posible conectar toda la ciudad
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Existen normas sobre vías de evacuación o emergencia? | En la realidad, existen corredores definidos por las autoridades. | RNFP-12-01: "Los corredores deben calcularse únicamente con las vías definidas en el mapa del sistema." |
| E – Económico | ¿Optimiza recursos? | Una red mínima reduce el costo total de vías usadas. | RNFE-12-02: "El sistema debe calcular la red mínima que conecte las zonas (árbol de expansión mínima)." |
| S – Social | ¿La visualización es accesible? | Colores poco visibles dificultan su lectura. | RFS-12-03: "Los corredores deben resaltarse con un color o grosor claramente distinguible." |
| T – Tecnológico | ¿Qué algoritmo se requiere? | Requiere un algoritmo como Kruskal o Prim. | RNFT-12-04: "El sistema debe usar un algoritmo de árbol de expansión mínima para calcular los corredores." |
| L – Legal | ¿Se requiere trazabilidad? | No es indispensable. | RNFL-12-05: "El sistema debe poder registrar cuándo el operador consulta los corredores." |
| E – Ético | ¿La información es veraz? | Debe corresponder al mapa real cargado. | RNFET-12-06: "Los corredores mostrados deben reflejar el estado actual del mapa." |

---

## RF-13 – Actualizar indicadores en tiempo real

### Gherkin

```gherkin
Feature: Actualizar indicadores en tiempo real

  Como operador del centro de emergencias
  Quiero ver actualizados el total de incidentes activos, accidentes, robos, incendios, vehículos disponibles y puntaje
  Para conocer el estado de la ciudad de un vistazo

  Scenario: Actualizar indicadores al generarse un incidente (caso feliz)
    Given el Centro de Monitoreo muestra los indicadores actuales
    When se genera un nuevo incendio
    Then el indicador de incidentes activos aumenta en uno
    And el indicador de incendios aumenta en uno

  Scenario: Actualizar indicadores al resolver un incidente
    Given existe un incidente en atención
    When el operador finaliza la atención
    Then el indicador de incidentes activos disminuye en uno
    And el indicador de vehículos disponibles aumenta en uno
    And el puntaje se actualiza
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Hay normas de transparencia sobre la información mostrada? | Los centros de monitoreo deben mostrar datos exactos. | RNFP-13-01: "Los indicadores deben calcularse a partir del estado real de la simulación." |
| E – Económico | ¿Genera costos computacionales? | Refrescar constantemente consume recursos. | RNFE-13-02: "Los indicadores solo deben actualizarse cuando cambie el dato correspondiente." |
| S – Social | ¿Los indicadores son claros? | Números poco visibles dificultan la lectura. | RFS-13-03: "Los indicadores deben presentarse con etiquetas claras y tamaño legible." |
| T – Tecnológico | ¿Qué rendimiento se requiere? | Requiere actualización inmediata sin bloquear la interfaz. | RNFT-13-04: "Los indicadores deben actualizarse de forma segura entre hilos, mediante eventos." |
| L – Legal | ¿Se requiere trazabilidad? | No maneja datos personales. | RNFL-13-05: "El sistema no debe almacenar datos personales en los indicadores." |
| E – Ético | ¿Los datos son veraces? | Un indicador desactualizado engaña al operador. | RNFET-13-06: "Los indicadores mostrados deben ser consistentes con la lista real de incidentes y vehículos." |

---

## RF-14 – Calcular el puntaje del operador

### Gherkin

```gherkin
Feature: Calcular el puntaje del operador

  Como operador del centro de emergencias
  Quiero recibir puntos cuando resuelvo correctamente los incidentes
  Para medir mi desempeño durante la simulación

  Scenario: Sumar puntos al resolver un incidente (caso feliz)
    Given un incidente fue atendido por un vehículo compatible
    When el operador finaliza la atención
    Then el sistema suma al puntaje los puntos definidos para ese tipo y gravedad
    And el indicador de puntaje se actualiza

  Scenario: No sumar puntos por una atención incorrecta (alternativo)
    Given un incidente se cerró sin una atención válida
    When el sistema calcula el puntaje
    Then el sistema no suma puntos por ese incidente
    And el puntaje permanece igual
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Existen reglas externas sobre la evaluación del desempeño? | Las reglas son propias de la simulación. | RNFP-14-01: "Las reglas de puntuación deben estar documentadas y ser aplicadas de manera uniforme." |
| E – Económico | ¿Genera costos? | No implica costos directos. | RNFE-14-02: "El cálculo del puntaje debe ser simple y no afectar el rendimiento de la simulación." |
| S – Social | ¿El puntaje es justo para todos los operadores? | Reglas confusas pueden frustrar al usuario. | RFS-14-03: "El sistema debe explicar al operador cómo se obtiene el puntaje." |
| T – Tecnológico | ¿Qué rendimiento se requiere? | Debe actualizarse al instante. | RNFT-14-04: "El puntaje debe actualizarse de forma segura ante acceso concurrente." |
| L – Legal | ¿Se requiere trazabilidad? | Permite auditar cómo se obtuvo cada punto. | RNFL-14-05: "El sistema debe registrar los puntos obtenidos por cada incidente resuelto." |
| E – Ético | ¿El puntaje puede manipularse? | Modificaciones externas invalidarían el resultado. | RNFET-14-06: "El puntaje solo debe modificarse mediante las reglas del sistema." |

---

## RF-15 – Gestionar la configuración inicial mediante JSON

### Gherkin

```gherkin
Feature: Gestionar la configuración inicial mediante JSON

  Como operador del centro de emergencias
  Quiero cargar desde archivos JSON la configuración del mapa, las rutas y los vehículos
  Para iniciar la simulación con datos válidos sin modificar el código

  Scenario: Cargar una configuración válida (caso feliz)
    Given existen los archivos JSON de mapa, rutas y vehículos con estructura correcta
    When el sistema inicia la carga de la configuración
    Then el sistema carga el mapa, las rutas y los vehículos
    And la simulación queda lista para iniciar

  Scenario: Archivo JSON inexistente (alternativo)
    Given el archivo de configuración del mapa no existe
    When el sistema intenta cargarlo
    Then el sistema no inicia la simulación
    And muestra un mensaje indicando qué archivo falta

  Scenario: Datos inconsistentes (alternativo)
    Given una ruta del JSON referencia una zona que no existe
    When el sistema valida la configuración
    Then el sistema rechaza la carga
    And informa cuál referencia es inválida
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Hay normas sobre el formato de datos? | JSON es un estándar abierto. | RNFP-15-01: "Los archivos de configuración deben seguir el estándar JSON." |
| E – Económico | ¿Genera costos? | No requiere licencias; usa librerías gratuitas. | RNFE-15-02: "El sistema debe usar una librería JSON de código abierto." |
| S – Social | ¿Es fácil de modificar para el usuario? | Permite adaptar la simulación sin programar. | RFS-15-03: "El formato de los archivos JSON debe estar documentado para que otros puedan modificarlos." |
| T – Tecnológico | ¿Qué validaciones se requieren? | Datos corruptos podrían cerrar la aplicación. | RNFT-15-04: "El sistema debe validar existencia, estructura y consistencia de los datos antes de cargarlos." |
| L – Legal | ¿Hay datos personales? | No. | RNFL-15-05: "Los archivos de configuración no deben contener datos personales." |
| E – Ético | ¿Los mensajes de error son transparentes? | Errores vagos dificultan corregir el archivo. | RNFET-15-06: "El sistema debe informar con claridad el motivo del rechazo de la configuración." |

---

## RF-16 – Gestionar la persistencia de la simulación

### Gherkin

```gherkin
Feature: Gestionar la persistencia de la simulación

  Como operador del centro de emergencias
  Quiero guardar y recuperar el estado de la simulación mediante serialización
  Para continuar una partida anterior sin perder mi progreso

  Scenario: Guardar el estado de la simulación (caso feliz)
    Given la simulación está en ejecución
    When el operador selecciona "Guardar"
    Then el sistema serializa el estado completo en un archivo
    And muestra un mensaje de guardado exitoso

  Scenario: Recuperar una simulación guardada
    Given existe un archivo de simulación guardado
    When el operador selecciona "Cargar"
    Then el sistema restaura incidentes, vehículos, puntaje e historial
    And la simulación continúa desde el punto guardado

  Scenario: Archivo guardado dañado (alternativo)
    Given el archivo guardado está corrupto
    When el operador intenta cargarlo
    Then el sistema no restaura la simulación
    And muestra un mensaje de error sin cerrar la aplicación
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Existen normas sobre almacenamiento de información? | Aplican normas generales de manejo de archivos. | RNFP-16-01: "El sistema debe guardar el estado únicamente en la ubicación definida para la aplicación." |
| E – Económico | ¿Genera costos de almacenamiento? | Archivos grandes ocupan espacio en disco. | RNFE-16-02: "El archivo de guardado debe contener solo la información necesaria para reanudar la simulación." |
| S – Social | ¿Todos los usuarios pueden recuperar su progreso? | Perder el progreso frustra al usuario. | RFS-16-03: "El sistema debe informar claramente si el guardado o la carga fue exitoso." |
| T – Tecnológico | ¿Qué tecnología se usa? | Serialización de objetos de Java. | RNFT-16-04: "El sistema debe usar la serialización de Java y declarar las clases serializables." |
| L – Legal | ¿Se almacenan datos personales? | Solo datos de la simulación. | RNFL-16-05: "El archivo guardado no debe incluir datos personales del usuario." |
| E – Ético | ¿La información restaurada es fiel? | Una carga incorrecta alteraría la simulación. | RNFET-16-06: "El estado restaurado debe ser idéntico al estado guardado." |

---

## RF-17 – Gestionar el historial de la simulación

### Gherkin

```gherkin
Feature: Gestionar el historial de la simulación

  Como operador del centro de emergencias
  Quiero que se registren mis acciones y poder consultar la última acción ejecutada
  Para revisar lo que hice y poder corregir errores

  Scenario: Registrar una acción del operador (caso feliz)
    Given la simulación está en ejecución
    When el operador asigna un vehículo a un incidente
    Then el sistema registra la acción en el historial con su fecha y hora

  Scenario: Consultar la última acción
    Given el historial contiene acciones registradas
    When el operador consulta la última acción
    Then el sistema muestra la acción más reciente

  Scenario: Consultar el historial vacío (alternativo)
    Given el operador no ha ejecutado ninguna acción
    When el operador consulta la última acción
    Then el sistema informa que no hay acciones registradas
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Hay normas sobre registro de operaciones? | Las auditorías exigen registro de acciones. | RNFP-17-01: "El sistema debe registrar todas las acciones relevantes del operador." |
| E – Económico | ¿Genera costos de almacenamiento? | El historial crece durante la ejecución. | RNFE-17-02: "El historial debe usar una estructura que permita acceder a la última acción de forma eficiente (por ejemplo una pila)." |
| S – Social | ¿Es comprensible para el usuario? | Descripciones técnicas dificultan su lectura. | RFS-17-03: "Las acciones del historial deben describirse con lenguaje claro." |
| T – Tecnológico | ¿Qué rendimiento se requiere? | Consultar la última acción debe ser inmediato. | RNFT-17-04: "La consulta de la última acción debe tener tiempo de respuesta constante." |
| L – Legal | ¿Se requiere trazabilidad? | Es el objetivo principal del requisito. | RNFL-17-05: "Cada acción registrada debe incluir tipo, elementos involucrados y fecha y hora." |
| E – Ético | ¿Se respeta la privacidad? | El historial no debe exponer datos personales. | RNFET-17-06: "El historial no debe almacenar datos personales del operador." |

---

## RF-18 – Gestionar eventos de la simulación

### Gherkin

```gherkin
Feature: Gestionar eventos de la simulación

  Como operador del centro de emergencias
  Quiero que se registren y procesen los eventos de generación y resolución de incidentes y cambios de estado de vehículos
  Para mantener la interfaz y los indicadores siempre actualizados

  Scenario: Procesar un evento de generación de incidente (caso feliz)
    Given la simulación está en ejecución y hay escuchas registradas
    When se genera un nuevo incidente
    Then el sistema registra el evento y lo procesa
    And la interfaz y los indicadores se actualizan

  Scenario: Procesar un cambio de estado de vehículo
    Given un vehículo está asignado a un incidente
    When el vehículo llega a la ubicación del incidente
    Then el sistema registra el evento de cambio de estado
    And el mapa muestra el nuevo estado del vehículo
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Hay normas sobre el procesamiento de eventos? | No existen normas externas específicas. | RNFP-18-01: "El sistema debe procesar los eventos en el orden en que ocurren." |
| E – Económico | ¿Optimiza recursos? | Un manejo por eventos evita refrescar todo constantemente. | RNFE-18-02: "El sistema debe actualizar la interfaz solo cuando ocurra un evento relevante." |
| S – Social | ¿Afecta negativamente a algún usuario? | Eventos perdidos dejan la interfaz desactualizada. | RFS-18-03: "El sistema no debe perder eventos que afecten lo mostrado al operador." |
| T – Tecnológico | ¿Qué tecnología se requiere? | Patrón de observador o colas de eventos. | RNFT-18-04: "El sistema debe usar un mecanismo de eventos desacoplado entre lógica e interfaz." |
| L – Legal | ¿Se requiere trazabilidad? | Facilita depurar la simulación. | RNFL-18-05: "El sistema debe registrar los eventos procesados." |
| E – Ético | ¿La información de eventos es veraz? | Eventos incorrectos generarían indicadores erróneos. | RNFET-18-06: "Cada evento debe reflejar fielmente el cambio ocurrido en la simulación." |

---

## RF-19 – Gestionar errores de ejecución

### Gherkin

```gherkin
Feature: Gestionar errores de ejecución

  Como operador del centro de emergencias
  Quiero recibir mensajes claros cuando ocurra una situación inválida y que la aplicación no se cierre
  Para corregir el error y continuar con la simulación

  Scenario: Controlar una acción inválida (caso feliz)
    Given el operador intenta asignar un vehículo ya ocupado
    When el sistema valida la acción
    Then el sistema lanza una excepción controlada
    And muestra un mensaje claro al operador
    And la aplicación sigue en ejecución

  Scenario: Controlar un error de carga de archivo
    Given un archivo requerido no existe o está dañado
    When el sistema intenta leerlo
    Then el sistema captura la excepción
    And informa el problema al usuario sin cerrarse inesperadamente
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Existen normas sobre el manejo de fallas? | Los sistemas críticos deben tolerar fallas. | RNFP-19-01: "El sistema debe manejar las situaciones anómalas mediante excepciones definidas por el equipo." |
| E – Económico | ¿Qué impacto tiene un cierre inesperado? | Se pierde el progreso y el puntaje. | RNFE-19-02: "El sistema debe evitar cierres inesperados ante errores controlables." |
| S – Social | ¿Los mensajes son claros para el público? | Mensajes técnicos confunden al usuario. | RFS-19-03: "Los mensajes de error deben estar en lenguaje comprensible, sin términos técnicos." |
| T – Tecnológico | ¿Qué tecnología se requiere? | Uso de excepciones personalizadas de Java. | RNFT-19-04: "El sistema debe usar excepciones personalizadas para cada situación anómala." |
| L – Legal | ¿Se requiere trazabilidad? | Los errores deben poder revisarse. | RNFL-19-05: "El sistema debe registrar los errores ocurridos para su análisis." |
| E – Ético | ¿Se informa con transparencia? | Ocultar errores genera desconfianza. | RNFET-19-06: "El sistema debe informar siempre al usuario cuando una acción no pudo realizarse." |

---

## RF-20 – Ejecutar procesos concurrentes

### Gherkin

```gherkin
Feature: Ejecutar procesos concurrentes

  Como operador del centro de emergencias
  Quiero que la generación de eventos y la actualización de información se ejecuten concurrentemente
  Para que la simulación fluya sin congelar la interfaz

  Scenario: Ejecutar procesos en paralelo (caso feliz)
    Given la simulación está en ejecución
    When el generador de eventos y la actualización de información corren a la vez
    Then la interfaz sigue respondiendo a las acciones del operador
    And los datos se actualizan sin bloquear la pantalla

  Scenario: Acceso simultáneo a un recurso compartido
    Given dos hilos intentan modificar la lista de incidentes al mismo tiempo
    When ambos acceden al recurso compartido
    Then el sistema sincroniza el acceso
    And la lista queda consistente, sin datos perdidos ni duplicados
```

### Análisis PESTLE

| Dimensión | Pregunta | Impacto | Requerimiento derivado |
|---|---|---|---|
| P – Político | ¿Hay normas sobre el desempeño de sistemas críticos? | Los sistemas de emergencia deben ser confiables y disponibles. | RNFP-20-01: "El sistema debe mantener su operación continua durante la ejecución de múltiples hilos." |
| E – Económico | ¿Optimiza recursos? | Los hilos aprovechan mejor el procesador. | RNFE-20-02: "El sistema debe usar un número limitado de hilos para no sobrecargar el equipo." |
| S – Social | ¿Afecta la experiencia del usuario? | Una interfaz congelada frustra al operador. | RFS-20-03: "La interfaz no debe bloquearse mientras se ejecutan procesos en segundo plano." |
| T – Tecnológico | ¿Qué tecnología se requiere? | Hilos y mecanismos de sincronización de Java. | RNFT-20-04: "El sistema debe proteger los recursos compartidos con mecanismos de sincronización." |
| L – Legal | ¿Se requiere trazabilidad? | Facilita detectar condiciones de carrera. | RNFL-20-05: "El sistema debe registrar los errores ocurridos en los hilos." |
| E – Ético | ¿Se garantiza información veraz? | Condiciones de carrera pueden mostrar datos incorrectos. | RNFET-20-06: "El sistema debe garantizar que los datos mostrados sean consistentes pese a la concurrencia." |
