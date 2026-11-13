# ARQUITECTURA TECNOLÓGICA PROPUESTA Y JUSTIFICACIÓN DEL STACK TÉCNICO

**Plataforma Web:** Radar Ciudadano HNP – Archivo Digital y Observatorio Comunitario

**Proyecto:** Hechos No Palabras (HNP) – Cunduacán, Tabasco

**Unidad Receptora:** ALLS BASIC SUPPLY COMPANY SA DE CV

**Estudiante / Desarrollador:** Yesenia Sarai Martínez Collado

**Responsable del Proyecto:** Alexis Miguel Lazo Fuentes

**Fase de Desarrollo:** Fase 1 – Análisis y Planeación del Proyecto (Semanas 1 y 2\)

**Versión del Documento:** 1.0

## 1\. RESUMEN EJECUTIVO Y VISIÓN GENERAL DE LA ARQUITECTURA

El sistema **Radar Ciudadano HNP** está diseñado bajo una **Arquitectura Desacoplada (Decoupled System Architecture)**, garantizando una separación total entre la capa de presentación del cliente (Frontend PWA) y la capa de servicios de datos y lógica de negocio (Backend API REST).

Esta decisión de diseño responde a las necesidades operativas del municipio de Cunduacán, Tabasco, donde la cobertura de red móvil 4G/5G puede ser inestable o nula en diversas comunidades y Centros Integradores. La arquitectura seleccionada habilita capacidades **Offline-First**, optimización extrema en consumo de datos móviles y flexibilidad para futuras expansiones institucionales.

```
[ CLIENTES PWA ] <---> [ SERVICE WORKERS / INDEXEDDB ]
       |
       |  (API REST / HTTPS - JSON)
       v
[ BACKEND API (Node.js/Express) ]
       |
       +---> [ BASE DE DATOS (PostgreSQL) ]
       |
       +---> [ OBJECT STORAGE (Cloudflare R2 / S3 API) ]

```

## 2\. JUSTIFICACIÓN DEL STACK TECNOLÓGICO SELECCIONADO (STACK 1\)

Tras evaluar la factibilidad técnica, el costo de infraestructura y la mantenibilidad a largo plazo, se ha ratificado la adopción del **Stack 1**. A continuación se detalla la justificación técnica de cada componente:

### 2.1 Capa de Frontend y Aplicación Web Progresiva (PWA)

* **React \+ Vite:**

  * **Justificación:** Vite ofrece tiempos de compilación y empaquetado ultra rápidos mediante ES Modules nativos. React proporciona una arquitectura basada en componentes reutilizables (tarjetas de casos, modales de captura, barras de filtrado) ideales para la construcción de una SPA (*Single Page Application*) fluida y reactiva.  
* **Tailwind CSS:**

  * **Justificación:** Framework CSS de utilidades que permite implementar una interfaz *Responsive / Mobile-First* adaptada a cualquier resolución (320px hasta 4K). Garantiza el cumplimiento de la jerarquía cromática de la marca institucional ("Antes" en rojo, "Después" en verde).  
* **Leaflet.js \+ OpenStreetMap:**

  * **Justificación:** Biblioteca de mapas interactivos ligera ($\sim 39\text{ KB}$ gzipped), de código abierto y completamente gratuita. Evita costos por uso de APIs de pago (como Google Maps API) y permite la personalización de marcadores (*pins*) por categorías urbanas.  
* **Dexie.js (IndexedDB Wrapper) \+ Service Workers (`vite-plugin-pwa`):**

  * **Justificación:** Permite implementar el patrón *Offline-First*. Cuando un ciudadano levanta un reporte en una zona rural sin señal, el registro y la fotografía comprimida se almacenan temporalmente en la base de datos del navegador (**IndexedDB**) y se sincronizan automáticamente con la API REST mediante *Background Sync* al recuperar conexión a internet.

### 2.2 Capa de Backend y Lógica de Negocio

* **Node.js \+ Express.js:**

  * **Justificación:** Entorno de ejecución asíncrono y no bloqueante basado en eventos, altamente eficiente para el manejo de peticiones I/O concurrentes (recepción de reportes, consultas de catálogo, carga de imágenes). Express ofrece un framework ligero para la construcción de la API RESTful estructurada.  
* **JWT (JSON Web Tokens) \+ Bcrypt:**

  * **Justificación:** Garantiza la seguridad en las sesiones del Panel Administrativo (Backoffice), permitiendo la autenticación sin estado (*stateless*) mediante tokens con expiración y cifrado de contraseñas con algoritmos estándar de la industria.

### 2.3 Capa de Almacenamiento y Base de Datos

* **Base de Datos Relacional: PostgreSQL (Neon.tech / Supabase):**

  * **Justificación:** Motor de base de datos relacional robusto, con soporte nativo para integridad referencial, transacciones ACID y consultas geoespaciales. Su esquema estructurado asegura la consistencia de las entidades clave: `Casos`, `Categorias`, `Centros_Integradores`, `Localidades`, `Evidencias` y `Biblioteca_Documentos`.  
* **Almacenamiento Multimedia: Cloudflare R2 / Object Storage (S3 API):**

  * **Justificación:** Para mantener la base de datos PostgreSQL ligera y de respuesta rápida, los archivos pesados (fotografías de evidencias "Antes y Después" y documentos PDF de la biblioteca) se almacenan desacoplados en Object Storage y se sirven globalmente vía CDN.

## 3\. ESQUEMA DE ARQUITECTURA DE CAPAS Y FLUJO DE DATOS

