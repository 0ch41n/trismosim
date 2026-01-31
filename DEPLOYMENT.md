# Guía de Implementación PWA - Trismosim

## 📱 ¿Qué es esto?

Este proyecto implementa una Progressive Web App (PWA) para Trismosim, permitiendo a los usuarios de Android (y otros dispositivos) instalar el sitio web como una aplicación nativa en su dispositivo.

## ✨ Características

- **Instalable**: Los usuarios pueden instalar la app desde el navegador
- **Icono en escritorio**: Genera un icono personalizado en la pantalla de inicio
- **Modo encapsulado**: Se ejecuta en modo standalone sin la barra del navegador
- **Funciona offline**: Gracias al Service Worker, la app funciona sin conexión
- **Optimizada para móviles**: Diseño responsive y táctil

## 🚀 Despliegue

### Requisitos

Para que la PWA funcione correctamente en producción, necesitas:

1. **HTTPS**: El sitio web DEBE estar servido a través de HTTPS (excepto localhost para desarrollo)
2. **Servidor web**: Cualquier servidor web que pueda servir archivos estáticos (Apache, Nginx, GitHub Pages, etc.)

### Opción 1: GitHub Pages

1. Ve a la configuración del repositorio en GitHub
2. Navega a "Settings" > "Pages"
3. En "Source", selecciona la rama que contiene los archivos (por ejemplo, `main`)
4. Guarda los cambios
5. GitHub Pages automáticamente servirá el sitio en `https://0ch41n.github.io/trismosim/`

**Importante**: Si usas GitHub Pages con un subdirectorio, actualiza las rutas en `manifest.json` y `sw.js`:
- Cambia `"start_url": "/"` a `"start_url": "/trismosim/"`
- Cambia `"scope": "/"` a `"scope": "/trismosim/"`
- Actualiza las rutas en el Service Worker

### Opción 2: Servidor Personalizado (Nginx)

Configuración de ejemplo para Nginx:

```nginx
server {
    listen 443 ssl http2;
    server_name www.trismosim.com;
    
    # Certificados SSL
    ssl_certificate /path/to/certificate.crt;
    ssl_certificate_key /path/to/private.key;
    
    root /var/www/trismosim;
    index index.html;
    
    # Headers para PWA
    add_header Service-Worker-Allowed /;
    
    location / {
        try_files $uri $uri/ /index.html;
    }
    
    # Cache para assets estáticos
    location ~* \.(png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
    
    # Manifest y Service Worker - no cachear
    location ~* (manifest\.json|sw\.js)$ {
        add_header Cache-Control "no-cache, no-store, must-revalidate";
        add_header Pragma "no-cache";
        add_header Expires "0";
    }
}

# Redirigir HTTP a HTTPS
server {
    listen 80;
    server_name www.trismosim.com;
    return 301 https://$server_name$request_uri;
}
```

### Opción 3: Apache

Archivo `.htaccess`:

```apache
# Forzar HTTPS
RewriteEngine On
RewriteCond %{HTTPS} off
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]

# Headers para PWA
<IfModule mod_headers.c>
    Header set Service-Worker-Allowed "/"
</IfModule>

# No cachear manifest y service worker
<FilesMatch "(manifest\.json|sw\.js)$">
    <IfModule mod_headers.c>
        Header set Cache-Control "no-cache, no-store, must-revalidate"
        Header set Pragma "no-cache"
        Header set Expires 0
    </IfModule>
</FilesMatch>

# Cachear assets estáticos
<FilesMatch "\.(ico|png|jpg|jpeg|gif|svg)$">
    <IfModule mod_headers.c>
        Header set Cache-Control "max-age=31536000, public, immutable"
    </IfModule>
</FilesMatch>
```

## 📱 Cómo Instalar en Android

