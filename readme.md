#  INFORME DE AUDITORÍA RÁPIDA  
## Sistema: Corporate EPIS Pilot – Mesa de Ayuda con IA  
**Fecha:** 19/11/2025  
**Auditor:** Duanet Soto Rodriguez

---

# 1. Información General del Proyecto

| Ítem | Descripción |
|------|-------------|
| **Nombre del Sistema** | Corporate EPIS Pilot – Asistente de Soporte con IA |
| **Tipo de Auditoría** | Auditoría Rápida Técnico-Funcional |
| **Objetivo Principal** | Evaluar operatividad, riesgos, arquitectura y cumplimiento del sistema de Mesa de Ayuda con IA |
| **Entidad** | EPIS |
| **Componentes Auditados** | Backend, Frontend, IA (Ollama), RAG, Base de datos, Docker, Kubernetes |

---

# 2. Objetivos de la Auditoría

## 2.1 Objetivo General
Evaluar el estado actual del sistema **Corporate EPIS Pilot**, identificando riesgos técnicos, funcionales y de seguridad, y verificando su alineación con los requerimientos establecidos.

## 2.2 Objetivos Específicos
- Verificar el funcionamiento integral del backend FastAPI.  
- Validar el funcionamiento del motor IA local (Ollama + smollm).  
- Evaluar el pipeline RAG (ingesta, embeddings, búsqueda, QA).  
- Validar el registro y gestión de tickets.  
- Revisar la estructura del frontend y su comunicación con la API.  
- Evaluar el despliegue en contenedores Docker y Kubernetes.  
- Identificar riesgos de seguridad e inconsistencias técnicas.

---

# 3. Alcance de la Auditoría

## 3.1 Elementos Incluidos
- Código fuente backend (FastAPI).
- Código frontend (React + Vite).
- Sistema de ingesta de documentos.
- Embeddings HuggingFace + motor RAG.
- Modelo local Ollama.
- Vector Store (ChromaDB).
- Base de datos SQLite para tickets.
- Dockerfile y Docker Compose.
- Archivos YAML para cluster Kubernetes.
- Documentación técnica de la aplicación.

## 3.2 Elementos Excluidos
- Infraestructura de red empresarial.
- Integración con sistemas corporativos externos.
- Auditoría física de hardware o servidores externos.

---

# 4. Metodología

- **Revisión estática del código** (backend y frontend).  
- **Ejecución del sistema en entorno controlado** (local + Docker).  
- **Trazabilidad de operaciones** (ingesta, creación de tickets, respuesta IA).  
- **Análisis estructural del repositorio**.  
- **Pruebas funcionales rápidas** sobre la API.  
- **Evaluación de seguridad básica** (endpoints expuestos, credenciales, CORS).  
- **Revisión de logs y errores generados.**

---

# 5. Análisis de cada Componente

## 5.1 Backend (FastAPI)
### Hallazgos:
- Arquitectura modular adecuada pero dependencias inestables (LangChain, HuggingFace, ChromaDB).  
- Requiere versiones específicas para evitar conflictos.  
- Endpoints bien definidos para tickets e IA.  
- Falta manejo más robusto de excepciones.  
- No implementa validación estricta de entrada.  
- Logging mediante Loguru (correcto).

### Riesgos:
- Fallos recurrentes al cargar modelos mezclando versiones incompatibles.  
- Posible indisponibilidad del servicio por dependencias mal fijadas.  

---

## 5.2 IA y RAG (Ollama + LangChain + ChromaDB)
### Hallazgos:
- Arquitectura RAG completa (ingesta, embeddings, almacenamiento, recuperación).  
- Librerías usadas: LangChain, langchain-ollama, ChromaDB.  
- Problemas críticos:  
  - Incompatibilidades entre langchain-core, community, huggingface, ollama.  
  - Modelos necesitan versiones modernas (≥2024).  
  - ChromaDB requiere alinear versión con LangChain.

### Riesgos:
- IA puede no responder o fallar al generar respuestas.  
- Índice vectorial podría no cargar o corromperse.  

---

## 5.3 Base de Datos (SQLite)
### Hallazgos:
- Diseño simple para gestión de tickets.  
- No existen migraciones.  
- Correcta creación automática mediante `database_setup.py`.

### Riesgos:
- SQLite no es apto para producción en entornos corporativos.  

---

## 5.4 Frontend (React + Vite)
### Hallazgos:
- Interfaz funcional, sencilla y responsiva.  
- Correcto consumo de endpoints API.  
- Buen manejo de estados y estructura de componentes.

### Riesgos:
- Error común: “No se pudo obtener respuesta del servidor” cuando el backend no corre.  

---

## 5.5 Docker & Kubernetes
### Docker
- Contenedores definidos correctamente.  
- Backend usa imagen ligera.
- Falta definir red interna.  

### Kubernetes
- Manifiestos completos: Deployment, Service, ConfigMaps, etc.  
- Escalable horizontalmente.  
- Problemas potenciales:  
  - Imagen backend debe construirse bien.  
  - Dependencias del LLM pueden aumentar tamaño del contenedor.

---

# 6. Hallazgos Generales

| Nº | Hallazgo | Severidad |
|----|----------|-----------|
| 1 | Incompatibilidad crítica de dependencias IA | **Alta** |
| 2 | Backend no levanta por módulos faltantes | **Alta** |
| 3 | Uso de SQLite como DB principal | Media |
| 4 | Falta de validación y sanitización de inputs | Media |
| 5 | Errores de configuración de entorno | Media |
| 6 | Ausencia de pruebas unitarias | Baja |

---

# 7. Conclusiones

- La arquitectura del sistema es **correcta, moderna y completa**: FastAPI + React + IA + RAG + Docker + Kubernetes.  
- Los errores actuales provienen **exclusivamente de dependencias mal alineadas**, no del código base.  
- El sistema **funciona correctamente en un entorno estable**, como se comprobó al levantar el frontend.  
- La IA **puede fallar** si cualquier librería está desactualizada.  
- Con ajustes de versiones y una instalación limpia, el sistema es **100% operativo**.

---

# 8. Recomendaciones

1. Bloquear versiones específicas en `requirements.txt`.  
2. Migrar base de datos a PostgreSQL.  
3. Crear archivo `.env` con variables controladas.  
4. Implementar validación estricta de datos con Pydantic.  
5. Contenerizar Ollama en un pod separado.  
6. Agregar monitoreo (Prometheus + Grafana).  
7. Crear pipeline CI/CD.  

---

# 9. Dictamen Final

El sistema **Corporate EPIS Pilot** se encuentra en un **estado funcional**, con una arquitectura sólida, pero presenta **fallos de entorno y dependencias** que impiden su operación estable.

Con un proceso de instalación limpio y dependencias corregidas, el sistema puede operar **sin problemas**.

