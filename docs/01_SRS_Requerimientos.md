# DOCUMENTO DE ESPECIFICACIÓN DE REQUERIMIENTOS DE SOFTWARE (SRS)

**Plataforma Web:** Radar Ciudadano HNP – Archivo Digital y Observatorio Comunitario

**Proyecto:** Hechos No Palabras (HNP) – Cunduacán, Tabasco

**Unidad Receptora:** ALLS BASIC SUPPLY COMPANY SA DE CV

**Estudiante / Desarrollador:** Yessenia Sarai Martínez Collado

**Responsable del Proyecto:** Alexis Miguel Lazo Fuentes

**Fase de Desarrollo:** Fase 1 – Análisis y Planeación del Proyecto (Semanas 1 y 2\)

**Versión del Documento:** 1.0

**Fecha:** Octubre / Noviembre 2026

## 1\. INTRODUCCIÓN Y PROPÓSITO

### 1.1 Propósito del Documento

El presente documento especifica de manera formal y detallada los requerimientos funcionales, no funcionales, de arquitectura técnica y de infraestructura para la plataforma **Radar Ciudadano HNP / Archivo Ciudadano HNP**. Este pliego sirve como marco normativo y guía técnica para el desarrollo, pruebas, entrega e implementación del sistema durante el periodo de Prácticas Profesionales.

### 1.2 Justificación del Proyecto

El Observatorio Comunitario *Hechos No Palabras* ha documentado activamente diversas problemáticas comunitarias en Cunduacán, Tabasco (infraestructura urbana, vialidades, alumbrado público, drenaje y medio ambiente). Históricamente, gran parte de esta información se ha recibido y difundido a través de redes sociales (Facebook) y mensajería instantánea (WhatsApp), lo que ocasiona la dispersión de evidencias, dificulta la consulta histórica y limita la contraloría ciudadana sistemática.

La plataforma no busca sustituir los canales de comunicación existentes, sino constituir un **archivo digital institucional y mapa interactivo** que organice, preserve, georreferencie y exponga públicamente los expedientes de casos comunitarios, garantizando el derecho a la información y el fortalecimiento de la memoria colectiva.

### 1.3 Objetivo General

Diseñar y desarrollar una plataforma web accesible, ligera y optimizada para dispositivos móviles (SPA/PWA) con arquitectura desacoplada (*Frontend/Backend*), que permita documentar, georreferenciar, clasificar y consultar públicamente los casos ciudadanos registrados en el municipio de Cunduacán, Tabasco, respaldando evidencias de "Antes y Después", cronologías y un repositorio documental comunitario.

## 2\. DESCRIPCIÓN GENERAL Y ALCANCE

### 2.1 Componentes Principales del Sistema

1. **Sitio Web Público (PWA):** Portal accesible a la ciudadanía con soporte de trabajo *Offline-First* para captura en campo sin conexión a internet.

2. **Mapa Ciudadano Interactivo:** Módulo de georreferenciación dinámica mediante marcadores por categoría y filtros geográficos por Centros Integradores.

3. **Panel Administrativo (Backoffice):** Interfaz autenticada para la moderación de reportes, edición de expedientes, actualización de estados, carga de evidencias fotográficas comparativas y gestión de catálogos.

4. **Biblioteca Ciudadana:** Repositorio digital público para la consulta y descarga de acuerdos, informes, infografías y marco normativo.

### 2.2 Delimitación del Alcance (Exclusiones)

De acuerdo con las especificaciones del proyecto, **se excluyen expresamente** las siguientes funcionalidades:

* Desarrollo de aplicaciones móviles nativas para sistemas iOS o Android.

* Integraciones automáticas bidireccionales vía API con Facebook Graph API o WhatsApp Business API.

* Pasarelas de pagos o transacciones monetarias.

* Sistemas de chat o mensajería instantánea propia en tiempo real dentro del sitio.

* Algoritmos de inteligencia artificial o automatizaciones por redes neuronales.

* Registro masivo obligatorio de cuentas de usuario para el público en general.

## 3\. PERFILES DE USUARIO (ACTORES DEL SISTEMA)

| Rol / Actor | Descripción y Permisos |
| :---- | :---- |
| **Ciudadano / Visitante Público** | Usuario general (anónimo o con registro opcional de contacto). Puede navegar por el sitio público, buscar y filtrar casos, visualizar el mapa interactivo, descargar documentos de la biblioteca, y enviar reportes ciudadanos (incluso en zonas sin cobertura de datos móviles). |
| **Administrador / Moderador HNP** | Miembro autorizado del Observatorio Comunitario. Cuenta con credenciales de acceso al Panel Administrativo. Puede revisar y aprobar reportes públicos, crear casos manualmente, cambiar estados de seguimiento, subir fotos comparativas ("Antes y Después"), gestionar la biblioteca y administrar los catálogos del municipio. |

