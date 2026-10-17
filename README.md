# Plant Care Companion

**Plant Care Companion** es una aplicación móvil full-stack diseñada para la identificación botánica, monitoreo y cuidado integral de plantas. El proyecto combina una aplicación móvil construida con **React Native (Expo)** y una arquitectura **BFF (Backend-For-Frontend)** desarrollada en **Node.js / Express con TypeScript**. 

Integra visión por computadora e inteligencia artificial (Plant.id) para reconocer especies vegetales a partir de fotografías tomadas con la cámara, generar calendarios inteligentes de riego, mantener un diario digital de jardinería y ofrecer asistencia interactiva con cifrado de extremo a extremo.

---

## Características

- **Identificación con IA**: escaneo en tiempo real mediante la cámara o galería; detecta especie, nombre científico, probabilidad y ficha botánica a través de la API de Plant.id.
- **Mi Jardín Digital**: registro personalizado de plantas con fotos, notas de evolución, fecha de plantación y estado de salud.
- **Calendario y Recordatorios de Riego**: control y cálculo automático de frecuencias de riego, historial de cuidados y alertas visuales de riegos pendientes o atrasados.
- **Explorador Botánico**: catálogo integrado para descubrir nuevas especies, requerimientos de iluminación, humedad y recomendaciones de sustrato.
- **Chat Asistente con Cifrado E2E**: módulo de mensajería con cifrado de extremo a extremo para consultas y soporte sobre el cuidado vegetal.
- **Backend For Frontend (BFF)**: capa intermedia en Express que centraliza la lógica de negocio, oculta claves privadas de terceros y gestiona almacenamiento en la nube.
- **Seguridad Robusta**: cabeceras de protección con `helmet`, limitación de tasa de peticiones (`rate-limit`), sanitización de datos y autenticación con tokens JWT.
- **Almacenamiento Híbrido**: integración con **Firebase** (Firestore y autenticación) y **Supabase** (base de datos relacional y storage de imágenes).
- **Persistencia Offline**: almacenamiento local con `AsyncStorage` para consultar el jardín e historial de cuidados sin conexión a internet.

---

## Requisitos

- **Node.js**: versión `18.x` o `20.x+`
- **npm** (v9+) o **yarn**
- **Dispositivo móvil o emulador**:
  - Aplicación **Expo Go** instalada en tu dispositivo físico (Android o iOS), o
  - Android Studio Emulator / Xcode Simulator configurado en tu equipo.
- **Variables de entorno**: credenciales para servicios externos (Plant.id, Firebase, Supabase).

---

## Instalación y Uso

### 1. Clonar el repositorio

```bash
git clone https://github.com/Bjmf05/plant-care-companion.git
cd plant-care-companion
```

---

### 2. Configurar y levantar el Backend (`backend_plant`)

El backend actúa como servidor API / BFF en el puerto `3000` (o el configurado en `.env`):

```bash
cd backend_plant
npm install
```

Crea tu archivo `.env` a partir de la plantilla:

```bash
cp .env.example .env
```

Inicia el servidor en modo desarrollo:

```bash
npm run dev
```

El servidor estará escuchando en `http://localhost:3000` (con endpoint de salud en `/health`).

---

### 3. Configurar y levantar la App Móvil (`project_plant`)

En una nueva terminal, navega al directorio del cliente móvil:

```bash
cd project_plant
npm install
```

Configura las variables de entorno en tu `.env` (asegúrate de apuntar a la IP local de tu máquina para pruebas con Expo Go en dispositivos físicos):

```bash
cp .env.example .env
```

Inicia la aplicación con Expo:

```bash
npx expo start
```

* **Android**: presiona `a` en la terminal o escanea el código QR con la app **Expo Go**.
* **iOS**: presiona `i` para abrir el simulador o escanea el QR con la app de Cámara (iOS).
* **Web**: presiona `w` para probar una vista preliminar en el navegador.

