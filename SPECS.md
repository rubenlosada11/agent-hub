# SPECS — AgentHub · Panel de Administración

> Este documento es la única fuente de verdad para construir el prototipo. Un desarrollador o agente de código debe poder implementar `index.html` leyendo únicamente este archivo, sin hacer preguntas adicionales.

---

## 1. Descripción del producto

**AgentHub** es una plataforma SaaS que permite a empresas alquilar **agentes de IA preconfigurados** para resolver tareas de negocio (investigación, soporte, ventas, operaciones, análisis). Cada agente puede equiparse con distintas **skills** (capacidades) como navegar por la web, leer documentos, gestionar calendarios, consultar información interna o ejecutar tareas de negocio.

**Problema que resuelve:** las empresas necesitan capacidades de IA operativas sin construir su propia infraestructura de agentes. AgentHub centraliza la contratación, configuración y supervisión de estos agentes.

**Quién usa este panel:** administradores internos de AgentHub (no los clientes finales). Su trabajo es supervisar la salud y el negocio de la plataforma: qué clientes hay, qué agentes están operando, qué skills existen, qué contratos están activos y qué está fallando.

**Objetivo del administrador:** tener visibilidad total y rápida de:
- Estado general del negocio (ingresos, pérdidas, salud de agentes).
- Gestión de cuentas de usuarios/clientes.
- Configuración y estado de los agentes desplegados.
- Catálogo de skills disponibles y su adopción.
- Contratos de alquiler activos y su facturación.
- Errores de ejecución que requieren atención.

**Fuera de alcance de este prototipo:**
- No hay backend, API ni base de datos reales; todos los datos están hardcodeados en JavaScript.
- No hay autenticación real ni gestión de sesión.
- No se implementa el frontend público para clientes (eso es un producto distinto).
- No hay persistencia real de cambios (crear/editar/eliminar son interacciones visuales, no mutan un backend).
- No se integra ninguna librería de gráficos real; el gráfico de actividad es un placeholder visual de alta fidelidad.
- No hay internacionalización; el panel se construye en español (idioma de este documento y del equipo).

---

## 2. Stack tecnológico

Obligatorio y exclusivo:

- HTML5 semántico.
- Tailwind CSS vía CDN: `https://cdn.tailwindcss.com` (Tailwind Play CDN), configurado inline con `tailwind.config = { darkMode: 'class', theme: {...} }` dentro de un `<script>` en el `<head>`. Se elige este CDN (en vez del de `@tailwindcss/browser@4` sugerido en el README del starter) porque permite configurar `darkMode: 'class'` de forma explícita y predecible, requisito indispensable para el toggle de tema.
- JavaScript vanilla (ES6+), sin frameworks.
- **Sin** React, Vue, Angular, jQuery, TypeScript.
- **Sin** herramientas de build (no bundlers, no npm install, no transpilación).
- **Sin** backend, API ni base de datos.
- **Sin** archivos CSS externos ni CSS personalizado (`<style>` o `.css`). Todo el estilo se resuelve con clases utilitarias de Tailwind.
- **Sin** estilos inline (atributo `style="..."`).
- El modo oscuro se implementa exclusivamente con utilidades `dark:` de Tailwind, activadas alternando la clase `dark` en `<html>`.

---

## 3. Arquitectura general

- Entregable único: **`index.html`** en la raíz del repositorio — aplicación de una sola página (SPA sin router).
- Todo el JavaScript vive en un único bloque `<script>` al final de `index.html` (sin archivos `.js` externos), organizado en secciones comentadas: `DATASETS`, `STATE`, `RENDER`, `NAV`, `DROPDOWNS`, `MODALS`, `DARK MODE`, `INIT`.
- Las seis secciones (`Dashboard`, `Usuarios`, `Agentes`, `Skills`, `Contrataciones`, `Log de errores`) existen todas como elementos `<section>` dentro del mismo `<main>`, cada una con un `id` único (`section-dashboard`, `section-usuarios`, `section-agentes`, `section-skills`, `section-contrataciones`, `section-errores`).
- Solo una sección tiene la clase visible (resto con `hidden`) en un momento dado. El sidebar cambia qué `<section>` está visible mediante JavaScript (sin recargar la página, sin hash routing, sin `history.pushState`).
- Los datasets (usuarios, agentes, skills, contrataciones, errores) se definen como arrays/objetos JavaScript en la parte superior del script, y todas las funciones de render leen de esas mismas estructuras para garantizar consistencia entre secciones.

---

## 4. Datasets hardcodeados (fuente única de verdad)

Estos son los datos exactos a usar. Los nombres deben ser **idénticos y literales** en todas las secciones donde aparezcan.

### 4.1 Usuarios (clientes) — mínimo 5, usar estos 6

