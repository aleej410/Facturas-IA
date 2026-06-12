# ✅ Checklist de Verificación - FacturaAI SaaS v2.0

## 📋 Pre-Instalación

- [ ] Tienes Node.js 18+ instalado: `node --version`
- [ ] Tienes PostgreSQL instalado y ejecutándose
- [ ] Tienes npm instalado: `npm --version`
- [ ] Tienes una clave de Google Gemini API

---

## 🔧 Instalación Local

### Paso 1: Configurar PostgreSQL
- [ ] PostgreSQL está corriendo (verificar con `psql --version`)
- [ ] Ejecutaste: `psql -U postgres -c "CREATE DATABASE facturaai;"`

### Paso 2: Configurar Variables de Entorno
- [ ] Copiaste `.env.example` a `.env`
- [ ] Completaste `DB_USER` (default: postgres)
- [ ] Completaste `DB_PASSWORD` (tu contraseña de PostgreSQL)
- [ ] Completaste `GEMINI_API_KEY` (de https://aistudio.google.com/app/apikeys)
- [ ] Generaste un `JWT_SECRET` aleatorio

### Paso 3: Instalar Dependencias
- [ ] Ejecutaste `npm install`
- [ ] Se instalaron todas las dependencias sin errores

### Paso 4: Crear Tablas en BD
- [ ] Ejecutaste `node migrations.js`
- [ ] Obtuviste confirmación "✨ Migraciones completadas exitosamente"

### Paso 5: Iniciar Servidor
- [ ] Ejecutaste `npm start` o `npm run dev`
- [ ] Ves "🚀 API FacturaAI SaaS corriendo en puerto 3000"

---

## 🧪 Testing

### Test 1: Verificar API
```bash
# En otra terminal
curl http://localhost:3000/api/health
```
- [ ] Recibiste respuesta JSON con `"success": true`

### Test 2: Suite Automática
```bash
node test.js
```
- [ ] Todos los tests pasaron (9/9 ✅)

### Test 3: Registro Manual
- [ ] Abre `login.html` en el navegador
- [ ] Haz clic en "Registro"
- [ ] Completa: nombre, email, contraseña
- [ ] Recibiste confirmación "¡Cuenta creada!"
- [ ] Fuiste redirigido a dashboard

### Test 4: Subir Factura de Prueba
- [ ] En dashboard, haz clic en el área de carga
- [ ] Selecciona una imagen de factura
- [ ] Espera a que Gemini procese
- [ ] Ves los datos extraídos

---

## 📦 Verificar Instalación de Dependencias

```bash
cd whatsapp_backend
npm list --depth=0
```

Deberías ver:
- [ ] `express@^4.19.0`
- [ ] `cors@^2.8.6`
- [ ] `dotenv@^16.4.0`
- [ ] `pg@^8.11.3`
- [ ] `jsonwebtoken@^9.1.2`
- [ ] `bcryptjs@^2.4.3`
- [ ] `@google/generative-ai@^0.24.1`

---

## 🗄️ Verificar Base de Datos

```bash
# Conectar a PostgreSQL
psql -U postgres -d facturaai

# Listar tablas
\dt

# Verificar estructura de users
\d users

# Verificar estructura de invoices
\d invoices

# Contar usuarios
SELECT COUNT(*) FROM users;

# Salir
\q
```

- [ ] Ves tabla `users` creada
- [ ] Ves tabla `invoices` creada
- [ ] Hay índices en `invoices`

---

## 🔐 Verificar Seguridad

### JWT
- [ ] `JWT_SECRET` está configurado en `.env`
- [ ] `JWT_SECRET` tiene al menos 32 caracteres
- [ ] Token se guarda en localStorage

### Contraseñas
- [ ] Las contraseñas se hashean (bcryptjs)
- [ ] Las contraseñas no están visibles en BD

### CORS
- [ ] CORS está configurado en `server.js`
- [ ] Frontend puede hacer requests al API

---

## 📱 Interfaz de Usuario

### Login Page (`login.html`)
- [ ] Página carga correctamente
- [ ] Dos pestañas: "Acceso" y "Registro"
- [ ] Formularios con validaciones
- [ ] Botones funcionan
- [ ] Mensajes de error aparecen
- [ ] Token se guarda después de login

### Dashboard (`extractor_pro.html`)
- [ ] Página carga después de login
- [ ] Muestra nombre de usuario en header
- [ ] Botón "CERRAR SESIÓN" funciona
- [ ] Sin token, redirige a login.html

---

## 🔌 API Endpoints

### Público
- [ ] `GET /api/health` → 200 OK
- [ ] `POST /api/auth/register` → 201 Created
- [ ] `POST /api/auth/login` → 200 OK
- [ ] `POST /api/auth/validate` → 200 OK (con token)

### Protegidos
- [ ] `GET /api/registros` → 200 OK (con token)
- [ ] `GET /api/registros/:id` → 200 OK o 404
- [ ] `DELETE /api/registros/:id` → 200 OK
- [ ] `GET /api/estadisticas` → 200 OK
- [ ] `POST /api/procesar-factura` → 200 OK (con imagen)

### Sin Autenticación
- [ ] Todos rechazados con 401 Unauthorized

---

## 📊 Datos de Prueba

Usuario de prueba creado:
- [ ] Email registrado en BD
- [ ] Contraseña hasheada (no en plano)
- [ ] Al menos 1 factura procesada

---

## 🚀 Próximos Pasos

- [ ] Leer [QUICKSTART.md](./QUICKSTART.md)
- [ ] Leer [README_SAAS.md](./whatsapp_backend/README_SAAS.md)
- [ ] Leer [ARCHITECTURE.md](./ARCHITECTURE.md)
- [ ] Realizar deploy a producción ([PRODUCTION_DEPLOY.md](./PRODUCTION_DEPLOY.md))

---

## 🐛 Troubleshooting

Si algo falla, verifica:

1. **PostgreSQL no conecta**
   - [ ] PostgreSQL está corriendo
   - [ ] Credenciales en `.env` son correctas
   - [ ] Base de datos `facturaai` existe

2. **Migraciones fallan**
   - [ ] PostgreSQL está corriendo
   - [ ] Usuario postgres existe
   - [ ] Base de datos facturaai existe

3. **API no inicia**
   - [ ] Puerto 3000 no está en uso
   - [ ] `GEMINI_API_KEY` está configurado
   - [ ] `JWT_SECRET` está configurado

4. **Login/Registro falla**
   - [ ] Servidor está corriendo (npm start)
   - [ ] Base de datos está conectada
   - [ ] Validaciones se cumplen (email válido, pass > 8 chars)

5. **Procesar factura falla**
   - [ ] `GEMINI_API_KEY` válido
   - [ ] Imagen es JPEG/PNG válido
   - [ ] Usuario está autenticado

---

## 📈 Performance

- [ ] Servidor responde en < 500ms
- [ ] Gemini responde en < 3s
- [ ] Base de datos responde en < 100ms
- [ ] Frontend carga en < 2s

---

## ✨ Confirmación Final

- [ ] ✅ Instalación completada
- [ ] ✅ Base de datos funcionando
- [ ] ✅ API ejecutándose
- [ ] ✅ Frontend accesible
- [ ] ✅ Tests pasando
- [ ] ✅ Autenticación funcionando
- [ ] ✅ Procesamiento de facturas funcionando

**¡LISTO PARA USAR!** 🎉
