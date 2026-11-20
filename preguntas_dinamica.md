# Dinámica de Preguntas — Equipo E4 (Misiones y Progresión)

---

### Pregunta 1 (Tipo: "¿Qué artefacto usarías?")
**Situación:** Durante el desarrollo de RPG Manager, el equipo de pruebas detecta que un jugador puede recibir experiencia pero el sistema no evalúa si alcanzó el umbral para subir de nivel. Necesitas revisar **en qué orden exacto** `MisionService` invoca a `Personaje` y dónde ocurre la condición (`alt`) de subida de nivel. ¿Qué artefacto debes consultar?

* **a)** Modelo del dominio
* **b)** Diagrama de secuencia
* **c)** Vista de despliegue del SAD

**Respuesta correcta:** **b) Diagrama de secuencia**
* **Explicación:** El diagrama de secuencia muestra la colaboración temporal entre objetos (`MisionService` $\rightarrow$ `Personaje`) y usa fragmentos combinados (`alt`) para representar condicionales como `experienciaActual >= experienciaSiguienteNivel`. El modelo del dominio (a) solo muestra conceptos sin métodos ni orden temporal, y la vista de despliegue (c) muestra servidores y nodos físicos.

---

### Pregunta 2 (Tipo: "Encuentra el error" — Consistencia entre artefactos)
**Situación:** Observa este fragmento de nuestro **Diagrama de Secuencia** al reclamar la recompensa de una misión:

```text
Service -> Personaje : otorgarRecompensa(xp, oro)
Personaje -> Personaje : subirNivel()
