# Diseño de Casos de Prueba Funcionales - SGMMS

## Configuración de los Escenarios

| Nombre | Clase | Escenario |
|---|---|---|
| setupStage1 | ControllerTest | Sistema inicializado sin incidentes ni vehículos registrados, con el mapa cargado (límites 0..9 en ambos ejes). |
| setupStage2 | ControllerTest | Sistema con el incidente INC-001 registrado: robo, gravedad MEDIA, ubicación (3,4), estado PENDIENTE, sin vehículo asignado. |
| setupStage3 | ControllerTest | Sistema con tres incidentes activos: INC-001 (robo, MEDIA, 10:00), INC-002 (incendio, ALTA, 10:05) e INC-003 (accidente, BAJA, 10:10). |
| setupStage4 | ControllerTest | Sistema con dos incidentes activos de gravedad ALTA: INC-004 generado a las 09:00 e INC-005 generado a las 09:30. |
| setupStage5 | ControllerTest | Sistema con tres vehículos registrados y disponibles: PAT-01 (patrulla, (1,1)), AMB-01 (ambulancia, (5,5)) y BOM-01 (camión de bomberos, (2,7)). |
| setupStage6 | ControllerTest | Sistema con los vehículos PAT-01 (patrulla, DISPONIBLE) y AMB-02 (ambulancia, ATENDIENDO, asociada al incidente INC-006). |
| setupStage7 | ControllerTest | Sistema con el incidente INC-010 (robo, MEDIA, PENDIENTE, sin vehículo) y los vehículos PAT-01 (patrulla, DISPONIBLE) y AMB-01 (ambulancia, DISPONIBLE). |
| setupStage8 | ControllerTest | Sistema con el incidente INC-011 (incendio, ALTA, PENDIENTE) y AMB-01 (ambulancia, DISPONIBLE) como único vehículo registrado. |
| setupStage9 | ControllerTest | Sistema con el incidente INC-012 (accidente, ALTA, RESUELTO) y AMB-01 (ambulancia, DISPONIBLE). |
| setupStage10 | ControllerTest | Sistema con el incidente INC-013 (robo, BAJA, PENDIENTE) y PAT-02 (patrulla) en estado ATENDIENDO. |
| setupStage11 | ControllerTest | Sistema con el incidente INC-014 (incendio, ALTA, PENDIENTE, ubicación (4,4)) y los vehículos BOM-01 (bomberos, DISPONIBLE), BOM-02 (bomberos, FUERA DE SERVICIO) y PAT-01 (patrulla, DISPONIBLE). |
| setupStage12 | ControllerTest | Sistema con el incidente INC-015 (accidente, MEDIA, EN PROCESO) con la ambulancia AMB-03 ya asignada, y AMB-04 (ambulancia, DISPONIBLE). |

---

## Diseño de Casos de Prueba

### Objetivo de la Prueba:
Validar que el sistema registre incidentes con datos válidos y rechace los registros con identificador duplicado o datos inválidos. **(RF3)**

| Clase | Método | Escenario | Valores de Entrada | Resultado esperado |
|---|---|---|---|---|
| Controller | registerIncident(id : String, type : IncidentType, x : int, y : int, description : String, severity : Severity) | setupStage1 | "INC-001", ROBBERY, (3,4), "Robo en zona comercial", MEDIUM | El incidente queda registrado con estado PENDIENTE, sin vehículo asignado y con fecha y hora de generación. listActiveIncidents() retorna 1 elemento. |
| Controller | registerIncident(...) | setupStage1 | "INC-002", FIRE, (6,2), "Incendio en bodega", HIGH | El incidente queda registrado con estado PENDIENTE. listActiveIncidents() retorna 1 elemento y consultIncident("INC-002") retorna el incidente creado. |
| Controller | registerIncident(...) | setupStage2 | "INC-001", FIRE, (6,2), "Incendio en bodega", HIGH | No se registra el incidente, se lanza DuplicateIdException y el total de incidentes activos sigue en 1. |
| Controller | registerIncident(...) | setupStage1 | "INC-003", ACCIDENT, (15,20), "Choque en vía principal", HIGH | No se registra el incidente, se lanza InvalidIncidentDataException porque la ubicación está fuera de los límites del mapa. El sistema continúa su ejecución. |
| Controller | registerIncident(...) | setupStage1 | "INC-004", ROBBERY, (3,4), "", LOW | No se registra el incidente, se lanza InvalidIncidentDataException porque la descripción está vacía. |

---

### Objetivo de la Prueba:
Validar que la consulta de incidentes por identificador retorne los datos correctos y controle los identificadores inexistentes. **(RF3)**

| Clase | Método | Escenario | Valores de Entrada | Resultado esperado |
|---|---|---|---|---|
| Controller | consultIncident(id : String) | setupStage2 | "INC-001" | Retorna el incidente con tipo ROBBERY, gravedad MEDIA, ubicación (3,4), estado PENDIENTE y sin vehículo asignado. |
| Controller | consultIncident(id : String) | setupStage2 | "INC-999" | Se lanza IncidentNotFoundException, el sistema informa el error y no se modifica ningún incidente registrado. |

