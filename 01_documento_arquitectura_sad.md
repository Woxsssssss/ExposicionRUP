# Documento de Arquitectura de Software (SAD) — Módulo Misiones y Progresión

* **Autor:** Jean (Arquitecto de Software — Equipo E4)
* **Fase RUP:** Elaboración (Artefacto de cierre de fase)

---

## 1. Vista Lógica (Arquitectura en Capas)

El módulo de Misiones y Progresión se estructura bajo una arquitectura de **3 capas** para separar la interfaz de usuario de las reglas de progresión y el acceso a datos:

1. **Capa de Presentación (Vista):** `VistaMisiones`. Gestiona la interacción con el jugador, muestra el listado de misiones activas y renderiza las notificaciones de recompensa o subida de nivel.
2. **Capa de Lógica de Negocio (Controlador/Servicio):** `MisionService` y entidades de dominio (`Mision`, `Recompensa`, `Personaje`). Orquesta la validación de cumplimiento de objetivos, asignación de experiencia y cálculo de nivel.
3. **Capa de Persistencia (Acceso a Datos):** Interfaz `IMisionRepository` e implementación `MisionRepository`. Encapsula las consultas SQL hacia el motor relacional.

*(Ver diagrama en `01_vista_logica.puml` / `01_vista_logica.png`)*

---

## 2. Vista de Despliegue

El sistema se despliega en una topología cliente-servidor distribuida en 3 nodos físicos/lógicos:

* **Nodo Cliente (Navegador Web / App Cliente):** Ejecuta la interfaz gráfica (`VistaMisiones`) y se comunica mediante peticiones HTTPS / JSON.
* **Nodo Servidor de Aplicaciones (Backend Java / Python):** Aloja los servicios REST, la lógica de negocio (`MisionService`) y la capa de persistencia (`MisionRepository`).
* **Nodo Servidor de Base de Datos (MariaDB):** Almacena de forma transaccional el estado de los personajes, sus misiones asignadas y el catálogo de recompensas (comunicación vía JDBC/TCP puerto `3306`).

*(Ver diagrama en `01_vista_despliegue.puml` / `01_vista_despliegue.png`)*

---

## 3. Decisiones Arquitectónicas

| ID | Decisión Tomada | Alternativa Descartada | Justificación Técnica |
| :--- | :--- | :--- | :--- |
| **DA-01** | **Uso del patrón Repository (`IMisionRepository`)** para desacoplar la lógica de negocio de SQL. | Consultas SQL directas dentro de `MisionService` (Transaction Script acoplado). | Facilita las pruebas unitarias mediante *mocks* sin depender de MariaDB y permite cambiar el motor o esquema de BD sin alterar las reglas de progresión. |
| **DA-02** | **Cálculo de experiencia y subida de nivel en el Servidor (`Personaje.ganarExperiencia`)**. | Calcular la subida de nivel en el cliente y enviar el nuevo nivel al servidor. | Evita trampas o manipulación de paquetes por parte del jugador (integridad y seguridad del estado del personaje) y garantiza consistencia transaccional. |

---

## 4. Riesgos Técnicos y Mitigación

* **Riesgo Técnico (RT-01):** *Inconsistencia de datos por fallos parciales al completar una misión* (ejemplo: la misión se marca como `COMPLETADA`, pero ocurre un error antes de acreditar la experiencia u oro al personaje, o el jugador envía doble petición simultánea para reclamar dos veces la misma recompensa).
* **Estrategia de Mitigación:**
  1. Ejecutar `MisionService.completarMision()` dentro de una **transacción ACID** en MariaDB (`BEGIN TRANSACTION` / `COMMIT` / `ROLLBACK`), garantizando que la actualización de la misión y del personaje ocurran como una operación atómica.
  2. Validar en el servidor el estado previo de la misión (`estado == EN_PROGRESO`) con bloqueo optimista/pesimista antes de procesar la recompensa.
