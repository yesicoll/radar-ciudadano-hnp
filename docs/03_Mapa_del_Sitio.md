# MAPA DEL SITIO Y ARQUITECTURA DE INFORMACIÓN (SITEMAP)

**Plataforma Web:** Radar Ciudadano HNP – Archivo Digital y Observatorio Comunitario

**Proyecto:** Hechos No Palabras (HNP) – Cunduacán, Tabasco

**Unidad Receptora:** ALLS BASIC SUPPLY COMPANY SA DE CV

**Estudiante / Desarrollador:** Yesenia Sarai Martínez Collado

**Responsable del Proyecto:** Alexis Miguel Lazo Fuentes

**Fase de Desarrollo:** Fase 1 – Análisis y Planeación del Proyecto (Semanas 1 y 2)

**Versión del Documento:** 1.0

---

## 1. ESTRUCTURA JERÁRQUICA DE NAVEGACIÓN

La plataforma **Radar Ciudadano HNP** está dividida en dos grandes módulos independientes: la **Interfaz Pública Ciudadana (PWA)** accesible a toda la comunidad sin autenticación obligatoria, y el **Panel Administrativo de Moderación (Backoffice)** protegido por credenciales JWT.

```
RADAR CIUDADANO HNP
├── 1. Portal Público (PWA Ciudadana)
│   ├── [1.0] Inicio (Landing Page)
│   │   └── [1.0.1] Modal: Formulario "Reporta un Caso" (Captura + GPS Satelital)
│   ├── [1.1] Casos Ciudadanos (Catálogo de expedientes)
│   │   └── [1.1.1] Detalle de Expediente (`/casos/:id` - Cronología + Galería)
│   ├── [1.2] Mapa Ciudadano Interactivo (Filtros por categoría y Centros Integradores)
│   ├── [1.3] Resultados y Seguimiento ("Antes" Rojo / "Después" Verde)
│   ├── [1.4] Biblioteca Ciudadana (Descarga de informes y documentos PDF)
│   ├── [1.5] Participa y Contacto (Enlace seguro Signal / Redes Sociales)
│   └── [1.6] Aviso de Privacidad y Términos Legal
│
└── 2. Panel Administrativo (Backoffice HNP)
    ├── [2.0] Inicio de Sesión / Auth (`/admin/login`)
    ├── [2.1] Dashboard Principal (Métricas e indicadores globales)
    ├── [2.2] Moderación de Reportes (Revisión de bandeja de entrada pública)
    ├── [2.3] Gestión de Expedientes (Edición, línea de tiempo, fotos "Antes/Después")
    ├── [2.4] Administración de Biblioteca (Carga y eliminación de documentos)
    └── [2.5] Gestión de Catálogos (Categorías, Localidades, Centros Integradores)
```

---

## 2. TABLA DE RUTAS Y VISTAS DEL FRONTEND (REACT ROUTER)

### 2.1 Rutas Públicas (PWA Ciudadana)

| Ruta / URL | Vista / Componente React | Descripción y Funcionalidades Principales | Acceso / Permiso |
| --- | --- | --- | --- |
| `/` | `HomeView.jsx` | Portada principal, Hero con barra de búsqueda global, contadores estadísticos en tiempo real, acceso rápido al modal de reporte y tarjeta del último caso resuelto. | Público |
| `/casos` | `CasesListView.jsx` | Repositorio de casos en vista de tarjetas con buscador por texto, filtro por categoría, filtro por estado y paginación. | Público |
| `/casos/:folio` | `CaseDetailView.jsx` | Expediente público individual. Muestra folio, datos del caso, georreferenciación en mini-mapa, fotos comparativas ("Antes y Después"), línea de tiempo (cronología) y dictamen. | Público |
| `/mapa` | `InteractiveMapView.jsx` | Mapa interactivo a pantalla completa (Leaflet.js) con marcadores por código de color por categoría, agrupamiento por Centros Integradores y popups de detalle rápido. | Público |
| `/resultados` | `ResultsView.jsx` | Sección dedicada al impacto del observatorio, mostrando casos cerrados con comparativa visual "Antes" (Rojo) y "Después" (Verde). | Público |
| `/biblioteca` | `LibraryView.jsx` | Grid de publicaciones, acuerdos comunitarios e informes en formato PDF con miniatura, buscador interno y descarga directa. | Público |
| `/contacto` | `ContactView.jsx` | Información de contacto del observatorio, enlaces a redes sociales oficiales y canal cifrado externo (Signal) para denuncias sensibles. | Público |
| `/privacidad` | `PrivacyView.jsx` | Términos de uso de la información, política de anonimato y tratamiento de datos personales conforme al marco legal. | Público |

### 2.2 Rutas Privadas (Panel Administrativo Backoffice)

