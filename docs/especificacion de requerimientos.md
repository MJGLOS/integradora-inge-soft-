# Análisis del problema
 
## Información general
 
| Campo | Descripción |
|---|---|
| Cliente | Alcaldía de Palmira — administración municipal |
| Usuario | Operador del centro de monitoreo urbano |
| Contexto del problema | Palmira ha crecido en población, expansión urbana y circulación vehicular, lo que genera congestión en vías principales e incidentes de tránsito, robos e incendios que requieren atención oportuna. La ciudad no cuenta con una herramienta centralizada que integre el monitoreo de incidentes, rutas, vehículos disponibles y capacidad de respuesta, lo que dificulta la toma de decisiones rápidas. Se requiere un prototipo de escritorio en Java con interfaz gráfica que simule el comportamiento de la ciudad sobre un mapa 2D, registre incidentes, los priorice por gravedad y apoye la asignación eficiente de vehículos de atención |
| Requerimientos funcionales | - RF1 - Gestionar los escenarios del sistema <br> - RF2 - Representar el mapa de la ciudad <br> - **RF3 - Gestionar incidentes** <br> - RF4 - Generar incidentes <br> - **RF5 - Gestionar la prioridad de los incidentes** <br> - **RF6 - Gestionar vehículos de atención** <br> - **RF7 - Asignar vehículos a incidentes** <br> - RF8 - Gestionar la atención de incidentes <br> - RF9 - Gestionar rutas y desplazamientos <br> - RF10 - Analizar la red vial <br> - RF11 - Consultar información de movilidad <br> - RF12 - Visualizar corredores de emergencia <br> - RF13 - Actualizar indicadores en tiempo real <br> - RF14 - Calcular el puntaje del operador <br> - RF15 - Gestionar la configuración inicial mediante JSON <br> - RF16 - Gestionar la persistencia de la simulación <br> - RF17 - Gestionar el historial de la simulación <br> - RF18 - Gestionar eventos de la simulación <br> - RF19 - Gestionar errores de ejecución <br> - RF20 - Ejecutar procesos concurrentes <br><br> *Los requerimientos en negrilla corresponden al alcance de la Entrega 1 de Ingeniería de Software II* |
| Requerimientos no funcionales | - RNF1 - Desarrollo en Java sobre la versión definida para el curso <br> - RNF2 - Interfaz gráfica clara y consistente entre escenarios <br> - RNF3 - Mensajes claros al usuario ante operaciones exitosas y fallidas <br> - RNF4 - Indicadores y elementos visuales actualizados según el estado de la simulación <br> - RNF5 - Consistencia de la información entre las distintas estructuras de datos que gestionan un mismo elemento <br> - RNF6 - Manejo de excepciones que evite cierres inesperados y preserve el estado consistente <br> - RNF7 - Recuperación consistente del estado de una simulación almacenada <br> - RNF8 - Buenas prácticas de POO y nomenclatura clara y consistente <br> - RNF9 - Implementación bajo el patrón MVC con separación clara de responsabilidades <br> - RNF10 - Estructuras de datos propias y genéricas, sin ArrayList, LinkedList, HashMap, TreeMap, PriorityQueue, Stack ni ArrayDeque de Java <br> - RNF11 - Código escrito en inglés y organizado en paquetes <br> - RNF12 - Persistencia mediante archivos JSON y serialización de objetos <br> - RNF13 - Pruebas unitarias en JUnit ejecutables independientemente de la interfaz gráfica <br> - RNF14 - Sin bases de datos, conexión a internet, GPS, APIs externas ni mapas reales |
| Requerimientos de proceso | - RP1 - Uso de git y GitHub Classroom para el control de versiones <br> - RP2 - Uso de ramas bajo modelo Gitflow (main, dev, feat/) <br> - RP3 - Registro de 15 commits equi-temporales con indicadores de calidad <br> - RP4 - Documentación del proyecto en Readme.md <br> - RP5 - Inclusión de carpeta /doc con los artefactos del proyecto en formato markdown <br> - RP6 - Elaboración del diagrama de clases UML en Visual Paradigm (imagen PDF y fuentes .vpp) <br> - RP7 - Diseño de casos de prueba funcionales y unitarios <br> - RP8 - Desarrollo colaborativo en equipos de 3 personas <br> - RP9 - Especificación de TAD y análisis de complejidad temporal y espacial <br> - RP10 - Desarrollo incremental por entregas según cronograma <br> - RP11 - Coherencia verificada entre requerimientos, diagrama de clases y casos de prueba |
 