1. Abre Chrome en tu dispositivo Android
2. Navega a `www.trismosim.com`
3. Verás un botón "Instalar Aplicación" o un banner de instalación
4. Toca "Instalar" o el botón en la página
5. La aplicación se agregará a tu pantalla de inicio
6. Abre la app tocando el icono en tu escritorio

### Instalación Manual

Si el banner no aparece automáticamente:

1. Toca el menú de Chrome (⋮)
2. Selecciona "Instalar aplicación" o "Agregar a pantalla de inicio"
3. Confirma la instalación

## 🎨 Personalización

### Cambiar Iconos

Reemplaza los archivos en `/icons/`:
- `icon-192x192.png`: Icono pequeño (192x192 píxeles)
- `icon-512x512.png`: Icono grande (512x512 píxeles)

Usa herramientas como [PWA Asset Generator](https://www.pwabuilder.com/imageGenerator) para generar todos los tamaños necesarios.

### Cambiar Colores

Edita `manifest.json`:
```json
{
  "theme_color": "#2196F3",      // Color de la barra de estado
  "background_color": "#667eea"  // Color de fondo al iniciar
}
```

También actualiza el meta tag en `index.html`:
```html
<meta name="theme-color" content="#2196F3">
```

### Modificar Contenido

Edita `index.html` para cambiar el contenido de la aplicación.

## 🔧 Desarrollo Local

Para probar localmente:

```bash
# Opción 1: Python
python3 -m http.server 8000

# Opción 2: Node.js
npx http-server -p 8000

# Opción 3: PHP
php -S localhost:8000
```

Luego abre `http://localhost:8000` en tu navegador.

**Nota**: Algunas funciones PWA solo funcionan en HTTPS. Para probar con HTTPS localmente, usa herramientas como [ngrok](https://ngrok.com/) o [localhost.run](https://localhost.run/).

## 🧪 Validación

### Verificar PWA

1. Abre Chrome DevTools (F12)
2. Ve a la pestaña "Application"
3. Verifica:
   - **Manifest**: Debe mostrar todos los campos correctamente
   - **Service Workers**: Debe aparecer como "activated and running"
   - **Lighthouse**: Ejecuta una auditoría PWA (debe obtener >90 puntos)

### Lighthouse Audit

```bash
# Instalar Lighthouse
npm install -g lighthouse

# Ejecutar auditoría
lighthouse https://www.trismosim.com --view
```

## 📋 Checklist de Producción

- [ ] Sitio servido a través de HTTPS
- [ ] Manifest.json accesible y válido
- [ ] Service Worker registrado correctamente
- [ ] Iconos de todos los tamaños disponibles
- [ ] Theme color configurado
- [ ] Meta tags viewport configurados
- [ ] Prueba de instalación en Android exitosa
- [ ] Aplicación funciona en modo offline
- [ ] Lighthouse PWA score > 90

## 🐛 Resolución de Problemas

### El banner de instalación no aparece

- Verifica que el sitio esté en HTTPS
- Asegúrate de que el manifest.json sea válido
- Verifica que el Service Worker esté registrado
- El usuario debe visitar el sitio al menos dos veces con 5 minutos de diferencia

### Service Worker no se actualiza

```javascript
// Forzar actualización en sw.js
const CACHE_NAME = 'trismosim-v2'; // Incrementa la versión
```

Luego, recarga la página con Ctrl+Shift+R (hard refresh).

### Errores de CORS con iconos

Asegúrate de que los iconos estén en el mismo dominio o configura CORS headers correctamente.

## 📚 Referencias

- [Web.dev - Progressive Web Apps](https://web.dev/progressive-web-apps/)
- [MDN - Progressive Web Apps](https://developer.mozilla.org/es/docs/Web/Progressive_web_apps)
- [Google Developers - PWA Checklist](https://developers.google.com/web/progressive-web-apps/checklist)
- [PWA Builder](https://www.pwabuilder.com/)

## 📄 Licencia

Este proyecto está disponible bajo la licencia especificada en el repositorio.