| Ruta / URL | Vista / Componente React | Descripción y Funcionalidades Principales | Acceso / Permiso |
| --- | --- | --- | --- |
| `/admin/login` | `AdminLoginView.jsx` | Formulario de inicio de sesión de administradores del observatorio con autenticación JWT. | Público (No Auth) |
| `/admin/dashboard` | `AdminDashboardView.jsx` | Panel de control central con métricas clave: casos pendientes de moderar, casos en proceso, casos resueltos por mes y distribución por Centros Integradores. | Reclama Token JWT (`Admin`) |
| `/admin/moderacion` | `AdminModerationView.jsx` | Bandeja de entrada de reportes recibidos desde la web. Permite aprobar (convertir en expediente oficial), rechazar o solicitar corrección. | Reclama Token JWT (`Admin`) |
| `/admin/casos/:id/editar`| `AdminCaseEditView.jsx` | Edición completa de expediente: cambio de estado, edición de título/descripción, carga de fotos "Antes" y "Después", y actualización de cronología. | Reclama Token JWT (`Admin`) |
| `/admin/biblioteca` | `AdminLibraryView.jsx` | Formulario de carga de nuevos documentos PDF/Imágenes a la biblioteca y tabla de gestión de archivos existentes. | Reclama Token JWT (`Admin`) |
| `/admin/catalogos` | `AdminCatalogsView.jsx` | Mantenimiento (CRUD) de catálogos base del municipio de Cunduacán: Categorías, Centros Integradores y Localidades. | Reclama Token JWT (`Admin`) |

---

## 3. FLUJOS DE NAVEGACIÓN DE USUARIO (USER FLOWS)

### 3.1 Flujo A: Registro de Reporte Ciudadano en Campo (Modo Online / Offline)

1. **Ingreso:** El ciudadano accede al sitio web desde su dispositivo móvil o computadora.
2. **Acción Principal:** Presiona el botón destacado `"Reporta un Caso"` (Rojo).
3. **Modal de Captura:** Se despliega el formulario interactivo:
   * **Paso 1:** Presiona `"ENCUENTRA MI UBICACIÓN"`. El chip GPS del teléfono obtiene las coordenadas exactas. El marcador se ajusta en el mapa confirmando su presencia en Cunduacán, Tab.
   * **Paso 2:** Selecciona la categoría (Baches, Luminarias, Drenaje, etc.) e ingresa título y descripción.
   * **Paso 3:** Adjunta evidencia fotográfica (la app la comprime localmente a $\le 300\text{ KB}$).
   * **Paso 4 (Opcional):** Ingrese nombre/correo o selecciona "Permanecer Anónimo".
4. **Procesamiento de Red:**
   * **Si hay Internet:** Los datos viajan a la API REST. El servidor retorna la confirmación y se le muestra en pantalla su **Folio Único** (ej. `HNP-2026-042`).
   * **Si NO hay Internet (Offline):** El Service Worker captura la petición, guarda el paquete completo en **IndexedDB** y muestra el mensaje: *"Reporte guardado localmente. Se enviará automáticamente al recuperar conexión"*.

### 3.2 Flujo B: Consulta e Inspección de Casos por el Ciudadano

1. El usuario navega al menú **Casos** o **Mapa Ciudadano**.
2. Aplica filtros por **Centro Integrador** (ej. *Zona Centro, Tulipán, Cucuyulapa*) o por **Categoría**.
3. Hace clic en un pin del mapa o en una tarjeta del catálogo.
4. Explora el expediente completo: visualiza la fecha de reporte, la ubicación satelital, la galería de fotos, y el avance en la línea de tiempo (cronología).

### 3.3 Flujo C: Moderación y Actualización de Expediente por el Administrador

1. El miembro de HNP inicia sesión en `/admin/login`.
2. Accede a la bandeja de **Moderación** y revisa los reportes pendientes enviados por la ciudadanía.
3. Aprueba un reporte: Se crea oficialmente en el sistema y se le asigna a un Centro Integrador.
4. Durante las labores de seguimiento en campo, el administrador edita el caso:
   * Sube la foto del problema resuelto marcándola como tipo **"DESPUÉS"** (Verde).
   * Cambia el estado del caso a **`Atendido / Cerrado`**.
   * Agrega una nota en la cronología (ej. *"Reparación de luminaria completada por la cuadrilla comunitaria"*).
5. El caso actualizado se refleja inmediatamente en la portada pública en la sección **Resultados y Seguimiento**.

---

## 4. APROBACIÓN Y SEGUIMIENTO DEL ENTREGABLE

Este documento constituye el **Entregable 3 de la Fase 1 (Semanas 1 y 2)**.

* **Elaborado por:** Yesenia Sarai Martínez Collado (Estudiante de Ingeniería en Sistemas Computacionales - UJAT)
* **Revisado por:** Alexis Miguel Lazo Fuentes (Responsable de Proyecto - ALLS BASIC SUPPLY COMPANY SA DE CV)