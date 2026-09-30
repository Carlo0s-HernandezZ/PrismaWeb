## 🏗️ Arquitectura del Sistema

El sistema sigue una arquitectura moderna, distribuida y basada en microservicios, optimizada para el procesamiento asíncrono y la integración con Inteligencia Artificial.


sequenceDiagram
    autonumber
    actor Usuario
    participant App as App Cliente<br/>(KMP / Swift)
    participant Backend as Backend<br/>(Kotlin REST API)
    participant BD as Base de Datos

   Usuario->>App: Ingresa email y contraseña
    App->>Backend: POST /auth/login con email y password
    Backend->>BD: Consulta usuario por email
    BD-->>Backend: Datos del usuario y hash
    
 *   alt credenciales incorrectas
        Backend-->>App: HTTP 401 no autorizado
        App->>Usuario: Muestra "Usuario o contraseña incorrectos"
    else credenciales correctas
        Backend-->>App: HTTP 200 estado PENDING_2FA con temp_token
        App-->>Usuario: Solicita código 2FA
    end

  *  Usuario->>App: Introduce código TOTP de 6 dígitos
    App->>Backend: POST /auth/verify-2fa con código y temp_token
    Backend->>BD: Consulta semilla 2FA del usuario
    BD-->>Backend: Semilla criptográfica

   * alt código inválido o expirado
        Backend-->>App: HTTP 400 código incorrecto
        App->>Usuario: Muestra "Código de autenticación inválido"
    else código válido
        Backend-->>App: HTTP 200 access_token y refresh_token
        Note over App: Guarda tokens en Keychain o KeyStore
        App->>Usuario: Redirige al dashboard de tarjetas
    end


### 🗺️ Diagrama de Componentes y Flujo

erDiagram
    USERS ||--o{ USER_INCOMES : deposits
    USERS ||--o{ CREDIT_CARDS : owns
    USERS ||--o{ FINANCIAL_ADVICES : receives
    CREDIT_CARDS ||--o{ TRANSACTIONS : generates

   * USERS {
        bigint id PK
        string email UK
        string password_hash
        string secret_2fa
        string region
        timestamp created_at
    }

  *  USER_INCOMES {
        bigint id PK
        bigint user_id FK
        decimal amount_net
        boolean is_audited_payroll
        timestamp recorded_at
    }

   * CREDIT_CARDS {
        bigint id PK
        bigint user_id FK
        string bank_name
        string card_alias
        string last_four_digits
    }

   * FINANCIAL_ADVICES {
        bigint id PK
        bigint user_id FK
        text advice_text
        string advice_hash
        timestamp updated_at
    }

   * TRANSACTIONS {
        bigint id PK
        bigint card_id FK
        date transaction_date
        string description
        decimal amount_mxn
        string original_currency
    }


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