## 4\. REQUERIMIENTOS FUNCIONALES (RF)

### 4.1 Módulos de Interfaz Pública (Frontend)

#### **RF-01: Encabezado Institucional y Navegación**

* **RF-01.1:** Mostrar el logotipo oficial del Observatorio Comunitario *Hechos No Palabras* en el extremo superior izquierdo.

* **RF-01.2:** Desplegar la leyenda de ubicación geográfica `Cunduacán, Tab.` en la esquina superior derecha.

* **RF-01.3:** Incluir barra de navegación con enlaces a: *Inicio, Casos Ciudadanos, Mapa Ciudadano, Resultados y Seguimiento, Biblioteca Ciudadana, Participa y Contacto*.

* **RF-01.4:** En pantallas móviles, el menú debe colapsar automáticamente en una interfaz tipo hamburguesa accesible.

#### **RF-02: Zona Héroe y Búsqueda General**

* **RF-02.1:** Integrar barra de búsqueda general con marcador de posición (*placeholder*) `"Buscar Caso Ciudadano"` y botón interactivo `"Buscar"`.

* **RF-02.2:** Mostrar el distintivo oficial `#RadarCiudadanoHNP` e icono con enlace a la página oficial de Facebook.

* **RF-02.3:** Incluir mensaje institucional y el botón principal de acción `"Reporta un Caso"` (destacado en rojo con animación *hover*), el cual abre el modal de registro.

#### **RF-03: Tarjeta Dinámica "Resultados y Seguimiento" (Antes y Después)**

* **RF-03.1:** Renderizar dinámicamente en la portada principal la información y fotos del último caso cerrado o atendido.

* **RF-03.2:** Aplicar jerarquía cromática institucional UI:

  * **ANTES:** Marco/etiqueta en color **ROJO** (alerta/incidencia reportada).

  * **DESPUÉS:** Marco/etiqueta en color **VERDE** (incidencia atendida/resuelta).

* **RF-03.3:** Al hacer clic en la tarjeta, redirigir al expediente detallado del caso.

#### **RF-04: Formulario Dinámico de Registro ("Reporta un Caso")**

* **RF-04.1:** Despliegue mediante ventana modal emergente o vista dedicada al accionar el botón de reporte.

* **RF-04.2:** Integrar mapa interactivo con botón `"ENCUENTRA MI UBICACIÓN"` para capturar coordenadas satelitales mediante `navigator.geolocation`.

* **RF-04.3:** Permitir arrastrar el pin en el mapa y desplegar confirmación visual: `"El punto señalado se encuentra en el municipio de Cunduacán, Tab"`.

* **RF-04.4:** Capturar los campos obligatorios: **Título del Reporte**, **Descripción de la Problemática** y **Categoría** (menú desplegable: Baches, Luminarias, Drenaje, Reparaciones, Áreas Verdes, etc.).

* **RF-04.5:** Capturar campos opcionales: Nombre y Correo Electrónico (respetando el derecho al anonimato del ciudadano).

* **RF-04.6:** Incluir botón `"Subir evidencias"` que active la cámara o galería del dispositivo, aplicando compresión local previa al envío.

* **RF-04.7:** Al presionar `"ENVIAR REPORTE"`, procesar los datos y retornar un **Folio Único de Seguimiento** (ejemplo: `HNP-2026-042`).

#### **RF-05: Mapa Ciudadano Interactivo**

* **RF-05.1:** Renderizar casos georreferenciados sobre mapa interactivo (tecnología Leaflet.js / OpenStreetMap).

* **RF-05.2:** Implementar código de color e iconografía en los marcadores (*pins*) por categoría:

  * **Amarillo:** Luminarias

  * **Naranja / Marrón:** Baches

  * **Azul:** Drenajes

  * **Verde:** Áreas Verdes / Reparaciones

* **RF-05.3:** Proporcionar filtros de búsqueda por Centros Integradores (Zona Centro, Colonias, Ejidos) y por Tipo de Caso.

* **RF-05.4:** Desplegar *popup* al hacer clic en un marcador con: Folio, fotografía principal, estado del caso y enlace `"Ver detalle"`.

#### **RF-06: Consulta de Casos e Indicadores Estadísticos**

* **RF-06.1:** Catálogo de casos presentados en tarjetas con buscador y paginación.

* **RF-06.2:** Vista de expediente individual con: Folio, fecha, categoría, Centro Integrador, localidad, coordenadas, línea de tiempo (cronología), galería de evidencias y dictamen final.