| Nombre | Email | Empresa | Plan | Estado |
|---|---|---|---|---|
| Laura Gómez | laura.gomez@vantexcorp.com | Vantex Corp | Enterprise | Activo |
| Marcus Chen | marcus.chen@brightloop.io | BrightLoop Inc | Pro | Activo |
| Sofia Ibáñez | sofia.ibanez@nordlink.eu | Nordlink Solutions | Pro | Suspendido |
| David Okafor | d.okafor@parallelworks.ai | Parallel Works | Free | Activo |
| Elena Petrova | elena.petrova@quantiva.co | Quantiva | Enterprise | Pendiente de pago |
| Tom Richards | tom.richards@fluxbase.dev | FluxBase | Pro | Activo |

### 4.2 Skills — mínimo 4, usar estas 5

| Skill | Descripción | Agentes habilitados |
|---|---|---|
| Navegación Web | Permite al agente navegar por internet en tiempo real para obtener información actualizada. | 2 |
| Lectura de Documentos | Permite leer, interpretar y extraer información de documentos PDF, Word y hojas de cálculo. | 3 |
| Gestión de Calendarios | Permite crear, modificar y consultar eventos en calendarios conectados (Google Calendar, Outlook). | 2 |
| Consulta de Información | Permite consultar bases de datos internas y catálogos de negocio para resolver preguntas. | 3 |
| Ejecución de Tareas | Permite ejecutar flujos de trabajo predefinidos, como generar reportes o enviar notificaciones. | 2 |

(El número de "agentes habilitados" se calcula a partir del array de agentes de 4.3, no se hardcodea por separado — debe coincidir.)

### 4.3 Agentes — mínimo 4, usar estos 5

| Agente | Propietario (empresa) | Estado | Skills asociadas |
|---|---|---|---|
| Atlas Research Agent | Vantex Corp | Activo | Navegación Web, Lectura de Documentos, Consulta de Información |
| Nova Support Agent | BrightLoop Inc | Fallando | Gestión de Calendarios, Consulta de Información, Ejecución de Tareas |
| Helix Sales Agent | Nordlink Solutions | Activo | Navegación Web, Ejecución de Tareas |
| Orion Ops Agent | Quantiva | Inactivo | Lectura de Documentos, Gestión de Calendarios |
| Vega Analyst Agent | FluxBase | Activo | Consulta de Información, Lectura de Documentos |

Cada agente tiene además un **prompt de sistema** hardcodeado (2-4 frases, texto realista) usado en el modal "Configurar", por ejemplo para Atlas Research Agent:

> "Eres Atlas, un agente de investigación. Tu trabajo es navegar la web y leer documentos para responder preguntas de negocio con fuentes verificables. Responde siempre en español, cita tus fuentes y evita especular cuando no tengas información suficiente."

(Redactar un prompt equivalente y coherente para cada uno de los 5 agentes.)

### 4.4 Contrataciones — mínimo 4, usar estas 5

| Cliente | Agente | Skills contratadas | Inicio | Fin | Importe total | Estado |
|---|---|---|---|---|---|---|
| Vantex Corp | Atlas Research Agent | Navegación Web, Lectura de Documentos | 2026-01-15 | 2026-07-15 | $1,620 | Activo |
| BrightLoop Inc | Nova Support Agent | Gestión de Calendarios, Consulta de Información, Ejecución de Tareas | 2026-03-01 | 2026-09-01 | $2,340 | Activo |
| Nordlink Solutions | Helix Sales Agent | Navegación Web, Ejecución de Tareas | 2025-11-10 | 2026-05-10 | $1,860 | Vencido |
| Quantiva | Orion Ops Agent | Lectura de Documentos, Gestión de Calendarios | 2026-02-01 | 2026-08-01 | $1,320 | Pago pendiente |
| FluxBase | Vega Analyst Agent | Consulta de Información, Lectura de Documentos | 2026-04-01 | 2026-10-01 | $1,500 | Activo |

Todos los contratos tienen una duración de 6 meses. Cada skill contratada tiene un **precio individual mensual** hardcodeado (Navegación Web $150/mes, Lectura de Documentos $120/mes, Gestión de Calendarios $100/mes, Consulta de Información $130/mes, Ejecución de Tareas $160/mes) usado para mostrar el desglose (subtotal por skill = precio mensual × 6 meses; la suma de subtotales es exactamente igual al importe total de la tabla, sin excepción).

### 4.5 Log de errores — mínimo 6, usar estos 7

| Timestamp | Agente | Tipo | Descripción |
|---|---|---|---|
| 2026-08-22 14:32 | Nova Support Agent | Critical | Fallo de conexión con el servicio de calendario externo tras 3 reintentos. |
| 2026-08-22 09:10 | Atlas Research Agent | Timeout | La navegación web excedió el tiempo máximo de respuesta (30s). |
| 2026-08-21 22:47 | Orion Ops Agent | Authentication | Token de acceso expirado al intentar leer el documento adjunto. |
| 2026-08-21 18:05 | Nova Support Agent | Warning | Uso elevado de memoria durante la ejecución de una tarea programada. |
| 2026-08-20 11:52 | Helix Sales Agent | Error | No se pudo completar la tarea de negocio: parámetro de entrada inválido. |
| 2026-08-19 08:15 | Vega Analyst Agent | Critical | El agente dejó de responder durante la consulta de datos internos. |
| 2026-08-18 16:40 | Atlas Research Agent | Warning | Límite de peticiones alcanzado en la fuente de datos externa. |

