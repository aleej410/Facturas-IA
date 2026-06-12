# 🏗️ Arquitectura - FacturaAI SaaS v2.0

## 📐 Diagrama General

```
┌─────────────────────────────────────────────────────────────────┐
│                       FACTURAAI SAAS                             │
└─────────────────────────────────────────────────────────────────┘

                            🌐 INTERNET
                                 ↓
                    ┌────────────────────────┐
                    │   FRONTEND (HTML/JS)   │
                    │                        │
                    │ • login.html           │
                    │ • extractor_pro.html   │
                    │ • localStorage (JWT)   │
                    └────────────┬───────────┘
                                 ↓ HTTP
                    ┌────────────────────────┐
                    │   EXPRESS.JS SERVER    │
                    │   (Node.js)            │
                    │                        │
                    │ • Port 3000            │
                    │ • CORS habilitado      │
                    │ • JWT middleware       │
                    └────────────┬───────────┘
                                 ↓
            ┌────────────────────┼────────────────────┐
            ↓                    ↓                    ↓
    ┌──────────────┐     ┌──────────────┐    ┌──────────────┐
    │ PostgreSQL   │     │ Google       │    │ Archivos     │
    │              │     │ Gemini AI    │    │ locales      │
    │ • users      │     │              │    │              │
    │ • invoices   │     │ • Análisis   │    │ • db.js      │
    │              │     │ • OCR        │    │ • routes/    │
    └──────────────┘     └──────────────┘    └──────────────┘
```

---

## 📁 Estructura de Carpetas

```
facturaai_whatsapp_backend/
│
├── login.html                    # Pantalla de autenticación
├── extractor_pro.html            # Dashboard principal
├── QUICKSTART.md                 # Inicio rápido
├── PRODUCTION_DEPLOY.md          # Guía de deploy
│
└── whatsapp_backend/
    ├── server.js                 # Servidor principal
    ├── db.js                     # Configuración PostgreSQL
    ├── migrations.js             # Script de migraciones
    ├── test.js                   # Suite de pruebas
    ├── package.json              # Dependencias
    ├── .env.example              # Variables de entorno
    │
    ├── middleware/
    │   └── auth.js               # Middleware JWT
    │
    └── routes/
        └── auth.js               # Endpoints de autenticación
```

---

## 🔄 Flujo de Autenticación

```
┌──────────────────────────────────────────────────────┐
│ NUEVO USUARIO                                        │
└──────────────────────────────────────────────────────┘

login.html (Usuario registra)
    ↓
POST /api/auth/register
    ├─ Validar datos
    ├─ Hash contraseña (bcryptjs)
    ├─ Guardar en PostgreSQL (tabla users)
    ├─ Generar JWT
    └─ Respuesta: { token, user }
    ↓
localStorage.setItem('token', token)
    ↓
Redirigir a extractor_pro.html
    ↓
extracto_pro.html detecta token
    ├─ Verifica autenticidad
    ├─ Muestra nombre de usuario
    └─ Permite procesar facturas


┌──────────────────────────────────────────────────────┐
│ USUARIO EXISTENTE                                    │
└──────────────────────────────────────────────────────┘

login.html (Usuario ingresa)
    ↓
POST /api/auth/login
    ├─ Validar email existe
    ├─ Comparar contraseña (bcrypt.compare)
    ├─ Generar JWT
    └─ Respuesta: { token, user }
    ↓
localStorage.setItem('token', token)
    ↓
Petición con token en header:
    Authorization: Bearer {JWT}
    ↓
authMiddleware (middleware/auth.js)
    ├─ Extrae token del header
    ├─ Verifica validez con JWT_SECRET
    ├─ Setea req.user = { id, email }
    └─ Continúa a la ruta protegida
```

---

## 🔌 Endpoints API

### Públicos (sin autenticación)

```
POST /api/auth/register
├─ Body: { email, password, full_name }
├─ Status: 201 (éxito) | 400 (error)
└─ Response: { token, user }

POST /api/auth/login
├─ Body: { email, password }
├─ Status: 200 (éxito) | 401 (error)
└─ Response: { token, user }

POST /api/auth/validate
├─ Header: Authorization: Bearer {token}
├─ Status: 200 (válido) | 403 (inválido)
└─ Response: { user }

GET /api/health
├─ Status: 200
└─ Response: { success, version }
```

### Protegidos (requieren JWT)

```
POST /api/procesar-factura
├─ Body: { imageBase64 }
├─ Status: 200 (éxito) | 401 (sin token)
└─ Response: { success, data: {invoice} }

GET /api/registros
├─ Status: 200
└─ Response: { success, data: [{invoice}, ...] }

GET /api/registros/:id
├─ Status: 200 | 404
└─ Response: { success, data: {invoice} }

DELETE /api/registros/:id
├─ Status: 200
└─ Response: { success, message }

GET /api/estadisticas
├─ Status: 200
└─ Response: { success, data: {stats} }
```

---

## 🗄️ Base de Datos

### Tabla `users`

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,  -- bcryptjs hash
  full_name VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Tabla `invoices`

