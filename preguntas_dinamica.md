# Dinámica de preguntas · Grupo E4

**Módulo:** Misiones y progresión  
**Duración:** 5 minutos  
**Total:** 5 preguntas

Estas preguntas son para realizar al curso durante la exposición. Incluyen una situación de selección de artefacto y un ejercicio de detección de errores, tal como solicita el taller.

## 1. ¿Qué artefacto usarías?

¿Cómo se conectan el navegador, la aplicación RPG Manager y la base de datos MariaDB? ¿En qué artefacto del Grupo 4 encontrarías esa información?

- **A.** Documento de Arquitectura, vista de despliegue.
- **B.** Diagrama de secuencia.
- **C.** Diagrama de clases de diseño.

**Respuesta correcta: A.** El Documento de Arquitectura, en su vista de despliegue, muestra los componentes que se ejecutan y cómo se conectan. El diagrama de secuencia muestra el orden de las interacciones, mientras que el diagrama de clases representa la estructura del software.

---

## 2. Encuentra el error

En un fragmento del diagrama de secuencia aparece la llamada `Personaje.aumentarNivel()`, pero en el diagrama de clases el método declarado es `subirNivel(): void`. ¿Qué problema identificas y cómo lo solucionarías?

- **A.** No hay ningún error: los métodos pueden tener nombres diferentes en ambos diagramas.
- **B.** Hay una inconsistencia entre los artefactos; se debe cambiar la llamada del diagrama de secuencia a `subirNivel()`.
- **C.** El problema es que `subirNivel()` no debería tener un tipo de retorno.

**Respuesta correcta: B.** Los nombres no coinciden entre los dos artefactos. En el diseño del grupo, la llamada del diagrama de secuencia debe ser `subirNivel()`, igual que el método declarado en `Personaje`. Así se mantiene la coherencia entre ambos diagramas.

---

## 3. Responsabilidades de los componentes

La pantalla de misiones ejecuta SQL directamente para otorgar la recompensa. ¿Qué cambio sería más adecuado según nuestra arquitectura?

- **A.** Conservar la consulta en la pantalla para evitar clases adicionales.
- **B.** Enviar la operación a `MisionService` y realizar el acceso a datos mediante `MisionRepository`.
- **C.** Mover la consulta al manual de usuario.

**Respuesta correcta: B.** `MisionService` concentra las reglas del proceso y `MisionRepository` gestiona el acceso a los datos. Las otras opciones mezclan responsabilidades o no resuelven el problema.

---

## 4. Decisión técnica y riesgo

Un jugador hace doble clic en «Completar misión» y el sistema le entrega experiencia dos veces. ¿Qué medida de diseño corresponde al riesgo identificado en nuestro Documento de Arquitectura?

- **A.** Colocar un mensaje bajo el botón indicando que se debe presionar una sola vez.
- **B.** Validar el estado de la misión y registrar progreso y recompensa dentro de una transacción.
- **C.** Agregar un ciclo al diagrama de secuencia.

**Respuesta correcta: B.** La validación del estado y la transacción ayudan a evitar recompensas duplicadas y a conservar la consistencia de los datos. Un mensaje en la interfaz no impide técnicamente el doble registro y un ciclo en el diagrama no resuelve el riesgo.

---

## 5. De diseño a construcción

El Equipo E5 necesita conocer el orden en que `VistaMisiones`, `MisionService`, `Personaje` y `MisionRepository` colaboran para completar una misión. ¿Qué artefacto del Grupo 4 debe consultar primero?

- **A.** Diagrama de secuencia.
- **B.** Documento de Visión del sistema.
- **C.** Caso de negocio.

**Respuesta correcta: A.** El diagrama de secuencia representa las llamadas en orden temporal. El Documento de Visión y el caso de negocio sirven para definir o justificar el proyecto, pero no detallan el orden de mensajes entre esos componentes.