```
+-------------------------------------------------------------------+
|                     CAPA DE PRESENTACIÓN (CLIENTE)                 |
|  - PWA React (Mobile-First / Tailwind CSS)                        |
|  - Mapa Interactivo (Leaflet.js)                                  |
|  - Compresión de Imagen Client-Side (WebP / JPEG <= 300 KB)        |
+-------------------------------------------------------------------+
                                 |
           (Detección Estado de Red: Conexión vs Offline)
                                 |
        +------------------------+------------------------+
        |                                                 |
  [ Conexión Online ]                              [ Sin Conexión ]
        |                                                 |
        v                                                 v
Petición HTTPS (JSON)                      Almacenamiento Local
        |                                  IndexedDB (Dexie.js)
        |                                                 |
        |                                  (Sincronización diferida
        |                                   al detectar señal)
        v                                                 |
+---------------------------------------------------------+---------+
|                        CAPA DE SERVICIOS (BACKEND)                |
|  - API RESTful Node.js / Express                                  |
|  - Autenticación JWT / Middlewares de Seguridad                   |
|  - Controladores de Negocio (Casos, Catálogos, Biblioteca)        |
+-------------------------------------------------------------------+
                                 |
        +------------------------+------------------------+
        |                                                 |
        v                                                 v
[ PostgreSQL ]                                    [ Object Storage ]
- Datos Relacionales                              - Fotografías (Antes/Después)
- Coordenadas GPS                                 - Documentos PDF Biblioteca
- Historial de Estados                            - Servido vía CDN Global

```

## 4\. ESTRATEGIA DE TRABAJO OFFLINE-FIRST Y ALMACENAMIENTO LOCAL

La resiliencia operativa en campo es un pilar fundamental del proyecto. La estrategia técnica contempla:

1. **Captura Geográfica Satelital:** El módulo de reporte utiliza la API nativa `navigator.geolocation` del navegador para obtener latitud y longitud directamente del chip GPS del dispositivo móvil, sin requerir conexión a redes celulares.

2. **Compresión Local de Fotografías:** Antes de guardar la evidencia en el almacenamiento temporal o enviarla al servidor, un proceso en el cliente reduce la resolución y calidad de la imagen en formato WebP o JPEG, garantizando que el peso del archivo sea siempre $\le 300\text{ KB}$.

3. **Cola de Sincronización (Sync Queue):** Si la petición falla por falta de señal:

   * Los datos del reporte se guardan en el *store* `reportes_pendientes` de IndexedDB.

   * El Service Worker registra una tarea de sincronización en segundo plano.

   * Al restaurarse la conectividad, se reintenta el envío de forma transparente para el usuario y se notifica la asignación de su **Folio Único**.

## 5\. REQUISITOS DE SEGURIDAD Y PROTECCIÓN DE DATOS

* **Protocolo HTTPS Obligatorio:** Todos los dominios y servicios operarán bajo HTTPS mediante certificados SSL/TLS gratuitos (Let's Encrypt / Cloudflare Edge SSL). Esto es indispensable para habilitar el API de Geolocalización y la instalación de la PWA.

* **Anonimización Opcional:** El sistema permite el envío de reportes sin requerir registro obligatorio de usuario, garantizando el anonimato de los ciudadanos cuando el caso lo requiera.

* **Protección contra Inyecciones y Spam:** Sanitización estricta de parámetros de entrada en la API REST, limitación de frecuencia de peticiones (*Rate Limiting*) para prevenir ataques por fuerza bruta o spam en el formulario de reportes.

## 6\. EVALUACIÓN DE COSTOS DE INFRAESTRUCTURA Y OPERACIÓN

| Componente | Entorno de Desarrollo (Semanas 1 \- 12\) | Entorno de Producción | Estrategia de Costo |
| :---- | :---- | :---- | :---- |
| **Frontend Host (PWA)** | Vercel / Cloudflare Pages | \$0.00 USD / mes | Tier Gratuito Permanente |
| **Backend API (Node.js)** | Render / Koyeb (Free Tier) | \$5.00 \- \$7.00 USD / mes | Servidor Paga Continua (Evita Sleep) |
| **Base de Datos PostgreSQL** | Neon.tech / Supabase | \$0.00 USD / mes | Tier Gratuito (Hasta 500 MB) |
| **Object Storage (Fotos)** | Cloudflare R2 | \$0.00 USD / mes | 10 GB Gratis ($\approx 65,000$ fotos) |
| **Mapas (Leaflet)** | OpenStreetMap | \$0.00 USD / mes | Open Source (Sin costo de API) |
| **Dominio Personalizado** | Subdominio `.vercel.app` | \$450.00 \- \$700.00 MXN / año | Dominio `.org.mx` o `.mx` (Opcional) |

### Resumen Financiero:

* **Inversión Durante Desarrollo (Prácticas Profesional):** **\$0.00 MXN**

* **Operación Mensual en Producción:** **\~\$100.00 \- \$140.00 MXN / mes**

## 7\. CONCLUSIÓN Y CONEXIÓN CON LOS SIGUIENTES ENTREGABLES

La Arquitectura Tecnológica planteada cumple rigurosamente con los lineamientos del pliego técnico y las necesidades operativas del Observatorio Comunitario *Hechos No Palabras*.

Este documento constituye el **Entregable 2 de la Fase 1**. Los siguientes pasos inmediatos para completar la Fase 1 son la elaboración del **Mapa del Sitio (Sitemap)** y el **Diagrama General del Sistema**.

* **Elaborado por:** Yesenia Sarai Martínez Collado (Estudiante de Ingeniería en Sistemas Computacionales \- UJAT)

* **Revisado por:** Alexis Miguel Lazo Fuentes (Responsable de Proyecto \- ALLS BASIC SUPPLY COMPANY SA DE CV)