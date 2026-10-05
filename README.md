# API REST de Gestión de Clientes - Spring Boot

## 1. Visión y Requisitos Técnicos de Arquitectura
* **Framework y Lenguaje:** Java 21 / 25 con Spring Boot 3.x (Spring Web, Spring Data JPA, Validation).
* **Persistencia:** Base de datos H2 en memoria (`jdbc:h2:mem:clientesdb`), consola web activada para pruebas locales.
* **Manejo de Errores Global:** Implementación de `@RestControllerAdvice` retornando respuestas bajo el estándar RFC 7807 (`ProblemDetail`).
* **Documentación:** Swagger / OpenAPI 3.

---

## 2. Historias de Usuario con Criterios de Aceptación (BDD)

### HU-01: Gestión CRUD de Clientes
> **Como** Analista de Datos,  
> **quiero** registrar, consultar, actualizar y eliminar clientes,  
> **para** mantener actualizada la base de datos operativa.

* **Escenario 1: Creación exitosa**
  * **Given** que envío una solicitud `POST /api/v1/clientes` con un payload válido (DNI único, Nombre, Email).
  * **When** la API procesa el registro.
  * **Then** responde con HTTP `201 Created`, incluye el header `Location` y el cliente generado con su ID.

* **Escenario 2: Validación de DNI duplicado**
  * **Given** que ya existe un cliente registrado con el DNI "12345678".
  * **When** intento registrar otro cliente con el mismo DNI "12345678".
  * **Then** la API responde con HTTP `409 Conflict` indicando el fallo de unicidad.

### HU-02: Búsqueda y Filtrado por DNI o Nombre
> **Como** Operador del Sistema,  
> **quiero** realizar consultas por DNI o por coincidencia de Nombre,  
> **para** ubicar rápidamente la información del cliente.

* **Escenario 1: Búsqueda exacta por DNI**
  * **Given** que realizo una petición `GET /api/v1/clientes?dni=12345678`.
  * **When** la API ejecuta la consulta en la base de datos H2.
  * **Then** retorna HTTP `200 OK` con los datos del cliente encontrado.

* **Escenario 2: Búsqueda parcial por Nombre**
  * **Given** que realizo una petición `GET /api/v1/clientes?nombre=Carlos`.
  * **When** la API busca coincidencias parciales sin diferenciar mayúsculas/minúsculas (*case-insensitive*).
  * **Then** retorna HTTP `200 OK` con el listado de clientes coincidentes.

---

## 3. Especificación del Contrato de API (Endpoints)

| Método HTTP | Endpoint | Descripción | Parámetros / Body | Código Esperado |
| :--- | :--- | :--- | :--- | :--- |
| `POST` | `/api/v1/clientes` | Crear cliente | Body: `ClienteRequestDTO` | `201 Created` / `400 Bad Request` |
| `GET` | `/api/v1/clientes` | Listar / Buscar | Query Params opcionales: `dni`, `nombre` | `200 OK` |
| `GET` | `/api/v1/clientes/{id}` | Obtener por ID | Path Param: `id` | `200 OK` / `404 Not Found` |
| `PUT` | `/api/v1/clientes/{id}` | Actualizar cliente | Path Param: `id`, Body: `ClienteRequestDTO` | `200 OK` / `404 Not Found` |
| `DELETE` | `/api/v1/clientes/{id}` | Eliminar cliente | Path Param: `id` | `204 No Content` / `404 Not Found` |

---

## 4. Acuerdos de Calidad del Equipo

### 4.1. Definition of Ready (DoR) - Criterios de Entrada
Una historia de usuario entra al Sprint solo si cumple:
- [x] **Principio INVEST:** La historia es independiente, negociable, valiosa, estimable, pequeña y testeable.
- [x] **Contrato Definido:** Esquemas de base de datos, DTOs y endpoints acordados previamente.
- [x] **Reglas Claras:** Validación de DNI (8 dígitos exactos) y estructura de correo documentadas.
- [x] **Estimación Asignada:** Estimada en Story Points usando Planning Poker (Fibonacci).

### 4.2. Definition of Done (DoD) - Criterios de Salida
Una tarea se da por completada ("Hecha") únicamente si cumple:
- [x] Código funcional implementado con base de datos H2.
- [x] Pruebas unitarias (JUnit 5 + Mockito) e integración pasando exitosamente en el flujo de CI/CD.
- [x] Pull Request (PR) atómico (< 300 líneas) revisado y aprobado por un par o líder técnico.
- [x] Análisis estático libre de vulnerabilidades y con cobertura mínima aprobada.
- [x] Documentación interactiva Swagger / OpenAPI actualizada.
