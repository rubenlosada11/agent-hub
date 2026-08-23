# AgentHub — Panel de Administración

Prototipo funcional de alta fidelidad de un **panel interno de administración** para AgentHub, una plataforma SaaS ficticia donde empresas alquilan agentes de IA preconfigurados equipados con distintas *skills* (navegación web, lectura de documentos, gestión de calendarios, consulta de información, ejecución de tareas de negocio).

Este panel **no es** el producto público de cara al cliente: es la herramienta que usa el equipo de AgentHub para supervisar usuarios, agentes, skills, contrataciones y errores de ejecución de toda la plataforma.

## Qué incluye el prototipo

El panel es una aplicación de una sola página (`index.html`) con seis módulos, todos navegables desde un sidebar persistente sin recargar la página:

- **Dashboard** — cuatro métricas clave del negocio (ingresos del mes, pérdidas por descuentos, agentes activos, agentes fallando) y un panel de actividad semanal de los agentes.
- **Usuarios** — listado de clientes de la plataforma con su plan y estado de cuenta, con vista de detalle ampliada por usuario (empresa, historial de contratación, gasto total).
- **Agentes** — inventario de agentes de IA desplegados, su propietario, estado operativo, las skills que tiene equipadas y su prompt de sistema configurable.
- **Skills** — catálogo de capacidades disponibles para equipar en los agentes, con una explicación de qué es una skill y cuántos agentes la tienen habilitada.
- **Contrataciones** — contratos de alquiler de agentes por cliente, con periodo, importe y desglose de precio por skill contratada.
- **Log de errores** — incidencias de ejecución de los agentes, categorizadas por gravedad, con traza completa y opción de marcarlas como resueltas.

Todos los datos (usuarios, agentes, skills, contratos, errores) son ficticios pero **coherentes entre secciones**: un mismo agente, cliente o skill aparece con el mismo nombre allí donde se le referencia.

## Funcionalidad interactiva

- Navegación lateral entre las seis secciones con indicador de sección activa.
- Menús de acciones contextuales (⋮) en cada tabla/listado, con comportamiento estándar de apertura, cierre y exclusión mutua.
- Ventanas modales de detalle para usuarios, agentes, skills, contratos y errores, con cierre por botón o por clic fuera del contenido.
- Lista de skills de cada agente, colapsada por defecto y expandible con una transición visual.
- Modo claro/oscuro persistente entre sesiones, aplicado a todo el panel.
- Diseño adaptado a escritorio y tablet.

## Cómo revisarlo

El prototipo no requiere instalación ni backend: basta con abrir `index.html` en un navegador para explorar el panel completo con datos precargados.

La especificación funcional completa del proyecto —alcance, comportamiento esperado de cada componente y criterios de aceptación— está documentada en [`SPECS.md`](./SPECS.md).

## Alcance

Es un prototipo de interfaz: no hay backend, API, base de datos ni persistencia real de cambios entre sesiones. El objetivo es validar la experiencia y el diseño del panel de administración, no una implementación productiva.