* **RF-06.3:** Visualización de contadores estadísticos en tiempo real: Total de casos registrados, casos en seguimiento, casos atendidos y casos cerrados.

#### **RF-07: Biblioteca Ciudadana**

* **RF-07.1:** Maquetación en rejilla (*Grid responsive*) para consulta de acuerdos comunitarios, informes y guías.

* **RF-07.2:** Tarjetas con miniatura de documento (PDF/Imagen), título, síntesis, fecha de publicación y botón `"Descargar / Ver Documento"`.

* **RF-07.3:** Buscador interno y filtrado por categoría de documento.

#### **RF-08: Canal de Denuncias Sensibles y Footer Institucional**

* **RF-08.1:** Enlace directo a mensajería cifrada externamente (`https://signal.me/...`) para reportes con alta necesidad de anonimato y protección de identidad.

* **RF-08.2:** Footer en 3 columnas: Legitimidad (Aviso de Privacidad, Términos), Identidad Institucional (Derechos reservados 2026\) y Navegación Rápida.

### 4.2 Módulos del Panel Administrativo (Backoffice)

#### **RF-09: Autenticación y Seguridad**

* **RF-09.1:** Inicio de sesión mediante credenciales cifradas con emisión de tokens de sesión JWT (*JSON Web Token*).

#### **RF-10: Gestión y Moderación de Casos**

* **RF-10.1:** Panel de revisión de reportes ciudadanos recibidos desde la web o creados manualmente por el equipo administrativo.

* **RF-10.2:** Edición de información del caso, asignación de Centros Integradores y modificación del estado del reporte:

  * `Sin atención / Pendiente`

  * `En proceso`

  * `Atendido / Cerrado`

* **RF-10.3:** Carga y clasificación de fotografías asociadas al expediente en las categorías "ANTES" y "DESPUÉS".

* **RF-10.4:** Adición de notas de seguimiento a la línea de tiempo (cronología del caso).

#### **RF-11: Administración de Catálogos y Biblioteca**

* **RF-11.1:** Mantenimiento (CRUD) del catálogo de Categorías, Localidades y Centros Integradores del municipio.

* **RF-11.2:** Subida, edición y eliminación de documentos en la Biblioteca Ciudadana.

## 5\. REQUERIMIENTOS NO FUNCIONALES (RNF) Y ARQUITECTURA TÉCNICA

### 5.1 Especificaciones de Desempeño y Usabilidad

* **RNF-01 (Diseño Mobile-First):** La interfaz debe adaptarse de forma fluida a pantallas con anchos desde 320px hasta monitores 4K utilizando Tailwind CSS.

* **RNF-02 (Compresión de Imágenes):** Las fotografías seleccionadas por el usuario se comprimirán automáticamente en el cliente a formato WebP/JPEG antes de su almacenamiento o transmisión, limitando el tamaño máximo a $\le 300\text{ KB}$ por archivo.

### 5.2 Lógica Offline-First (PWA)

* **RNF-03 (Soporte Sin Conexión):** Implementación de Service Workers (vía Workbox / `vite-plugin-pwa`). Ante la ausencia de red de datos:

  1. El sistema detecta el estado offline.

  2. Almacena localmente en **IndexedDB** (usando `Dexie.js`) el contenido del formulario, la foto comprimida en Blob/Base64 y las coordenadas GPS satelitales (`navigator.geolocation`).

  3. Al detectar reconexión a internet, un proceso en segundo plano (*Background Sync*) transmite los datos acumulados a la API y genera el folio correspondiente.

### 5.3 Arquitectura y Almacenamiento

* **RNF-04 (Desacoplamiento):** Separación completa entre el cliente SPA/PWA (React \+ Vite) y el servidor de aplicación API REST (Node.js \+ Express).

* **RNF-05 (Almacenamiento Multimedia Decoplado):** Las imágenes no se guardarán en la base de datos relacional; se enviarán a un almacenamiento en la nube (*Object Storage* compatible con S3 o Cloudflare R2) sirviéndose mediante CDN.

* **RNF-06 (Base de Datos Relacional):** Base de datos PostgreSQL estructurada obligatoriamente con las entidades: `Casos`, `Categorias`, `Centros_Integradores`, `Localidades`, `Evidencias` y `Biblioteca_Documentos`.

## 6\. ESTIMACIÓN DE INFRAESTRUCTURA TÉCNICA Y COSTOS DE OPERACIÓN

