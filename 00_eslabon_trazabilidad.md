# Eslabón de Trazabilidad — Equipo E4 (Misiones y Progresión)

Este documento conecta el trabajo de **Diseño y Arquitectura (E4)** con las fases anterior (Requisitos/Análisis) y posterior (Construcción) dentro de la cadena RUP de RPG Manager.

---

## 1. Insumo Recibido (Fase Anterior: Requisitos y Análisis)

> *Nota: En un proyecto real, este insumo sería producido por el Especificador de Requisitos (E2) y el Analista de Sistemas (E3).*

### Especificación Mínima de Caso de Uso: `CU-MIS-01: Completar Misión`
* **Actor:** Jugador.
* **Precondiciones:** El personaje ha iniciado sesión, tiene la misión en estado `EN_PROGRESO` y ha cumplido todos los objetivos requeridos.
* **Flujo Básico:**
  1. El jugador solicita reclamar la recompensa de la misión completada desde la interfaz.
  2. El sistema valida que la misión esté activa para ese personaje y marca su estado como `COMPLETADA`.
  3. El sistema otorga la experiencia (`XP`) y el oro definidos en la recompensa al personaje.
  4. Si la experiencia acumulada alcanza o supera el umbral del siguiente nivel, el personaje sube de nivel y actualiza sus estadísticas base.
  5. El sistema persiste los cambios en la base de datos y notifica el resultado al jugador.
* **Postcondiciones:** La misión queda registrada como completada y el personaje actualiza su experiencia, oro y (si aplica) su nivel.

---

## 2. Matriz de Trazabilidad Interna (E4)

| Requisito / Caso de Uso (Entrada) | Elemento Arquitectónico (SAD) | Clase(s) y Método(s) de Diseño | Interacción en Secuencia |
| :--- | :--- | :--- | :--- |
| `CU-MIS-01`: Completar misión | Capa de Servicio (`MisionService`) y Persistencia (`MisionRepository`) | `MisionService.completarMision(idPersonaje, idMision)`<br>`Mision.marcarCompletada()` | Paso 2 a 4 del diagrama `Completar misión` |
| Regla de negocio: Otorgar recompensa y calcular subida de nivel | Lógica de Dominio en Capa de Servicio / Entidad `Personaje` | `Personaje.ganarExperiencia(xp)`<br>`Personaje.subirNivel()` | Fragmento `alt [experiencia >= umbralNivel]` |
| `RNF-MIS-01`: Persistencia transaccional en BD relacional | Nodo de Base de Datos **MariaDB** mediante patrón Repository | `IMisionRepository.actualizarEstado(...)`<br>`MisionRepository` | Paso 6 (`actualizarEstado`) |

---

## 3. Salida hacia la Fase Siguiente (Construcción — Equipo E5)

Los artefactos generados por E4 alimentan directamente al **Implementador** y al **Diseñador de Base de Datos** en la fase de Construcción:

1. **Al Modelo de Datos (Artefacto 15):** Las entidades `Personaje`, `Mision` y `Recompensa` del Diagrama de Clases definen las tablas, tipos de datos y llaves foráneas (`id_mision`, `id_personaje`, `id_recompensa`) que deben crearse en **MariaDB**.
2. **Al Código Fuente y Componentes (Artefacto 16):** Las firmas exactas de `MisionService`, `IMisionRepository` y `MisionRepository` sirven como contrato directo para programar las clases y endpoints sin ambigüedades.
3. **Al Plan de Despliegue (Artefacto 19 - E6):** La Vista de Despliegue del SAD establece la topología Cliente-Servidor-MariaDB necesaria para configurar los ambientes de pruebas y producción.