Cada entrada tiene además, solo visible en el modal de detalle, una **traza completa** hardcodeada (bloque de texto tipo stack trace, monoespaciado, 4-8 líneas realistas) y un campo de estado `resuelto: false` inicial, mutable en memoria al pulsar "Marcar como resuelto".

---

## 5. Paleta y lenguaje visual

Producto SaaS profesional, no una demo genérica.

- **Color base neutro:** escala `slate` de Tailwind para fondos, bordes y texto secundario (`bg-slate-50`, `bg-white`, `dark:bg-slate-900`, `dark:bg-slate-950`, `border-slate-200`, `dark:border-slate-800`).
- **Color de marca/acento primario:** `indigo` (botones primarios, enlace activo del sidebar, focus rings) — `bg-indigo-600`, `hover:bg-indigo-700`, `dark:bg-indigo-500`.
- **Acentos de métricas** (una familia de color distinta por tarjeta, usados con moderación en icono/borde superior, no como fondo sólido saturado):
  - Ingresos → `emerald`.
  - Pérdidas por descuentos → `amber`.
  - Agentes activos → `sky`.
  - Agentes fallando → `rose`.
- **Badges de estado** (usuarios/agentes/contratos): `emerald` (Activo), `slate` (Inactivo), `rose` (Fallando/Suspendido/Vencido), `amber` (Pendiente de pago).
- **Badges de severidad de error:** `rose` (Critical), `orange` (Error), `amber` (Warning), `sky` (Timeout), `violet` (Authentication).
- Sombras sutiles (`shadow-sm`, `shadow-md` en hover), radios de borde consistentes (`rounded-lg`/`rounded-xl`), sin gradientes decorativos, sin glassmorphism, sin animaciones llamativas (solo transiciones funcionales: `transition-colors`, `transition-all duration-200` para dropdowns/modales/skills colapsables).
- Tipografía: fuente por defecto del sistema vía Tailwind (`font-sans`), jerarquía clara con `text-sm`/`text-base`/`text-lg`/`text-xl`/`text-2xl` y pesos `font-medium`/`font-semibold`/`font-bold`.

---

## 6. Inventario de componentes reutilizables

| Componente | Qué representa | Dónde se usa | Comportamiento |
|---|---|---|---|
| **Sidebar** | Navegación principal persistente con las 6 secciones | Global, fijo a la izquierda en desktop | Resalta el ítem activo (fondo `indigo-50`/`dark:indigo-950` + texto `indigo-600`), cambia la sección visible al hacer clic, sin recarga |
| **Header** | Barra superior persistente | Global, encima de `<main>` | Muestra el título de la sección activa + descripción corta + toggle de dark mode + bloque de "Admin" (avatar iniciales + nombre + rol) |
| **Metric Card** | Tarjeta de métrica individual | Dashboard (×4) | Icono + etiqueta + valor + contexto secundario; color de acento propio; `hover:shadow-md` |
| **Dropdown de acciones (`⋮`)** | Menú contextual de acciones por fila/ítem | Usuarios, Agentes, Skills, Contrataciones, Errores | Se abre con clic en botón `⋮`, se cierra con clic fuera o segundo clic, solo un dropdown abierto a la vez en toda la página |
| **Modal** | Overlay de detalle/edición | Detalle de usuario, Configurar agente, Detalle de skill, Detalle de contrato, Detalle de error | Backdrop semitransparente + panel centrado; botón `✕` de cierre; cierre al clic en backdrop; el clic dentro del panel nunca cierra (usa `stopPropagation`) |
| **Badge** | Etiqueta de estado con color semántico | Usuarios, Agentes, Contrataciones | Pastilla `rounded-full`, `text-xs`, `font-medium`, fondo suave + texto saturado del mismo color |
| **Skill Chip** | Etiqueta compacta representando una skill | Agentes (skills colapsables), Contrataciones (skills contratadas), Skills (referencias cruzadas) | `rounded-full` o `rounded-md`, fondo `indigo-50`/`dark:indigo-950`, texto `indigo-700`/`dark:indigo-300` |
| **Lista de skills colapsable** | Contenedor expandible de Skill Chips por agente | Sección Agentes, dentro de cada fila/tarjeta de agente | Colapsada por defecto; botón con icono chevron que rota; expande con `max-height`/`grid-template-rows` + `transition-all duration-300`; toggle independiente por agente |
| **Toggle de dark mode** | Interruptor sol/luna | Header | Alterna clase `dark` en `<html>`; persiste elección en `localStorage` bajo la clave `agenthub-theme` |
| **Data Table** | Tabla de datos tabular | Usuarios, Contrataciones, Log de errores | `<table>` semántica con `<thead>`/`<tbody>`, filas con `hover:bg-slate-50 dark:hover:bg-slate-800/60`, scroll horizontal (`overflow-x-auto`) en viewports estrechos |
| **Empty/placeholder state** | Mensaje de "sin datos" | Reutilizable en cualquier tabla/listado si un futuro filtro no devolviera resultados (se documenta el componente aunque los datasets siempre tengan contenido) | Icono + texto centrado + `text-slate-400 dark:text-slate-500` |
| **Section header** | Encabezado interno de cada sección de contenido | Dentro de cada una de las 6 `<section>` | `<h2>` con título + descripción corta +, cuando aplica, botón de acción primario alineado a la derecha (ej. "Nuevo usuario", decorativo, sin funcionalidad real) |
| **Error severity badge** | Badge especializado de severidad para el log de errores | Log de errores | Igual mecánica que Badge pero con la paleta de severidad de la sección 5 |