Para garantizar la viabilidad económica y la sostenibilidad a largo plazo del Observatorio Comunitario en Cunduacán, Tabasco, se seleccionó el **Stack 1 (React \+ Vite PWA, Tailwind CSS, Leaflet.js, Node.js/Express, PostgreSQL, Cloudflare R2)**.

A continuación se detalla la estimación de costos tanto para la fase de desarrollo/MVP como para la fase de operación en producción.

### 6.1 Matriz de Costos por Componente

| Componente Técnico | Plataforma / Tecnología | Fase 1 y 2: Desarrollo y Pruebas (MVP) | Fase 4: Operación en Producción | Observación Técnica |
| :---- | :---- | :---- | :---- | :---- |
| **Frontend & PWA Host** | Vercel / Cloudflare Pages | **\$0.00 USD** / mes | **\$0.00 USD** / mes | Incluye certificado SSL (HTTPS obligatorio para PWA y GPS) y CDN global. |
| **Backend (API REST)** | Render / Railway / Koyeb | **\$0.00 USD** / mes *(Free Tier)* | **\$5.00 \- \$7.00 USD** / mes | El plan pagado de backend evita que el servidor "se duerma" tras inactividad. |
| **Base de Datos SQL** | Neon.tech / Supabase (PostgreSQL) | **\$0.00 USD** / mes *(500 MB gratis)* | **\$0.00 USD** / mes *(Free Tier prolongado)* | 500 MB permiten almacenar más de 50,000 registros de texto y coordenadas. |
| **Almacenamiento de fotos** | Cloudflare R2 / Cloudinary | **\$0.00 USD** / mes *(10 GB gratis)* | **\$0.00 USD** / mes *(Casi \$0.00 MXN)* | 10 GB equivalen a aproximadamente 65,000 fotos comprimidas en WebP ($150\text{ KB}$ prom.). |
| **Mapas Interactivos** | Leaflet.js \+ OpenStreetMap | **\$0.00 USD** / mes | **\$0.00 USD** / mes | 100% código abierto; elimina cobros por consumo de API de mapas. |
| **Dominio Personalizado** | Dominio `.mx` / `.org.mx` | **\$0.00 MXN** *(subdominio .vercel.app)* | **\$450 \- \$700 MXN** / año | Opcional, sujeto a la adquisición previa del observatorio. |

### 6.2 Resumen de Escenarios Financieros

1. **Escenario A: Periodo de Desarrollo y Pruebas de Prácticas (Semanas 1 a 12\)**

   * **Costo mensual estimado:** **\$0.00 MXN / mes** (Aprovechamiento integral de capas gratuitas).  
2. **Escenario B: Producción Autónoma Estable (Recomendado)**

   * **Costo mensual estimado:** **\~\$100.00 \- \$140.00 MXN / mes** (\$5 \- \$7 USD/mes para servicio de API Backend continuo 24/7).

   * **Costo anual de dominio:** **\~\$500.00 MXN / año**.

## 7\. ESQUEMA DE BASE DE DATOS E ENTIDADES OBLIGATORIAS

El modelo de datos relacional en PostgreSQL incorporará las siguientes entidades mínimas exigidas por el pliego técnico:

* `casos`: (`id`, `folio_id`, `titulo`, `descripcion`, `latitude`, `longitude`, `categoria_id`, `centro_integrador_id`, `localidad_id`, `status_id`, `nombre_ciudadano`, `correo_ciudadano`, `created_at`, `updated_at`)

* `categorias`: (`id`, `nombre`, `color_pin`, `icono`, `descripcion`)

* `centros_integradores`: (`id`, `nombre`, `zona_geografica`)

* `localidades`: (`id`, `nombre`, `centro_integrador_id`)

* `evidencias`: (`id`, `caso_id`, `url_foto`, `tipo_evidencia`, `comentario`, `created_at`)  *(Donde `tipo_evidencia` define si es "ANTES" o "DESPUÉS")*

* `cronologia_casos`: (`id`, `caso_id`, `titulo_accion`, `descripcion`, `fecha_registro`, `usuario_admin_id`)

* `biblioteca_documentos`: (`id`, `titulo`, `descripcion`, `categoria`, `url_archivo`, `formato`, `fecha_publicacion`)

## 8\. APROBACIÓN Y SEGUIMIENTO DEL ENTREGABLE

Este documento forma parte del **Entregable 1 de la Fase 1 (Semanas 1 y 2\)** del proyecto de prácticas profesionales.

* **Revisado por:** Alexis Miguel Lazo Fuentes (Responsable de Proyecto \- ALLS BASIC SUPPLY COMPANY SA DE CV)

* **Elaborado por:** Yesenia Sarai Martínez Collado (Estudiante de Ingeniería en Sistemas Computacionales \- UJAT)