---

### Objetivo de la Prueba:
Validar que la actualización de un incidente modifique únicamente los datos permitidos y rechace valores inválidos. **(RF3)**

| Clase | Método | Escenario | Valores de Entrada | Resultado esperado |
|---|---|---|---|---|
| Controller | updateIncidentDescription(id : String, description : String) | setupStage2 | "INC-001", "Robo reportado por un ciudadano en la zona comercial" | La descripción se actualiza y el tipo, la gravedad, la ubicación, el estado y la fecha de generación permanecen sin cambios. |
| Controller | updateIncidentDescription(id : String, description : String) | setupStage2 | "INC-001", "" | No se actualiza, se lanza InvalidIncidentDataException y la descripción original se conserva. |

---

### Objetivo de la Prueba:
Validar que el sistema identifique el incidente activo de mayor prioridad aplicando gravedad y, ante empate, antigüedad. **(RF5)**

| Clase | Método | Escenario | Valores de Entrada | Resultado esperado |
|---|---|---|---|---|
| Controller | getHighestPriorityIncident() | setupStage3 | No aplica | Retorna INC-002 por ser el único incidente de gravedad ALTA. |
| Controller | getHighestPriorityIncident() | setupStage4 | No aplica | Retorna INC-004, el más antiguo (09:00), aplicando el criterio de desempate por fecha y hora de generación. |
| Controller | getHighestPriorityIncident() | setupStage1 | No aplica | Se lanza EmptyStructureException porque no existen incidentes activos. El sistema informa la condición y continúa su ejecución. |

---

### Objetivo de la Prueba:
Validar que el listado de incidentes activos se entregue ordenado por prioridad y que la consulta no altere las estructuras. **(RF5)**

| Clase | Método | Escenario | Valores de Entrada | Resultado esperado |
|---|---|---|---|---|
| Controller | listActiveIncidents() | setupStage3 | No aplica | Retorna la secuencia [INC-002, INC-001, INC-003], ordenada de mayor a menor gravedad. |
| Controller | listActiveIncidents() | setupStage4 | No aplica | Retorna la secuencia [INC-004, INC-005], con el más antiguo en primer lugar ante igual gravedad. |
| Controller | getHighestPriorityIncident() | setupStage3 | Invocación repetida dos veces | Retorna INC-002 en ambas invocaciones y listActiveIncidents() sigue retornando 3 elementos: la consulta no modifica el estado del sistema. |

---

### Objetivo de la Prueba:
Validar que el sistema registre vehículos de atención válidos y rechace identificadores duplicados. **(RF6)**

| Clase | Método | Escenario | Valores de Entrada | Resultado esperado |
|---|---|---|---|---|
| Controller | registerVehicle(type : VehicleType, id : String, x : int, y : int) | setupStage1 | PATROL, "PAT-01", (1,1) | El vehículo queda registrado con estado DISPONIBLE y sin incidente asignado. listVehicles() retorna 1 elemento. |
| Controller | registerVehicle(...) | setupStage5 | AMBULANCE, "PAT-01", (2,2) | No se registra el vehículo, se lanza DuplicateIdException y el total de vehículos sigue en 3. |
| Controller | registerVehicle(...) | setupStage1 | FIRE_TRUCK, "BOM-01", (12,3) | No se registra el vehículo, se lanza InvalidVehicleDataException porque la ubicación está fuera de los límites del mapa. |

---

### Objetivo de la Prueba:
Validar la consulta de vehículos por identificador y el listado de vehículos disponibles. **(RF6)**

| Clase | Método | Escenario | Valores de Entrada | Resultado esperado |
|---|---|---|---|---|
| Controller | consultVehicle(id : String) | setupStage5 | "AMB-01" | Retorna el vehículo de tipo AMBULANCE, ubicación (5,5), estado DISPONIBLE y sin incidente asignado. |
| Controller | consultVehicle(id : String) | setupStage5 | "AMB-99" | Se lanza VehicleNotFoundException y el sistema continúa su ejecución. |
| Controller | listAvailableVehicles() | setupStage5 | No aplica | Retorna los 3 vehículos registrados: PAT-01, AMB-01 y BOM-01. |
| Controller | listAvailableVehicles() | setupStage6 | No aplica | Retorna únicamente PAT-01. AMB-02 no aparece porque su estado es ATENDIENDO. |

---

### Objetivo de la Prueba:
Validar que el estado de un vehículo cambie correctamente durante la atención de un incidente. **(RF6)**

