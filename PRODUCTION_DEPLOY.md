# 🌐 Deploy a Producción - FacturaAI SaaS

## Plataformas Recomendadas

### 1. **Railway** (⭐ Recomendado)
Más fácil, incluye PostgreSQL

### 2. **Render**
Buena opción, también incluye PostgreSQL

### 3. **Heroku** (ya no es gratis)
Requiere plan pagado

---

## 🚀 Deploy en Railway

### Paso 1: Preparar el Proyecto

```bash
# Asegurate que todo está en orden
git init
git add .
git commit -m "Initial commit - FacturaAI SaaS v2.0"
```

### Paso 2: Crear Cuenta en Railway

1. Ve a https://railway.app
2. Sign up con GitHub
3. Crea un nuevo proyecto

### Paso 3: Agregar PostgreSQL

1. En Railway, haz clic en "+ New"
2. Selecciona "Database" → PostgreSQL
3. Railway crea la BD automáticamente

### Paso 4: Agregar la Aplicación Node.js

1. Haz clic en "+ New"
2. Selecciona "GitHub repo"
3. Conecta tu repositorio
4. Railway detectará que es Node.js

### Paso 5: Configurar Variables de Entorno

En Railway, ve a Variables:

```
DB_USER=postgres
DB_PASSWORD=<auto-generada por Railway>
DB_HOST=<host de la BD>
DB_PORT=5432
DB_NAME=railway

GEMINI_API_KEY=<tu-clave-gemini>
JWT_SECRET=<generar-aleatorio-seguro>

CORS_ORIGIN=https://tu-app.railway.app
NODE_ENV=production
PORT=8080
```

### Paso 6: Configurar Build

En Railway → Build:

```
Install Command: npm install
Build Command: (leave empty)
Start Command: npm start
```

### Paso 7: Deploy

Railway automáticamente hace deploy cuando actualizas el repositorio.

---

## 🚀 Deploy en Render

### Paso 1: Preparar el Proyecto

```bash
git init
git add .
git commit -m "Initial commit"
git push origin main
```

### Paso 2: Crear PostgreSQL en Render

1. Ve a https://render.com
2. Crea una BD PostgreSQL
3. Copia las credenciales

### Paso 3: Crear Web Service

1. Click en "+ New" → "Web Service"
2. Conecta tu GitHub repo
3. Configura:
   - **Name**: facturaai-api
   - **Environment**: Node
   - **Build Command**: npm install
   - **Start Command**: npm start

### Paso 4: Variables de Entorno

```
DB_USER=postgres
DB_PASSWORD=<de-tu-bd>
DB_HOST=<host-de-render>
DB_PORT=5432
DB_NAME=facturaai

GEMINI_API_KEY=<tu-clave>
JWT_SECRET=<aleatorio>

NODE_ENV=production
```

---

## 🔐 Pasos de Seguridad para Producción

### 1. Generar JWT_SECRET Seguro

```bash
# PowerShell
$bytes = [System.Text.Encoding]::UTF8.GetBytes("secreto")
$rng = New-Object System.Security.Cryptography.RNGCryptoServiceProvider
$rng.GetBytes($bytes)
$jwt_secret = [Convert]::ToBase64String($bytes)
Write-Host $jwt_secret
```

O usa este generador online: https://generate-random.org/

### 2. Habilitar HTTPS/SSL

- Railway: Automático
- Render: Automático
- Heroku: Automático

### 3. Actualizar Frontend

En `login.html` y `extractor_pro.html`, cambia:

```javascript
// De:
const API_URL = 'http://localhost:3000/api';

// A:
const API_URL = 'https://tu-api.railway.app/api';
```

### 4. Actualizar .env.example

```env
# Comentar localhost
# DB_HOST=localhost

# Comentar development
# NODE_ENV=development
```

---

## 🔄 Actualizar Base de Datos Automáticamente

Si cambias el schema, agrega esto al inicio del `server.js`:

```javascript
// Auto-migrate on startup
async function autoMigrate() {
  try {
    await require('./migrations.js');
  } catch (err) {
    console.log('✓ Base de datos ya está actualizada');
  }
}

autoMigrate();
```

---

## 📊 Monitorear en Producción

### Railway

1. Ve a "Deployments"
2. Visualiza logs en tiempo real
3. Monitorea CPU, memoria, BD

### Render

1. Ve a "Logs"
2. Visualiza stdout/stderr
3. Alertas de errores

---

## 🚨 Troubleshooting

### Error: "Database connection failed"

Verifica que las credenciales en `.env` sean correctas:

```bash
# Railway
echo $DATABASE_URL
```

### Error: "CORS origin not allowed"

Actualiza en `server.js`:

```javascript
CORS_ORIGIN=https://tu-dominio.com
```

### Error: "JWT_SECRET not defined"

Asegúrate de que la variable está en la plataforma:

```bash
# Railway
railway vars
```

---

## 💡 Tips de Producción

1. **Backups**: Configura backups automáticos de PostgreSQL
2. **Logs**: Usa servicios como LogRocket o Sentry
3. **Monitoreo**: Configura alertas de errores
4. **Rate Limiting**: Agrega rate limiting para la API
5. **CDN**: Sirve frontend desde CloudFlare

---

## 📈 Escalabilidad Futura

Cuando crezca la aplicación:

1. **Read Replicas**: Múltiples instancias de lectura
2. **Caching**: Redis para cachear resultados
3. **Queue**: Bull/RabbitMQ para procesamiento asincrónico
4. **Microservicios**: Separar en múltiples servicios

---

**Éxito con tu deploy!** 🎉