---
 
# Requerimiento Funcional RF3 Gestionar incidentes
 
## Información del requerimiento
 
| Campo | Descripción |
|---|---|
| Identificador y nombre | RF3 - Gestionar incidentes |
| Resumen | Permitir registrar, consultar y actualizar incidentes de tipo accidente, robo e incendio, almacenando su identificador, ubicación en el mapa, gravedad, fecha y hora de generación, descripción, estado y vehículo asignado |
 
---
 
## Entradas
 
| Nombre entrada | Tipo de dato | Condición valores válidos |
|---|---|---|
| identificador del incidente | String | único, no vacío |
| tipo de incidente | IncidentType | ACCIDENT, ROBBERY o FIRE |
| ubicación | Location | coordenadas (x,y) dentro de los límites del mapa y sobre una celda transitable |
| gravedad | Severity | HIGH, MEDIUM o LOW |
| descripción | String | no vacía |
| fecha y hora de generación | LocalDateTime | no nula, no posterior al instante actual |
 
---
 
## Salidas
 
| Nombre salida | Tipo de dato | Formato |
|---|---|---|
| incidente registrado | Incident | objeto con estado PENDING y sin vehículo asignado |
| incidente consultado | Incident | datos completos del incidente |
| mensaje de confirmación o error | String | texto |
 
---
 
## Resultado o Postcondición
 
| Resultado o Postcondición |
|---|
| El incidente queda almacenado de forma consistente en el índice por ID y en la estructura de prioridad, con estado PENDING y sin vehículo asignado, y puede ser consultado y actualizado posteriormente. Si el identificador ya existe, el incidente no se registra y se informa el error mediante DuplicateIdException. Si los datos de entrada son inválidos (ubicación fuera del mapa o descripción vacía) se lanza InvalidIncidentDataException. Si se consulta un identificador inexistente se lanza IncidentNotFoundException y el sistema continúa su ejecución |
 
---
 
# Requerimiento Funcional RF5 Gestionar la prioridad de los incidentes
 
## Información del requerimiento
 
| Campo | Descripción |
|---|---|
| Identificador y nombre | RF5 - Gestionar la prioridad de los incidentes |
| Resumen | Organizar los incidentes activos según su gravedad, aplicando la fecha y hora de generación como criterio de desempate, y permitir consultar y atender el incidente de mayor prioridad |
 
---
 
## Entradas
 
| Nombre entrada | Tipo de dato | Condición valores válidos |
|---|---|---|
| conjunto de incidentes activos | Estructura de prioridad de Incident | no vacía para la consulta del más prioritario |
| criterio de orden | Comparator\<Incident\> | gravedad descendente (HIGH > MEDIUM > LOW) y, ante empate, fecha y hora ascendente |
| solicitud de atención del más prioritario | evento | válido |
 
---
 
## Salidas
 
| Nombre salida | Tipo de dato | Formato |
|---|---|---|
| incidente de mayor prioridad | Incident | objeto con sus datos completos |
| listado ordenado de incidentes activos | Incident[] | secuencia ordenada por prioridad |
| mensaje de error | String | texto |
 
---
 
## Resultado o Postcondición
 
| Resultado o Postcondición |
|---|
| El sistema retorna el incidente activo de mayor gravedad y, cuando dos incidentes tienen la misma gravedad, retorna el más antiguo. La consulta no modifica el estado de las estructuras. Al atender el incidente más prioritario, este pasa a estado IN_PROGRESS. Si no existen incidentes activos, la operación lanza EmptyStructureException, informa la condición al usuario y no altera el sistema |
 
