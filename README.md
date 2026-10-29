# Módulo Nativo de Trazabilidad y Gráficos Históricos de Precios

**Caso de estudio:** Mercado Libre — Organización Empresarial
**Institución:** Tecnicatura Universitaria en Programación — Universidad Tecnológica Nacional (UTN)
**Docente titular:** Prof. Mario Raúl López
**Docente tutor:** Prof. Eduardo Martinez

## Integrantes:
Laureano Emanuel D´Agostino



## Enlaces de acceso:
- Repositorio público: `https://github.com/laureanodagostino10-sys/trazabilidad_precios_ml`
- Tablero ágil (GitHub Projects): `https://github.com/users/laureanodagostino10-sys/projects/1`

---

## Descripción del sistema:
En categorías de alta volatilidad (placas de video, procesadores, monitores y periféricos de PC) los precios cambian sin dejar registro visible para el consumidor, que termina recurriendo a monitores externos como HardGamers, donde Mercado Libre no está indexado.

El módulo propuesto:
**Registra** cada cambio de precio como un registro nuevo (serie temporal inmutable), sin sobrescribir el valor anterior.
**Cruza** ese historial con campañas promocionales (por ejemplo: Cyber Monday) para detectar inflación artificial previa al descuento.
**Modera** automáticamente: quita la insignia "Oferta" a las publicaciones infladas, suma un intento al vendedor y, si es reincidente, deriva el caso a Auditoría para bloquear su participación en el evento.
**Muestra** al comprador un gráfico de líneas con la curva de precios y la insignia "Descuento genuino" directamente en la publicación.

### Organigrama del equipo

Jefatura de Proyecto, con Auditoría como staff, y tres áreas: Desarrollo (Frontend, Backend, QA), Datos (Bases de Datos, Trazabilidad de Precios) e Infraestructura (Operaciones, Despliegue/DevOps).

### Modelo de datos

| Entidad | Atributos principales |
|---|---|
| `publicacion_hardware` | `id_publicacion`, `id_vendedor`, `categoria_volatilidad`, `marca_descuento_real` |
| `serie_temporal_precio` | `id_registro`, `id_publicacion`, `valor_registrado`, `fecha_captura` |
| `vendedor_moderacion` | `id_tienda`, `reputacion_actual`, `intentos_inflacion`, `estado_penalizacion` |

---

## 2. Modelado BPMN (Bizagi Modeler)

Archivos en la raíz del repositorio: `Nuevo Modelo.bpm` (fuente de Bizagi) y `Diseño BPMN.png` (exportación).

![Diagrama BPMN del proceso](Dise%C3%B1o%20BPMN.png)

**Pools**

- Vendedor (actor externo)
- Mercado Libre (organización)
- Comprador (actor externo)

**Lanes del pool Mercado Libre**

- Backend (automático)
- Moderación (automático)
- Auditoría (rol humano)
- Frontend (automático)

**Flujo estándar**

1. El vendedor modifica el precio.
2. Backend registra un nuevo precio en la base de datos, sin pisar el anterior.
3. Backend compara el historial con el precio de oferta.
4. Frontend lee la serie temporal, renderiza el gráfico y muestra el estado.
5. El comprador ve el gráfico y la insignia, y decide la compra.

**Compuertas de decisión (excepciones)**

| Compuerta | Condición | Camino "Sí" | Camino "No" |
|---|---|---|---|
| G1 | ¿Hay campaña activa? | Comparar historial vs. oferta | Va directo a Frontend |
| G2 | ¿Hay inflación artificial? | Quitar insignia "Oferta" y sumar intento (`intentos_inflacion + 1`) | Va a Frontend con "Descuento genuino" |
| G3 | ¿El vendedor es reincidente? (`intentos_inflacion` ≥ umbral) | Auditoría revisa y bloquea (`estado_penalizacion = True`) | Va a Frontend |

---

## 3. Flujo de trabajo Git/GitHub

**Estrategia de ramas**

- `main`: versión estable y entregable. Los cambios principales entran mediante Pull Request.
- `docs/bpmn`: rama secundaria donde se desarrolló el modelado BPMN. Su cambio se integró a `main` mediante el Pull Request #1, con revisión de una segunda cuenta.

---

## 4. Backlog ágil (GitHub Projects)

Tablero con columnas **Por Hacer → En Progreso → Listo**. Cada historia es un *Issue* vinculado al tablero y a su PR.

| ID | Historia de usuario | Criterio de aceptación | Estado |
|---|---|---|---|
| HU-01 | Como **comprador**, quiero ver un gráfico de líneas con el historial de precios dentro de la publicación, para verificar si un descuento es real sin salir de Mercado Libre. | El gráfico se renderiza con datos del histórico, con eje de fechas y valores.
| HU-02 | Como **analista de datos**, quiero que cada cambio de precio se guarde como un registro nuevo sin sobrescribir el anterior, para contar con una serie temporal auditable. | Tras 7 días hay un registro por cada cambio, sin pérdida ni pisado.
| HU-03 | Como **auditor de la plataforma**, quiero que el sistema detecte inflación artificial previa a una campaña y retire la insignia "Oferta", para proteger la confianza de los compradores. | El algoritmo detecta el 100 % de las anomalías del lote de prueba.

---

## 5. Mecanismos de control e indicadores

| Indicador | Fórmula | Estándar (meta) | Fase / Semana |
|---|---|---|---|
| Cobertura de maquetación | Vistas maquetadas / vistas requeridas | 100 % | Diseño (Semana 2) |
| Integridad de la serie temporal | Registros conservados / cambios de precio ocurridos | 100 % sin sobrescrituras (50 artículos, 7 días) | Codificación (Semana 5) |
| Tasa de detección de anomalías | Anomalías detectadas / anomalías inyectadas | 100 % (lote de 100 publicaciones) | QA (Semana 6) |
| Tasa de falsos positivos | Publicaciones legítimas penalizadas / total legítimas | ≤ 5 % | QA (Semana 6) |
| Tiempo de render del gráfico | Segundos hasta mostrar el gráfico | ≤ 2 s | QA (Semana 6) |
| Retención de tráfico | Visitas con gráfico que no salen a sitios externos / total | Seguimiento posterior al despliegue | Implementación (Semanas 7+) |
| Trazabilidad del repositorio | Commits descriptivos / commits totales | ≥ 90 % | Continuo |
| Avance del backlog | Historias en "Listo" / historias totales | 100 % al cierre | Continuo |

---

## 6. Declaración de uso de IA

### Herramientas utilizadas

| Herramienta | Uso declarado |
|---|---|
| Gemini | Redacción, síntesis del contenido, orden del organigrama y diseño de imágenes. |
| Perplexity | Búsqueda de información y validación de documentación. |
| Claude | Estructuración de la entrega integradora: BPMN, README, backlog e indicadores. |
