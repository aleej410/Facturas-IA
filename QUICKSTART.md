# 🚀 Inicio Rápido - FacturaAI SaaS v2.0

## ⚡ Setup en 5 minutos

### Paso 1: Instalar PostgreSQL

**Windows:**
- Descarga desde: https://www.postgresql.org/download/windows/
- En la instalación, anota la contraseña del usuario `postgres`
- Instala pgAdmin (incluido)

**macOS:**
```bash
brew install postgresql@15
brew services start postgresql@15
```

**Linux (Ubuntu):**
```bash
sudo apt install postgresql postgresql-contrib
sudo systemctl start postgresql
```

---

### Paso 2: Crear Base de Datos

Abre la terminal/PowerShell:

```bash
psql -U postgres
```

Luego en la consola de PostgreSQL:

```sql
CREATE DATABASE facturaai;
\q
```

---

### Paso 3: Configurar Variables de Entorno

En la carpeta `whatsapp_backend/`, crea o edita `.env`:

```env
PORT=3000
DB_USER=postgres
DB_PASSWORD=tu-contraseña-postgres
DB_HOST=localhost
DB_PORT=5432
DB_NAME=facturaai
DB_SSL=false

GEMINI_API_KEY=obtén-de-https://aistudio.google.com/app/apikeys
JWT_SECRET=tu-clave-secreta-super-aleatoria

NODE_ENV=development
```

---

### Paso 4: Instalar Dependencias

```bash
cd whatsapp_backend
npm install
```

---

### Paso 5: Ejecutar Migraciones

Crear las tablas en PostgreSQL:

```bash
node migrations.js
```

Deberías ver ✅ confirmaciones.

---

### Paso 6: Iniciar el Servidor

```bash
npm start
```

Verás:
```
🚀 API FacturaAI SaaS corriendo en puerto 3000
📊 Base de datos: PostgreSQL
🔐 Autenticación: JWT
🤖 IA: Google Gemini 2.5 Flash
```

---

## 🌐 Acceder a la Aplicación

### Opción A: Abrir archivo local

1. Abre `login.html` en el navegador (o arrastra el archivo)
2. Haz clic en "Registro" para crear una cuenta
3. Completa el formulario
4. ¡Serás redirigido al dashboard!

### Opción B: Servidor local (recomendado)

```bash
# Terminal 2 - Inicia un servidor web simple
cd c:\Users\hp\Downloads\facturaai_whatsapp_backend
npx http-server
```

Luego accede a:
- Login: http://localhost:8080/login.html
- Dashboard: http://localhost:8080/extractor_pro.html

---

## ✅ Verificar que todo funciona

```bash
# En otra terminal
curl http://localhost:3000/api/health
```

Deberías recibir:
```json
{"success":true,"message":"🚀 API activa","version":"2.0-saas"}
```

---

## 📝 Crear Primera Cuenta de Prueba

1. Abre `login.html`
2. Haz clic en "Registro"
3. Completa:
   - Nombre: Juan Pérez
   - Email: juan@ejemplo.com
   - Contraseña: Password123
4. ¡Listo! Ya tienes acceso al dashboard

---

## 🔧 Troubleshooting

### "Error: Cannot find module pg"
```bash
npm install
```

### "ECONNREFUSED - No se conecta a PostgreSQL"
```bash
# Windows - Verificar que PostgreSQL está corriendo
Get-Service | findstr postgres

# macOS
brew services list

# Linux
sudo systemctl status postgresql
```

### "Database facturaai does not exist"
```bash
psql -U postgres -c "CREATE DATABASE facturaai;"
```

### "Token inválido" en el dashboard
- Borra cookies/localStorage: `F12` → Application → localStorage → Eliminar
- Recarga la página y login nuevamente

---

## 📚 Recursos

- 📖 [README_SAAS.md](./README_SAAS.md) - Documentación completa
- 🔌 [API Endpoints](#api-endpoints) - Lista de endpoints
- 🐘 [PostgreSQL Docs](https://www.postgresql.org/docs/) - Documentación oficial

---

## 🎯 Próximos Pasos

1. ✅ Crear cuenta de prueba
2. ✅ Subir una factura de prueba
3. ✅ Verificar datos en PostgreSQL
4. ⏳ Configurar deploy a producción (Railway, Render, etc.)

---

**¡Listo!** 🎉 Ya tienes FacturaAI SaaS corriendo localmente.