---

## 7. Especificaciones por sección (mínimo 3 por sección, nivel implementable)

### 7.1 Dashboard

1. **Grid de métricas.** Cuatro `Metric Card` en una cuadrícula `grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4`. Cada tarjeta: contenedor `rounded-xl border p-5 shadow-sm bg-white dark:bg-slate-900`, con un icono SVG inline dentro de un círculo de color de acento (`bg-emerald-100 text-emerald-600 dark:bg-emerald-900/40 dark:text-emerald-400` etc.), la etiqueta en `text-sm text-slate-500`, el valor principal en `text-2xl font-bold`, y una línea de contexto secundario en `text-xs` con color semántico (verde si es positivo, rojo si es negativo). Valores: Ingresos totales este mes = `$48,320`, contexto "+12% vs. mes anterior"; Pérdidas por descuentos y cupones = `$3,150`, contexto "6.5% de los ingresos brutos"; Agentes activos = `3`, contexto "de 5 agentes totales"; Agentes fallando = `1`, contexto "requiere atención — Nova Support Agent".
2. **Gráfico de actividad semanal.** Debajo del grid, un `<article>` de ancho completo (`w-full rounded-xl border p-6 bg-white dark:bg-slate-900`) con título "Actividad semanal de agentes" y subtítulo "Ejecuciones completadas por día (últimos 7 días)". El cuerpo es un placeholder visual elaborado: 7 barras verticales (una por día, etiquetadas Lun–Dom) construidas con `<div>`s de altura variable hardcodeada (ej. `h-24`, `h-40`, `h-28`, `h-48`, `h-36`, `h-16`, `h-20`) en `bg-indigo-500 dark:bg-indigo-500/80 rounded-t-md`, alineadas sobre una línea base, con el valor numérico encima de cada barra en `text-xs`. No se usa ninguna librería de gráficos.
3. **Resumen rápido lateral opcional.** A la derecha del gráfico (o debajo en tablet, usando `flex-col lg:flex-row`), una lista compacta de 3 "insights" hardcodeados con icono pequeño y una frase cada uno (ej. "Helix Sales Agent es el agente con más ejecuciones esta semana", "2 contratos vencen en los próximos 30 días", "El plan Pro concentra el 50% de los usuarios activos"), en `text-sm` con separadores `divide-y divide-slate-100 dark:divide-slate-800`.

### 7.2 Gestión de usuarios

1. **Tabla de usuarios.** `Data Table` con columnas Nombre, Email, Plan, Estado, Acciones, poblada con los 6 usuarios de la sección 4.1. La columna Nombre muestra además un avatar circular con las iniciales sobre fondo `indigo-100 dark:indigo-900/40`. La columna Plan se muestra como texto simple (`Free`/`Pro`/`Enterprise`), no badge. La columna Estado usa `Badge` (Activo=emerald, Suspendido=rose, Pendiente de pago=amber).
2. **Dropdown de acciones por fila.** Botón `⋮` al final de cada fila abre un `Dropdown` con dos opciones: "Ver detalle" y "Eliminar" (esta última en `text-rose-600`). Solo un dropdown de usuario puede estar abierto a la vez; se cierra al hacer clic fuera.
3. **Modal de detalle de usuario.** "Ver detalle" abre un `Modal` con: avatar grande + nombre + email en la cabecera, y debajo una sección de información ampliada en formato de lista clave-valor (`dl`/`dt`/`dd`): Empresa, Plan, Estado (badge), Fecha de alta (hardcodear una fecha por usuario, ej. "12 de enero de 2026"), Agentes contratados (derivar del dataset de contrataciones filtrando por empresa del usuario — debe mostrar al menos el nombre del agente correspondiente), Total gastado (derivar del importe de su/s contrato/s). El modal tiene botón `✕` arriba a la derecha y se cierra al clic en backdrop.
4. **Acción Eliminar.** Al pulsar "Eliminar" en el dropdown se abre un segundo `Modal` de confirmación ("¿Eliminar a Laura Gómez? Esta acción no se puede deshacer.") con botones "Cancelar" y "Eliminar" (`bg-rose-600`). Al confirmar, la fila se oculta visualmente de la tabla en memoria (usando el estado de la app, sin recargar) y se muestra una notificación toast breve ("Usuario eliminado") en la esquina inferior derecha que desaparece sola tras ~2.5s. No se elimina del array fuente, solo se filtra en el render.