| Clase | Método | Escenario | Valores de Entrada | Resultado esperado |
|---|---|---|---|---|
| Controller | assignVehicle(incidentId : String, vehicleId : String) | setupStage7 | "INC-010", "PAT-01" | PAT-01 pasa de DISPONIBLE a ATENDIENDO y queda asociado a INC-010. listAvailableVehicles() ya no lo incluye. |
| Controller | resolveIncident(incidentId : String) | setupStage7 (tras asignar PAT-01 a INC-010) | "INC-010" | PAT-01 vuelve al estado DISPONIBLE, queda sin incidente asociado y aparece nuevamente en listAvailableVehicles(). |

---

### Objetivo de la Prueba:
Validar que el sistema proponga un vehículo candidato compatible y disponible sin modificar el estado del sistema. **(RF7)**

| Clase | Método | Escenario | Valores de Entrada | Resultado esperado |
|---|---|---|---|---|
| Controller | suggestCandidateVehicle(incidentId : String) | setupStage11 | "INC-014" | Retorna BOM-01, único vehículo compatible con un incendio y disponible. BOM-02 se descarta por estar FUERA DE SERVICIO y PAT-01 por ser incompatible. El estado del incidente y de los vehículos no cambia. |
| Controller | suggestCandidateVehicle(incidentId : String) | setupStage8 | "INC-011" | Retorna null y el sistema informa que no hay vehículos compatibles disponibles, sin modificar ningún estado. |
| Controller | suggestCandidateVehicle(incidentId : String) | setupStage8 | "INC-999" | Se lanza IncidentNotFoundException. |

---

### Objetivo de la Prueba:
Validar que la asignación de un vehículo compatible y disponible a un incidente activo se realice correctamente. **(RF7)**

| Clase | Método | Escenario | Valores de Entrada | Resultado esperado |
|---|---|---|---|---|
| Controller | assignVehicle(incidentId : String, vehicleId : String) | setupStage7 | "INC-010", "PAT-01" | La asignación se realiza: INC-010 pasa a estado EN PROCESO con PAT-01 como vehículo asignado y PAT-01 pasa a ATENDIENDO asociado a INC-010. Retorna true. |
| Controller | assignVehicle(...) | setupStage11 | "INC-014", "BOM-01" | La asignación se realiza: INC-014 queda EN PROCESO con BOM-01 asignado y BOM-01 pasa a ATENDIENDO. |

---

### Objetivo de la Prueba:
Validar que el sistema rechace las asignaciones no permitidas y preserve el estado consistente. **(RF7)**

| Clase | Método | Escenario | Valores de Entrada | Resultado esperado |
|---|---|---|---|---|
| Controller | assignVehicle(incidentId : String, vehicleId : String) | setupStage8 | "INC-011", "AMB-01" | Se lanza IncompatibleVehicleException porque una ambulancia no puede atender un incendio. INC-011 sigue PENDIENTE y AMB-01 sigue DISPONIBLE. |
| Controller | assignVehicle(...) | setupStage10 | "INC-013", "PAT-02" | Se lanza VehicleNotAvailableException porque PAT-02 está ATENDIENDO. INC-013 sigue PENDIENTE y sin vehículo asignado. |
| Controller | assignVehicle(...) | setupStage9 | "INC-012", "AMB-01" | Se lanza IncidentAlreadyResolvedException porque el incidente ya fue resuelto. AMB-01 sigue DISPONIBLE. |
| Controller | assignVehicle(...) | setupStage12 | "INC-015", "AMB-04" | Se lanza IncidentAlreadyAssignedException porque INC-015 ya tiene asignada la ambulancia AMB-03. AMB-04 sigue DISPONIBLE y AMB-03 sigue asociada a INC-015. |
| Controller | assignVehicle(...) | setupStage7 | "INC-999", "PAT-01" | Se lanza IncidentNotFoundException. PAT-01 sigue DISPONIBLE. |
| Controller | assignVehicle(...) | setupStage7 | "INC-010", "PAT-99" | Se lanza VehicleNotFoundException. INC-010 sigue PENDIENTE y sin vehículo asignado. |

---

## Matriz de cobertura de requerimientos

| Requerimiento | Casos válidos | Casos inválidos |
|---|---|---|
| RF3 - Gestionar incidentes | Registro de incidente con datos válidos, consulta por identificador existente, actualización de descripción | Identificador duplicado, ubicación fuera del mapa, descripción vacía, consulta de identificador inexistente, actualización con descripción vacía |
| RF5 - Gestionar la prioridad de los incidentes | Consulta del más prioritario por gravedad, consulta del más prioritario con desempate por antigüedad, listado ordenado, consulta que no altera el estado | Consulta del más prioritario sin incidentes activos |
| RF6 - Gestionar vehículos de atención | Registro de vehículo válido, consulta por identificador existente, listado de disponibles, cambio de estado al asignar y al resolver | Identificador duplicado, ubicación fuera del mapa, consulta de identificador inexistente |
| RF7 - Asignar vehículos a incidentes | Propuesta de vehículo candidato compatible, asignación de vehículo compatible y disponible | Vehículo incompatible, vehículo no disponible, incidente resuelto, incidente con vehículo ya asignado, incidente inexistente, vehículo inexistente, sin candidatos disponibles |