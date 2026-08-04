# Doc 05 - Hoja de Ruta (Roadmap)

**Proyecto:** SyncroLab  
**Versión:** 1.0  
**Estado:** En planificación  
**Última actualización:** Agosto 2026  

---

### 1. Introducción
Este documento define la planificación estratégica y el ciclo de vida del desarrollo de SyncroLab.

El objetivo es establecer un plan de acción secuencial, priorizando el desarrollo de funcionalidades según sus dependencias técnicas y su impacto en el negocio. Este *Roadmap* no opera con fechas límite rígidas, sino con **Fases de Desarrollo Incremental**, asegurando que cada etapa sea funcional, testeable y escalable antes de avanzar a la siguiente.

---

### 2. Objetivos del Roadmap
- **Mitigar riesgos técnicos:** Construir los cimientos de seguridad y arquitectura de datos antes que las interfaces transaccionales.
- **Enfoque MVP (Producto Mínimo Viable):** Identificar el núcleo transaccional mínimo necesario para que una PyME pueda operar el sistema.
- **Desarrollo Modular:** Asegurar que las etapas posteriores (integraciones, IA, e-commerce) se acoplen sin romper el núcleo.

---

### 3. Fase 1: Análisis y Arquitectura (Current State)
*Objetivo: Definir las reglas de negocio y el diseño del sistema.*
- Redacción de la Visión del Producto.
- Análisis Funcional y de Negocio (Caso de estudio: Cronkie).
- Definición de Requerimientos y Arquitectura Lógica.
- Modelado de Dominio (Entidades y relaciones).

---

### 4. Fase 2: Diseño Técnico y UX/UI
*Objetivo: Estructurar la base de datos y la experiencia del usuario.*
- Diagramado de la Base de Datos Relacional (DER).
- Diseño de la API (Endpoints, métodos, payloads).
- Wireframing y diseño de la Interfaz de Usuario (UI) enfocada en operaciones rápidas (Punto de Venta, Dashboard).
- Configuración de los repositorios y entornos de desarrollo (CI/CD básicos).

---

### 5. Fase 3: Desarrollo del MVP (Core del Sistema)
*Objetivo: Construir el núcleo operativo. Sin esta fase completada, el software no es funcional.*
Esta etapa sigue un orden de dependencias estricto:

1. **Fundamentos:** Gestión de Usuarios, Autenticación (Login/Tokens) y Roles.
2. **Catálogos Base:** Gestión de Entidades (Clientes/Proveedores), Productos e Insumos.
3. **Motor Lógico:** Gestión de Stock Unificado (Ingresos, Egresos, Ajustes).
4. **Flujo de Dinero:** Gestión de Caja (Apertura/Cierre).
5. **Transaccionalidad:** Gestión de Ventas (Ingresos) y Gastos/Compras (Egresos).

---

### 6. Fase 4: Funcionalidades Avanzadas (Versión 1.0)
*Objetivo: Elevar el MVP a un sistema de gestión integral, agregando valor analítico y operativo.*
- Módulo de Producción (Fórmulas, recetas y deducción de stock).
- Módulo de Pedidos (Trazabilidad de estados de preparación y entrega).
- Dashboard Gerencial y Reportería financiera.
- Módulo de Configuración Global (Impuestos, sucursales, monedas).

---

### 7. Fase 5: Capa de Integraciones (Módulos Periféricos)
*Objetivo: Conectar SyncroLab con el ecosistema digital externo.*
- Integración fiscal (Facturación Electrónica vía AFIP/ARCA).
- Integración de mensajería (Notificaciones por WhatsApp/Email).
- E-commerce y Catálogo Digital.
- Pasarelas de cobro y terminales de pago.

---

### 8. Fase 6: Optimización y Escala
*Objetivo: Refactorización continua y mejora de métricas.*
- Optimización de consultas a la base de datos (Indexación).
- Implementación de caché para el catálogo de productos.
- Auditorías de seguridad.
- Incorporación de funcionalidades basadas en IA (Predicción de quiebres de stock, sugerencias de compras).

---

### Decisiones Estratégicas Tomadas

- **Autenticación Primero:** Se bloquea el desarrollo de cualquier entidad transaccional hasta que el sistema de usuarios y seguridad esté implementado.
- **MVP Definido:** La Fase 3 marca la frontera entre un sistema en desarrollo y un sistema capaz de ser testeado en el mundo real por el PoC (Cronkie).
- **Desarrollo Ágil/Incremental:** Ninguna fase pasará a producción si no cuenta con la documentación técnica y las pruebas correspondientes.