### 7.3 Gestión de agentes

1. **Listado de agentes.** Grid de tarjetas (`grid grid-cols-1 lg:grid-cols-2 gap-4`) o tabla — se elige **tarjetas** (`article` por agente, `rounded-xl border p-5 bg-white dark:bg-slate-900`) porque cada agente necesita espacio para el bloque de skills colapsable. Cada tarjeta muestra: nombre del agente (`text-lg font-semibold`), propietario (empresa, `text-sm text-slate-500`), `Badge` de estado (Activo=emerald, Inactivo=slate, Fallando=rose) alineado a la derecha del nombre, y el botón `⋮` de acciones en la esquina superior derecha.
2. **Skills colapsables.** Debajo de la info principal, un botón de texto "Ver skills (3)" con icono chevron-down, colapsado por defecto (`max-height-0 overflow-hidden opacity-0`). Al hacer clic, expande (`max-height` suficiente, `opacity-100`) con `transition-all duration-300 ease-in-out` mostrando los `Skill Chip` del agente en `flex flex-wrap gap-2`; el chevron rota 180° (`transition-transform`); el texto del botón cambia a "Ocultar skills". Un segundo clic vuelve a colapsar. Cada agente controla su propio estado de forma independiente (expandir uno no afecta a los demás).
3. **Dropdown de acciones.** El botón `⋮` abre "Configurar" y "Eliminar" (`text-rose-600`). Mismo comportamiento de apertura/cierre que en Usuarios (un solo dropdown global abierto a la vez, cierre al clic fuera).
4. **Modal Configurar.** Abre un `Modal` con el nombre del agente en el título, un campo de solo-lectura "Propietario" y "Estado" arriba, y debajo una etiqueta "Prompt de sistema" seguida de un `<textarea>` (`w-full h-48 rounded-md border font-mono text-sm p-3`, editable, precargado con el prompt hardcodeado del agente de la sección 4.3). Botones "Cancelar" y "Guardar cambios" (el guardado es visual: cierra el modal y muestra un toast "Cambios guardados", no persiste tras recargar).

### 7.4 Skills

1. **Explicación introductoria.** Al inicio de la sección, un bloque (`rounded-lg border-l-4 border-indigo-500 bg-indigo-50 dark:bg-indigo-950/30 p-4`) con el título "¿Qué es una skill?" y un párrafo: "Una skill es una capacidad modular que se puede habilitar en un agente para ampliar lo que es capaz de hacer, como navegar por la web, leer documentos, gestionar calendarios o ejecutar tareas de negocio. Los administradores pueden ver qué skills existen, cuántos agentes las tienen habilitadas y gestionarlas desde este catálogo."
2. **Catálogo de skills.** Grid de tarjetas (`grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4`), una por skill de la sección 4.2 (5 tarjetas). Cada tarjeta (`article rounded-xl border p-5`) muestra: icono representativo, nombre (`font-semibold text-base`), descripción breve (`text-sm text-slate-500 line-clamp-2`), y un contador "X agentes habilitados" con un pequeño icono de usuarios, calculado dinámicamente contando cuántos agentes del dataset 4.3 incluyen esa skill.
3. **Dropdown y modal de detalle.** Cada tarjeta tiene botón `⋮` con "Ver detalle" y "Eliminar". "Ver detalle" abre un `Modal` con: nombre + descripción completa (más extensa que la de la tarjeta, hardcodear 2-3 frases), lista de agentes que la tienen habilitada (derivada del dataset, con `Skill Chip`-like badges de agente o simplemente lista con viñetas), y una sección "Casos de uso típicos" con 2-3 bullets hardcodeados específicos de esa skill.

### 7.5 Contrataciones de agentes

1. **Tabla de contrataciones.** `Data Table` con columnas Cliente, Agente, Skills, Inicio, Fin, Importe total, Estado, Acciones, poblada con las 5 filas de la sección 4.4. La columna Skills muestra hasta 2 `Skill Chip` visibles + un indicador "+N" si hay más (todas las contrataciones de este dataset tienen 2-3 skills, mostrar todas si caben, o comprimir con "+1" cuando sean 3). La columna Estado usa `Badge` (Activo=emerald, Vencido=rose, Pago pendiente=amber).
2. **Dropdown de acciones.** Botón `⋮` con al menos "Ver detalle" (puede incluir opcionalmente "Marcar como pagado" si el estado es "Pago pendiente", decorativo). Mismo patrón global de apertura/cierre de dropdowns.
3. **Modal de detalle de contrato.** "Ver detalle" abre un `Modal` amplio con: Cliente + Agente en la cabecera, `dl` con Periodo (inicio – fin), Estado (badge), Importe total; debajo una **tabla de desglose** (`Skill`, `Precio mensual`, `Meses`, `Subtotal`) usando los precios individuales hardcodeados de la sección 4.4, con una fila final "Total" en negrita que debe coincidir con el importe total de la tabla principal.
4. **Indicador de vigencia.** Junto a la fecha de fin en la tabla y en el modal, cuando el contrato está "Activo" y faltan menos de 45 días para su fin (calculado respecto a la fecha de sistema simulada `2026-08-23`, hardcodeada como constante `TODAY` en JS, no `new Date()` real), se muestra un pequeño indicador de texto `text-amber-600 text-xs` "Vence pronto". Esto aplica al contrato de Quantiva (fin 2026-08-01 ya vencido → no aplica, se muestra "Pago pendiente" tal cual) — verificar y ajustar el ejemplo que sí aplique si ninguno cae en rango; si ningún contrato cae naturalmente en la ventana de 45 días, este comportamiento se documenta igualmente como parte del componente pero puede no dispararse visualmente con el dataset actual (no bloquea la aceptación).

