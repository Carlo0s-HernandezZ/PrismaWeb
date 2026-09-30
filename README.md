## 🏗️ Arquitectura del Sistema

El sistema sigue una arquitectura moderna, distribuida y basada en microservicios, optimizada para el procesamiento asíncrono y la integración con Inteligencia Artificial.

### 🗺️ Diagrama de Componentes y Flujo

### 📦 Capas y Componentes Clave

#### 1. Capa de Cliente (Frontend)
* **App Web & Android (KMP):** Clientes desarrollados con **Kotlin Multiplatform**, compartiendo la lógica de negocio central entre plataformas.
* **Seguridad y Comunicación:** Conexión mediante **REST/gRPC** protegida bajo el estándar **OAuth2**.

#### 2. Gateway y Enrutamiento
* **API Gateway (Ktor + Coroutines):** Punto único de entrada encargado de la verificación de tokens, enrutamiento de peticiones y gestión de solicitudes asíncronas de manera no bloqueante.

#### 3. Servicios del Backend (Microservicios)
* **Servicio de Autenticación (Auth):** Gestiona la seguridad mediante tokens **JWT** y autenticación de dos factores (**2FA**).
* **Servicio de IA:** Encargado de la integración con **Google Gemini (API REST)** para el procesamiento de prompts. Trabaja de forma asíncrona mediante eventos.
* **Servicio de Finanzas:** Motor de libro mayor que ejecuta transacciones críticas con propiedades **ACID**.
* **Servicio de Notificaciones:** Sistema encargado de enviar alertas push en tiempo real a los dispositivos de los usuarios.

#### 4. Mensajería y Persistencia
* **Cola de Mensajes (RabbitMQ / Redis):** Intermediario (*Message Broker*) que desacopla los servicios permitiendo una comunicación síncrona/asíncrona mediante publicación y suscripción de eventos.
* **Base de Datos (PostgreSQL):** Base de datos relacional central con almacenamiento **cifrado**, soporte de caché y persistencia segura de datos financieros y de usuario.
* **Servicios Push Externos:** Integración con **FCM** (Firebase Cloud Messaging) y **APNs** (Apple Push Notification service) para la entrega final de alertas.a