```sql
CREATE TABLE invoices (
  id SERIAL PRIMARY KEY,
  user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  ticket_id VARCHAR(255) NOT NULL,
  business_name VARCHAR(255),
  invoice_date DATE,
  subtotal DECIMAL(10, 2),
  iva DECIMAL(10, 2),
  total DECIMAL(10, 2),
  category VARCHAR(100),                -- Categorización automática
  description TEXT,
  deductible VARCHAR(100),               -- Sí, Parcial, No, Verificar
  deductible_percentage DECIMAL(5, 2),   -- 0-100%
  deductible_amount DECIMAL(10, 2),      -- Monto deducible
  notes TEXT,
  image_base64 LONGTEXT,                 -- Imagen original en base64
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_invoices_user_id ON invoices(user_id);
CREATE INDEX idx_invoices_created_at ON invoices(created_at);
```

---

## 🔐 Flujo de Procesamiento de Factura

```
Usuario sube imagen
    ↓
1. Frontend codifica imagen a Base64
    ↓
2. Envía POST /api/procesar-factura
   Header: Authorization: Bearer {JWT}
   Body: { imageBase64 }
    ↓
3. authMiddleware verifica JWT
    ├─ Válido → req.user = { id, email }
    └─ Inválido → Error 403
    ↓
4. procesar-factura controller
    ├─ Valida imageBase64 no sea nulo
    └─ Llama analyzeReceiptGemini()
    ↓
5. Google Gemini 2.5 Flash
    ├─ Recibe imagen
    ├─ Extrae campos JSON
    │  ├─ ticket_id
    │  ├─ business_name
    │  ├─ invoice_date
    │  ├─ subtotal, iva, total
    │  ├─ category
    │  └─ description
    └─ Devuelve JSON parseado
    ↓
6. saveToPostgreSQL()
    ├─ Calcula deductible según categoría
    ├─ Inserta en tabla invoices
    ├─ user_id = req.user.id
    └─ Retorna ID de factura
    ↓
7. Response 200 OK
    └─ { success, data: {invoice, id} }
    ↓
8. Frontend actualiza UI
    ├─ Agrega a historial
    ├─ Actualiza estadísticas
    └─ Muestra resultado
```

---

## 🛡️ Seguridad

```
┌─────────────────────────────────────────┐
│ CAPAS DE SEGURIDAD                      │
└─────────────────────────────────────────┘

1. TRANSPORTE
   ├─ HTTPS (en producción)
   └─ TLS 1.3

2. AUTENTICACIÓN
   ├─ JWT con firma HS256
   ├─ Expira en 7 días
   └─ Enviado en header Authorization

3. CONTRASEÑAS
   ├─ Hasheadas con bcryptjs (10 rounds)
   └─ Nunca se almacenan en plano

4. SQL INJECTION
   ├─ Prepared statements (pg library)
   └─ Parametrización de queries

5. CORS
   ├─ Whitelistado en producción
   └─ Abierto en desarrollo

6. HEADERS HTTP
   ├─ X-Content-Type-Options: nosniff
   ├─ X-Frame-Options: DENY
   └─ X-XSS-Protection: 1; mode=block

7. VALIDACIÓN
   ├─ Emails validados
   ├─ Contraseñas mínimo 8 caracteres
   └─ Base64 validada
```

---

## 📊 Flujo de Datos

```
CLIENTE (Frontend)
    ↓
TOKEN JWT (localStorage)
    ↓
REQUEST HTTP
├─ Header: Authorization: Bearer {token}
├─ Body: JSON
└─ CORS: Aceptado
    ↓
SERVER (Express.js)
├─ Middleware CORS
├─ Middleware JSON Parser
├─ authMiddleware (JWT)
│  └─ jwt.verify(token, JWT_SECRET)
├─ Controller (ruta específica)
└─ Database Connection
    ↓
POSTGRESQL
├─ Query preparada
├─ User query (user_id = req.user.id)
└─ INSERT/SELECT/DELETE/UPDATE
    ↓
GOOGLE GEMINI (si procesa factura)
├─ Image → Base64
├─ Análisis con IA
└─ JSON Parsed → Base de datos
    ↓
RESPONSE HTTP
├─ Status: 200, 400, 401, 404, 500
├─ Body: { success, data, error }
└─ JSON stringified
    ↓
CLIENTE (Frontend)
├─ Parsea JSON
├─ Actualiza DOM
└─ localStorage (si hay token nuevo)
```

---

## 🚀 Deployment

```
LOCAL DEVELOPMENT
├─ npm install
├─ node migrations.js
├─ npm run dev (nodemon)
└─ Port: 3000

STAGING/TESTING
├─ Git push a rama develop
├─ CI/CD pipeline
├─ Deploy automático
└─ Testeos automáticos

PRODUCTION
├─ Git push a rama main
├─ Build en plataforma (Railway, Render)
├─ Migraciones automáticas
├─ SSL/TLS habilitado
└─ Monitoreo 24/7
```

---

## 🎯 Tecnologías

```
Frontend
├─ HTML5
├─ CSS3 (Modern, sin frameworks)
├─ Vanilla JavaScript (ES6+)
├─ localStorage API

Backend
├─ Node.js 18+
├─ Express.js 4.x
├─ PostgreSQL 13+
├─ Google Gemini 2.5 Flash

Autenticación
├─ JWT (jsonwebtoken)
├─ bcryptjs (hashing)
└─ CORS

DevTools
├─ nodemon (auto-reload)
├─ dotenv (variables)
└─ pg (PostgreSQL driver)
```

---

**Versión**: 2.0 SaaS  
**Última actualización**: Mayo 2024  
**Estado**: Producción-Ready