### 7.6 Log de errores

1. **Listado de errores.** Tabla o lista de `article` — se elige **lista de `article`** (`space-y-3`) porque cada entrada necesita espacio para descripción + severidad + acciones sin saturar una tabla. Cada `article` (`rounded-lg border p-4 flex items-start justify-between gap-4`, con `opacity-60` y una marca "Resuelto" cuando `resuelto: true`) muestra: `Error severity badge` + nombre del agente (`font-medium`) + timestamp (`text-xs text-slate-400`) en la cabecera de la fila, y debajo la descripción breve (`text-sm`). Poblado con las 7 entradas de la sección 4.5, ordenadas de más reciente a más antigua.
2. **Badges de severidad diferenciados.** Cada tipo de error usa el color de la sección 5 (Critical=rose, Error=orange, Warning=amber, Timeout=sky, Authentication=violet), pastilla `rounded-full text-xs font-medium px-2.5 py-0.5`.
3. **Dropdown y modal de detalle.** Botón `⋮` por entrada con "Ver detalle" y "Marcar como resuelto" (esta opción se oculta o se deshabilita —`text-slate-300 cursor-not-allowed`— si la entrada ya está resuelta). "Ver detalle" abre un `Modal` con: Agente + Timestamp + Tipo (badge) en la cabecera, "Mensaje" (la descripción breve) y debajo "Traza completa" dentro de un bloque `<pre>` monoespaciado (`bg-slate-900 text-slate-100 dark:bg-black rounded-md p-4 text-xs overflow-x-auto`) con el stack trace hardcodeado de esa entrada.
4. **Marcar como resuelto.** Al pulsar esta acción (desde el dropdown o desde dentro del modal), el estado en memoria de esa entrada cambia a `resuelto: true`: la tarjeta se atenúa (`opacity-60`) y se le añade un `Badge` adicional "Resuelto" (`slate`) junto al badge de severidad; si el modal estaba abierto se refleja el cambio sin necesidad de cerrarlo.

---

## 8. Navegación global (sidebar)

1. Sidebar fijo a la izquierda en desktop (`w-64 shrink-0 border-r bg-white dark:bg-slate-900 dark:border-slate-800`, `flex flex-col`, altura completa `h-screen sticky top-0`), con el logotipo/nombre "AgentHub" arriba (icono + texto, sin logo real, texto `font-bold text-lg`) y debajo un `<nav>` con 6 `<button>` (no `<a>`, ya que no hay navegación real de documento), uno por sección, cada uno con icono SVG inline + label.
2. El botón de la sección activa tiene fondo `bg-indigo-50 dark:bg-indigo-950/50`, texto `text-indigo-600 dark:text-indigo-400 font-medium`, y un borde izquierdo de acento (`border-l-2 border-indigo-600`); los inactivos usan `text-slate-600 dark:text-slate-300 hover:bg-slate-100 dark:hover:bg-slate-800`.
3. Al hacer clic en un botón del sidebar: se oculta la `<section>` visible y se muestra la seleccionada (toggle de `hidden`), se actualiza la clase activa del botón correspondiente, se actualiza el título/descripción del Header, y se cierra cualquier dropdown o modal que estuviera abierto. No hay recarga de página ni cambio de URL.
4. En viewport de tablet (`md`, ancho ≥768px y <1024px) el sidebar se mantiene visible pero colapsado a solo iconos (`w-16`, labels con `sr-only` o `hidden md:hidden lg:inline`, tooltips no obligatorios); en escritorio (`lg:` ≥1024px) se muestra expandido con labels visibles. No se requiere soporte para móvil estrecho como prioridad de este prototipo (foco en desktop/tablet según sección 16 del prompt original), pero el layout no debe romperse visualmente por debajo de tablet.

---

## 9. Header

1. Barra superior persistente (`h-16 border-b bg-white dark:bg-slate-900 dark:border-slate-800 flex items-center justify-between px-6`), fuera del `<nav>` del sidebar, dentro de `<header>`.
2. A la izquierda: título de la sección activa (`h1`, `text-lg font-semibold`) + una línea descriptiva corta debajo (`text-xs text-slate-500`) que cambia según la sección (ej. Dashboard → "Resumen general de la plataforma"; Usuarios → "Gestiona las cuentas de clientes"; etc., una frase por sección).
3. A la derecha: `Toggle de dark mode` (botón circular con icono sol/luna que cambia según el tema activo) + separador vertical (`border-l h-6`) + bloque de administrador: avatar circular con iniciales "AD" (`bg-indigo-600 text-white`) + nombre "Ana Duarte" + rol "Admin de Plataforma" en `text-xs text-slate-500`, sin funcionalidad de menú desplegable (puramente informativo, decorativo).