---

## Módulos y API en un vistazo

### Pantallas de la Aplicación Móvil

| Pantalla | Ruta | Descripción |
| -------- | ---- | ----------- |
| 🏠 **Inicio** | `app/(tabs)/index.tsx` | Panel principal con resumen del jardín, alertas de riego y acciones rápidas. |
| 🔍 **Explorar** | `app/(tabs)/explore.tsx` | Buscador y biblioteca de especies botánicas con detalles de cultivo. |
| 📷 **Cámara / IA** | `app/(tabs)/camera.tsx` | Captura fotográfica o carga de imágenes para identificación con IA. |
| 🌱 **Mi Jardín** | `app/(tabs)/garden.tsx` | Colección personal de plantas, seguimiento individual y notas. |
| 👤 **Perfil** | `app/(tabs)/profile.tsx` | Gestión de cuenta de usuario, preferencias y personalización. |
| 📅 **Calendario** | `app/calendar.tsx` | Vista en calendario de eventos de riego y tareas programadas. |
| 💬 **Chat Seguro** | `components/chat/*` | Ventana flotante de chat con cifrado criptográfico local. |

---

### Endpoints Principales del Backend (BFF)

| Método | Endpoint | Descripción | Protección |
| ------ | -------- | ----------- | ---------- |
| `POST` | `/api/identify` | Envía una imagen a Plant.id y devuelve la especie identificada. | Rate Limit |
| `POST` | `/api/translate` | Traduce información botánica al idioma del usuario. | Rate Limit |
| `POST` | `/api/auth/register` | Registro de nuevos usuarios y emisión de JWT. | Auth Rate Limit |
| `POST` | `/api/auth/login` | Inicio de sesión y verificación de credenciales. | Auth Rate Limit |
| `GET` / `POST` | `/api/plants` | Consulta y registro de plantas en el jardín del usuario. | JWT / Rate Limit |
| `GET` / `POST` | `/api/calendar` | Obtención y actualización del cronograma de riego. | JWT |
| `POST` | `/api/images/upload` | Subida de fotografías a Supabase Storage con compresión. | Upload Limit |
| `GET` | `/health` | Verificación de estado y disponibilidad del servidor. | Público |

---

## Estructura del proyecto

```text
plant-care-companion/
├── backend_plant/             # Servidor API / Backend-For-Frontend (Express + TypeScript)
│   ├── src/
│   │   ├── config/            # Carga y validación tipada de variables de entorno
│   │   ├── controllers/       # Lógica de controladores de la API
│   │   ├── middleware/        # Rate limiting, helmet, manejo de errores y multer
│   │   ├── routes/            # Definición de rutas (identify, plants, auth, calendar...)
│   │   ├── services/          # Conexiones con Firebase, Supabase y APIs externas
│   │   ├── utils/             # Logger estructurado (Pino) y utilidades
│   │   └── index.ts           # Punto de entrada y configuración del servidor Express
│   ├── package.json
│   └── tsconfig.json
│
├── project_plant/             # Aplicación Móvil (React Native + Expo Router)
│   ├── app/                   # Enrutamiento basado en archivos (Tabs, Auth, Modals)
│   ├── assets/                # Imágenes, íconos y tipografías
│   ├── components/            # Componentes reutilizables (Botones, Chat, Tarjetas)
│   ├── context/               # Proveedores de estado global (AuthContext, ChatContext)
│   ├── hooks/                 # Hooks personalizados de React Native
│   ├── lib/                   # Clientes de API, Firebase client y utilitarios
│   ├── package.json
│   └── app.json               # Configuración de manifiesto Expo / App móvil
│
└── README.md                  # Documentación principal del repositorio
```

---

## Licencia

Distribuido bajo la licencia **MIT**. Ver [LICENSE](LICENSE) para más información.

Copyright (c) 2025 Breiner Muñoz Fallas
