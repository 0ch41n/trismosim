# Trismosim

Progressive Web App (PWA) encapsulada para instalación en dispositivos móviles.

## 🚀 Características

- ✅ Instalable como aplicación nativa en Android
- ✅ Icono personalizado en la pantalla de inicio
- ✅ Modo encapsulado (sin barra del navegador)
- ✅ Funciona offline con Service Worker
- ✅ Optimizada para dispositivos móviles

## 📱 Instalación

Visita **www.trismosim.com** desde tu navegador móvil (Chrome en Android) y verás la opción de instalar la aplicación. Un icono se agregará a tu escritorio para ejecutar la web en modo encapsulado.

## 🔧 Desarrollo

Para más información sobre desarrollo y despliegue, consulta [DEPLOYMENT.md](./DEPLOYMENT.md).

```bash
# Servidor local de desarrollo
python3 -m http.server 8000
```

## 📂 Estructura

```
.
├── index.html          # Página principal
├── manifest.json       # Web App Manifest
├── sw.js              # Service Worker
├── icons/             # Iconos de la aplicación
│   ├── icon-192x192.png
│   └── icon-512x512.png
├── DEPLOYMENT.md      # Guía de despliegue
└── README.md          # Este archivo
```