---

## 10. Modo claro / oscuro

1. Toggle en el Header alterna la clase `dark` en `<html>` mediante `document.documentElement.classList.toggle('dark')`.
2. El icono del toggle cambia entre sol (modo claro activo, ofrece pasar a oscuro) y luna (modo oscuro activo, ofrece pasar a claro).
3. Todos los componentes usan pares de clases `bg-white dark:bg-slate-900`, `text-slate-900 dark:text-slate-100`, `border-slate-200 dark:border-slate-800`, etc. — nunca color fijo que no varíe con el tema, salvo colores semánticos deliberados (ej. `bg-rose-600` de un botón destructivo, que ya funciona en ambos temas).
4. Persistencia: al cambiar el tema se guarda `localStorage.setItem('agenthub-theme', 'dark'|'light')`. Al cargar la página, un script inline **antes** del CDN de Tailwind (o inmediatamente después, ejecutado de forma síncrona antes del primer paint relevante) lee `localStorage.getItem('agenthub-theme')` y aplica la clase `dark` a `<html>` si corresponde, evitando parpadeo. Si no hay valor guardado, se usa `prefers-color-scheme: dark` como fallback inicial.
5. El tema se mantiene al navegar entre las 6 secciones (no depende de la sección, es un estado global de `<html>`).

---

## 11. Interacciones globales — reglas de implementación

### Dropdowns
- Cada dropdown se identifica con un `data-dropdown-id` único. Existe una única variable de estado JS (`let openDropdownId = null`) que trackea cuál está abierto.
- Al pulsar un botón `⋮`: si su dropdown ya está abierto, se cierra; si no, se cierra cualquier otro dropdown abierto y se abre el suyo.
- Un listener global en `document` (`click`) cierra el dropdown abierto si el clic ocurre fuera de su contenedor (`.closest()`), usando `stopPropagation()` en el botón `⋮` para que su propio clic no dispare inmediatamente el cierre.
- Los dropdowns se posicionan con `absolute right-0 mt-2` respecto a un contenedor `relative`, con `z-20` y `shadow-lg border rounded-md bg-white dark:bg-slate-800`.

### Modales
- Cada modal tiene una función `openModal(id)` / `closeModal(id)` que alterna `hidden` en el contenedor `fixed inset-0 z-50`.
- Estructura: backdrop (`absolute inset-0 bg-slate-900/50 dark:bg-slate-950/70`) + panel (`relative bg-white dark:bg-slate-900 rounded-xl shadow-xl max-w-lg w-full mx-4`, con `stopPropagation()` en el clic del panel).
- Botón `✕` siempre presente en la esquina superior derecha del panel.
- Clic en el backdrop cierra el modal (listener en el backdrop, no en el panel).
- Abrir un modal cierra cualquier dropdown abierto.

### Skills colapsables
- Estado por agente en un `Set` o mapa (`expandedAgents`), inicialmente vacío (todo colapsado).
- Clic en el botón de toggle añade/quita el id del agente del set y vuelve a renderizar solo ese bloque (o alterna clases directamente sobre el nodo sin re-render completo).
- Transición con `transition-all duration-300 ease-in-out` sobre `max-height`/`opacity`.

### Navegación
- Función `showSection(sectionId)` central: oculta todas las `<section>`, muestra la elegida, actualiza estilos activos del sidebar y el texto del header, cierra dropdowns/modales abiertos.

### Dark mode
- Función `setTheme(mode)` central que aplica la clase, actualiza el icono del toggle y guarda en `localStorage`.

---

## 12. Restricciones técnicas y accesibilidad

- HTML semántico obligatorio: `<nav>`, `<header>`, `<main>`, `<section>` (una por módulo), `<table>`/`<thead>`/`<tbody>`/`<tr>`/`<th>`/`<td>` en las tablas, `<article>` para tarjetas/entradas individuales (métricas, agentes, skills, errores), `<button>` para todo elemento interactivo que no navega, `<dl>`/`<dt>`/`<dd>` para bloques clave-valor en modales.
- Los modales pueden implementarse con `<div>` + roles ARIA (`role="dialog"`, `aria-modal="true"`) en lugar de `<dialog>` nativo, para tener control total sobre el backdrop y las transiciones con Tailwind; esta es una decisión de diseño explícita y válida dentro de "elementos semánticos equivalentes".
- Todo elemento interactivo (`button`) debe tener estado `hover:` y `focus-visible:ring-2 focus-visible:ring-indigo-500` visibles.
- Sin `style="..."` inline en ningún elemento; alturas variables (ej. barras del gráfico) se resuelven con clases utilitarias de altura predefinidas (`h-16`, `h-20`, `h-24`...), no con `style="height:...px"`.
- Sin archivos `.css` externos ni `<style>` con reglas propias (aparte del `<script>` de configuración de `tailwind.config`, que no es CSS).
- Responsive: layout probado visualmente para desktop (≥1280px) y tablet (768–1023px), usando breakpoints `md:`/`lg:` de Tailwind en grids de métricas, tablas (`overflow-x-auto`) y sidebar (colapso a iconos en tablet, sección 8.4).