---
 
# Requerimiento Funcional RF6 Gestionar vehículos de atención
 
## Información del requerimiento
 
| Campo | Descripción |
|---|---|
| Identificador y nombre | RF6 - Gestionar vehículos de atención |
| Resumen | Permitir registrar y consultar los vehículos de atención, su tipo, ubicación y estado, así como controlar los cambios de estado producidos durante la atención de un incidente |
 
---
 
## Entradas
 
| Nombre entrada | Tipo de dato | Condición valores válidos |
|---|---|---|
| identificador del vehículo | String | único, no vacío |
| tipo de vehículo | VehicleType | PATROL, AMBULANCE o FIRE_TRUCK |
| ubicación | Location | coordenadas (x,y) dentro de los límites del mapa |
| estado | VehicleStatus | AVAILABLE, EN_ROUTE, ATTENDING u OUT_OF_SERVICE |
 
---
 
## Salidas
 
| Nombre salida | Tipo de dato | Formato |
|---|---|---|
| vehículo registrado | Vehicle | objeto con estado AVAILABLE |
| vehículo consultado | Vehicle | datos completos del vehículo |
| listado de vehículos disponibles | Vehicle[] | secuencia |
| mensaje de confirmación o error | String | texto |
 
---
 
## Resultado o Postcondición
 
| Resultado o Postcondición |
|---|
| El vehículo queda registrado en el índice por ID con estado AVAILABLE y puede ser consultado por su identificador. Los cambios de estado producidos durante la atención de un incidente quedan reflejados en el vehículo y en los indicadores del sistema. Si el identificador ya existe, el vehículo no se registra y se informa el error mediante DuplicateIdException. Si se consulta un identificador inexistente se lanza VehicleNotFoundException |
 
---
 
# Requerimiento Funcional RF7 Asignar vehículos a incidentes
 
## Información del requerimiento
 
| Campo | Descripción |
|---|---|
| Identificador y nombre | RF7 - Asignar vehículos a incidentes |
| Resumen | Permitir asignar un vehículo disponible a un incidente activo, validando la compatibilidad entre el tipo de vehículo y el tipo de incidente y evitando asignaciones no permitidas. El sistema debe proponer un vehículo candidato adecuado para la atención |
 
---
 
## Entradas
 
| Nombre entrada | Tipo de dato | Condición valores válidos |
|---|---|---|
| identificador del incidente | String | existente en el sistema, con estado distinto de RESOLVED y sin vehículo asignado |
| identificador del vehículo | String | existente en el sistema, con estado AVAILABLE y compatible con el tipo de incidente |
| confirmación del operador | evento | válida sobre el vehículo propuesto o sobre uno seleccionado manualmente |
 
---
 
## Salidas
 
| Nombre salida | Tipo de dato | Formato |
|---|---|---|
| vehículo candidato propuesto | Vehicle | objeto sugerido, compatible y disponible; null si no existe ninguno |
| asignación realizada | boolean | true/false |
| mensaje de confirmación o error | String | texto |
 
---
 
## Resultado o Postcondición
 
| Resultado o Postcondición |
|---|
| Cuando la asignación es válida, el incidente queda con el vehículo asignado y estado IN_PROGRESS, el vehículo queda asociado al incidente y deja de estar disponible, y ambos cambios se reflejan de forma consistente en las estructuras del modelo. Cuando la asignación es inválida el sistema no modifica ningún estado y lanza la excepción correspondiente: IncompatibleVehicleException si el tipo de vehículo no puede atender el tipo de incidente, VehicleNotAvailableException si el vehículo no está disponible, IncidentAlreadyResolvedException si el incidente ya fue resuelto, IncidentAlreadyAssignedException si el incidente ya tiene un vehículo asignado, e IncidentNotFoundException o VehicleNotFoundException si alguno de los identificadores no existe. En todos los casos la aplicación informa el error mediante un mensaje claro y continúa su ejecución |
 