---

## 13. Criterios de aceptación

1. `SPECS.md` existe en la raíz del repositorio.
2. `SPECS.md` fue commiteado en un commit dedicado, antes de cualquier archivo HTML.
3. Existe un commit independiente exclusivo para `SPECS.md` (`docs: add AgentHub admin panel specification`).
4. Existe un `index.html` funcional en la raíz.
5. Las seis secciones (Dashboard, Usuarios, Agentes, Skills, Contrataciones, Log de errores) están implementadas dentro de `index.html`.
6. Las seis secciones son accesibles desde el sidebar mediante clic.
7. El sidebar mantiene un indicador visual claro de la sección activa.
8. Dashboard contiene cuatro tarjetas de métricas (Ingresos, Pérdidas, Agentes activos, Agentes fallando).
9. Dashboard contiene el placeholder de gráfico de actividad semanal de ancho completo.
10. Usuarios contiene al menos 5 registros (dataset incluye 6).
11. Agentes contiene al menos 4 registros (dataset incluye 5).
12. Skills contiene al menos 4 registros (dataset incluye 5).
13. Contrataciones contiene al menos 4 registros (dataset incluye 5).
14. Log de errores contiene al menos 6 registros (dataset incluye 7).
15. Cada tabla/listado (Usuarios, Agentes, Skills, Contrataciones, Errores) dispone de dropdown de acciones funcional por fila/ítem.
16. Los dropdowns se cierran al hacer clic fuera de su área.
17. Solo un dropdown permanece abierto a la vez.
18. "Ver detalle" abre modales en las 5 secciones con listados (Usuarios, Agentes vía "Configurar", Skills, Contrataciones, Errores) — al menos 4 secciones distintas cumplidas.
19. Todos los modales tienen botón de cierre (`✕`).
20. Todos los modales se cierran al hacer clic en el backdrop, y no se cierran al hacer clic dentro del panel.
21. Las skills de los agentes están colapsadas por defecto al cargar la página.
22. Las skills se expanden y colapsan mediante interacción de clic, de forma independiente por agente.
23. La expansión/colapso de skills tiene una transición visual suave (`transition-all`).
24. El prompt de sistema de cada agente aparece en un `<textarea>` editable dentro del modal "Configurar".
25. El toggle del header cambia entre modo claro y oscuro.
26. El dark mode se implementa exclusivamente con clases `dark:` de Tailwind (config `darkMode: 'class'`).
27. El modo (claro/oscuro) seleccionado se mantiene al navegar entre las seis secciones.
28. El modo seleccionado persiste en `localStorage` y se restaura al recargar la página.
29. Todos los datos del panel son hardcodeados en JavaScript (sin fetch, sin API).
30. Los nombres de agentes son idénticos y consistentes entre Agentes, Contrataciones y Log de errores.
31. Las skills son idénticas y consistentes entre Agentes, Skills y Contrataciones.
32. Los nombres de usuarios/empresas son consistentes entre Usuarios y Contrataciones.
33. No existe ningún archivo `.css` personalizado ni bloque `<style>` con reglas de estilo propias.
34. No existen atributos `style="..."` inline en el HTML.
35. No se utiliza ningún framework o librería JavaScript (React/Vue/Angular/jQuery) ni TypeScript.
36. Se utiliza HTML semántico (`nav`, `header`, `main`, `section`, `table`, `thead`, `tbody`, `article`, `button`, `dl`).
37. El layout funciona correctamente en resolución de escritorio (≥1280px).
38. El layout funciona correctamente en resolución de tablet (768–1023px), incluyendo el colapso a iconos del sidebar.
39. No existen errores en la consola de JavaScript durante el uso normal (navegación, dropdowns, modales, skills, dark mode).
40. Los elementos interactivos (`button`) tienen estados `hover:` y `focus-visible:` perceptibles.
41. Todos los requisitos funcionales de este documento pueden demostrarse manualmente abriendo `index.html` en un navegador, sin backend.

---

## 14. Flujo de trabajo y control de versiones

1. `SPECS.md` se commitea solo, antes de crear `index.html` (commit: `docs: add AgentHub admin panel specification`).
2. Referencia visual de Google Stitch (si la herramienta está disponible en el entorno) se usa solo como inspiración de layout/estilo; en caso de conflicto con este documento, **este documento prevalece**.
3. `index.html` se construye completo según las secciones 1–13 y se commitea en un commit separado (commit: `feat: build AgentHub admin panel prototype`).
4. Antes de considerar terminado el trabajo, se valida manualmente cada criterio de la sección 13.
