# 📚 Kavita Docker - Servidor de Lectura Autohospedado

[![GitHub](https://img.shields.io/badge/GitHub-kavita--docker-blue?logo=github)](https://github.com/jvmilazz0/kavita)
[![Docker](https://img.shields.io/badge/Docker-jvmilazz0%2Fkavita-blue?logo=docker)](https://hub.docker.com/r/jvmilazz0/kavita)
[![License](https://img.shields.io/badge/License-GPL--3.0-orange)](https://github.com/jvmilazz0/kavita/blob/main/LICENSE)

## 📋 Descripción general

**Kavita** es un servidor de lectura completo autohospedado para manga, webtoons, cómics y libros con una interfaz web responsiva, soporte multi-usuario, integración de metadatos, anotaciones, y todo bajo tu control sin dependencias de servicios terceros. Ideal para lectores apasionados, coleccionistas y homelabs.

Propuesta clave: **Reading server enterprise-grade self-hosted**. Soporta manga (CBR, CBZ, ZIP, RAR, 7Z), webtoons, cómics y libros (EPUB, PDF). Lectores web responsivos (mobile, tablet, desktop). Modo lectura continua (webtoon). Modo libro virtual (EPUB). Multi-usuario con control roles (admin, member). Metadatos integrados (cover, descripción, géneros, calificaciones). Integración externa (Kavita+). Anotaciones y highlights EPUB. Filtros inteligentes. Collections, listas lectura, want-to-read. Descarga metadatos, reseñas, calificaciones. Tematización customizable. Localización completa (Weblate). API REST. OPDS feed. Zero cloud, 100% privacidad. GPL-3.0 open source. Production-ready.

## ✨ Características principales

- **Formatos múltiples**: Manga (CBR, CBZ, ZIP, RAR, RAR5, 7Z), Webtoons, Cómics, Libros (EPUB, PDF), Raw images
- **Lectores responsivos**: Web reader completo, Modo webtoon (scroll continuo), Modo libro virtual (EPUB), Funciona perfecto en móvil, tablet, desktop
- **Multi-usuario seguro**: Gestión usuarios, roles (admin, member), Control acceso, Restricciones edad, OIDC (OAuth)
- **Metadatos integrados**: Covers, descripciones, géneros, autores, calificaciones, Búsqueda y filtros avanzados
- **Biblioteca compartida**: Collections, listas lectura, want-to-read, Compartir con amigos/familia
- **Anotaciones EPUB**: Highlights, notas, bookmarks en EPUB, Annotations integradas
- **Dashboard customizable**: Filtros inteligentes, Orden personalizado, Visibilidad toggles
- **Localización completa**: Multi-idioma (Weblate), Temas customizables, API REST, OPDS feed

## 📋 Requisitos del sistema

- Docker & Docker Compose v2+
- 2 GB - 4 GB RAM mínimo (Kavita es ligero)
- 50 GB - 500+ GB espacio disco (según librería manga/cómics/libros)
- `/mnt/media/kavita` directorio accesible (o configurar ruta)
- Puerto TCP disponible: 5000 (configurable)
- CPU: 1-2+ cores (muy ligero)
- Acceso internet (opcional: para metadatos, OPDS feeds)

> **Muy ligero**: Kavita consume pocos recursos. Perfecto para Raspberry Pi, NAS, homelab limitado. SQLite integrado.

### Estructura directorios

```
/mnt/media/kavita/
├── config/          # Configuración Kavita
├── data/            # Base datos SQLite
└── library/         # Manga, cómics, libros (tus archivos)
    ├── manga/
    ├── comics/
    └── books/
```

## 🐳 Instalación

### Paso 1: Preparar estructura directorios

```bash
# Crear carpetas Kavita
sudo mkdir -p /mnt/media/kavita/{config,data,library}

# Permisos
sudo chown -R 1000:1000 /mnt/media/kavita
sudo chmod -R 755 /mnt/media/kavita
```

### Paso 2: Crear docker-compose.yml

```bash
mkdir -p ~/kavita && cd ~/kavita
cat > docker-compose.yml << 'EOF'
version: '3.8'

services:
  kavita:
    image: jvmilazz0/kavita:latest
    container_name: kavita
    restart: unless-stopped
    ports:
      - "5000:5000"
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Madrid
      # Opcionales:
      # - KAVITA_PORT=5000
      # - KAVITA_API_KEY=tuAPIKeySegura
    volumes:
      - /mnt/media/kavita/config:/config
      - /mnt/media/kavita/data:/data
      - /mnt/media/kavita/library:/library
EOF
```

### Paso 3: Iniciar Kavita

```bash
docker compose up -d

# Espera ~10 segundos para que inicie
docker compose ps
# Debería mostrar kavita en estado "Up"
```

### Acceder a Kavita

📚 **Kavita Web UI**: http://localhost:5000

💡 **Desde otros dispositivos**: Usa la IP de tu servidor: `http://192.168.1.100:5000` (reemplaza con tu IP)

Para obtener tu IP:
```bash
hostname -I
```

## ⚙️ Configuración

1. **Variables de entorno principales**:
   - `PUID` / `PGID`: Usuario/Grupo para permisos (default 1000:1000)
   - `TZ`: Zona horaria (ej: Europe/Madrid)
   - `KAVITA_PORT`: Puerto interno (default 5000)
   - `KAVITA_API_KEY`: API Key para acceso programático

2. **Volúmenes persistentes**:
   - `/config`: Configuración de la aplicación
   - `/data`: Base de datos SQLite
   - `/library`: Archivos de manga, cómics y libros

3. **Puertos**: Mapea `5000:5000` (host:container)

4. **Restart policy**: `unless-stopped` para auto-inicio

## 🚀 Primeros pasos

1. **Setup inicial (username y contraseña)**
   - Abre http://localhost:5000
   - Setup wizard aparece automático
   - Configura: Admin username (ej: admin), Admin password (segura!), Directorio librería (ej: /library)
   - Click "Confirm" → Kavita inicia library scan

2. **Agregar contenido a la librería**
   - Copia tus archivos manga/cómics/libros a `/mnt/media/kavita/library/`
   - Formatos soportados:
     - Manga/Cómics: CBR, CBZ, ZIP, RAR, RAR5, 7Z
     - Webtoons: archivos imagen en carpeta
     - Libros: EPUB, PDF
   - Estructura recomendada:
     ```
     /mnt/media/kavita/library/
     ├── Manga/
     │   └── [Manga Título]/
     │       └── [Volumen 1]/
     │           ├── image1.jpg
     │           └── image2.jpg
     ├── Comics/
     │   └── [Comic Nombre].cbz
     └── Books/
         └── [Libro Nombre].epub
     ```
   - Ve a Settings → Library → Scan now
   - Kavita detecta e indexa automático

3. **Explorar y leer**
   - Dashboard muestra series detectadas
   - Click en serie → ver volúmenes
   - Click en volumen → abre reader
   - Reader soporta: Modo página (manga/cómics), Modo webtoon (scroll continuo), Modo libro (EPUB virtual pages)

4. **Crear usuarios**
   - Settings → Users → New user
   - Fill: username, email, password, rol
   - Roles: Admin, Member
   - Click "Add user" → usuario puede login

5. **Crear collections y listas lectura**
   - Collections: agrupa series por tema (ej: "Favoritos")
   - Reading Lists: secuencia lectura personalizada
   - Want to Read: guardados para leer después
   - Comparte colecciones con otros usuarios

6. **Configurar metadatos**
   - Settings → Libraries
   - Kavita auto-detecta covers, descripciones, géneros
   - Opción: integración Kavita+ (descarga metadatos externos)

7. **Usar OPDS feed**
   ```bash
   # OPDS feed URL para apps OPDS compatibles:
   http://localhost:5000/api/opds
   # Compatible: Moon+ Reader, Elytra, Page Turner, etc
   ```

## 💡 Casos de uso

- **Lectores manga/cómics**: Librería personal. Multi-dispositivo. Lector web profesional.
- **Coleccionistas**: Organiza manga, webtoons, cómics en servidor propio. Metadatos automáticos.
- **Familia/amigos**: Comparte librería lectura con multi-usuario. Biblioteca compartida segura.
- **Homelabs**: Ultra-ligero. Perfecto Raspberry Pi, NAS, VPS pequeño.
- **Privacidad**: Self-hosted. Sin cloud. Sin tracking. Control total datos lectura.

## 🔒 Acceso remoto seguro

Para acceso externo seguro, se recomienda:

- **Reverse Proxy** (Nginx Proxy Manager, Traefik, Caddy) con SSL/TLS
- **VPN** (WireGuard, Tailscale) para acceso privado
- **Authelia/Keycloak** para autenticación adicional
- **Fail2ban** para protección contra fuerza bruta

Ejemplo con Nginx Proxy Manager:
```nginx
# Configuración proxy pass hacia http://kavita:5000
# Habilitar WebSocket support para reader
proxy_http_version 1.1;
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
```

## 🛠️ Gestión y mantenimiento

### Ver estado
```bash
docker compose ps
```

### Ver logs
```bash
docker compose logs -f kavita
```

### Detener Kavita
```bash
docker compose down
# Datos persisten en /mnt/media/kavita
```

### Actualizar a versión nueva
```bash
docker compose pull
docker compose up -d
```

### Re-scan librería
```bash
# Desde UI: Settings → Library → Scan Now
# O desde API:
curl -X POST http://localhost:5000/api/library/scan \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### Backup datos
```bash
tar -czf kavita-backup-$(date +%Y%m%d).tar.gz /mnt/media/kavita
```

### Restore desde backup
```bash
docker compose down
tar -xzf kavita-backup-20240815.tar.gz -C /
docker compose up -d
```

### Formatos soportados

**Manga y Cómics**: CBR, CBZ, ZIP, RAR/RAR5, 7Z, Raw images (JPG, PNG, etc en carpeta)

**Libros**: EPUB (con annotations), PDF

**Formatos imagen**: JPG, PNG, GIF, WebP, BMP, TIFF

### Comparativa con alternativas

| Característica | Kavita | Ubooquity | Komga |
|----------------|--------|-----------|-------|
| Interface moderna | ✅ | ❌ | ✅ |
| Metadatos integrados | ✅ | ❌ | ✅ |
| Multi-usuario avanzado | ✅ | ⚠️ | ✅ |
| Lectores responsivos | ✅ | ❌ | ✅ |
| Anotaciones EPUB | ✅ | ❌ | ❌ |
| Libros EPUB nativos | ✅ | ✅ | ❌ |
| Enfoque específico cómics | ✅ | ✅ | ✅ |

**Mejor para**: Servidor lectura self-hosted. Multi-dispositivo. Multi-usuario. Librería completa (manga/cómics/libros). Homelabs.

## 📝 Licencia

Este proyecto utiliza la imagen oficial de **Kavita** licenciada bajo **GPL-3.0**.

- [Kavita GitHub - Repositorio oficial](https://github.com/Kareadita/Kavita)
- [Licencia GPL-3.0](https://github.com/Kareadita/Kavita/blob/main/LICENSE)

---

> **Referencias oficiales**:
> - [Kavita Wiki - Documentación completa](https://wiki.kavitareader.com/)
> - [Getting Started Guide](https://wiki.kavitareader.com/en/getting-started)
> - [Kavita+ - Premium features](https://kavitareader.com/kavita-plus)
> - [Demo Kavita - Prueba online](https://demo.kavitareader.com/)
> - [Docker Hub - Kavita image](https://hub.docker.com/r/jvmilazz0/kavita)
> - [Kavita Discord - Comunidad](https://discord.gg/kavita)

---

**📖 Basado en el post**: [Cómo instalar Kavita en Docker - Servidor de lectura autohospedado](https://genbyte.blogspot.com/2026/09/como-instalar-kavita-en-docker-servidor.html)

**🌐 Genbyte**: [YouTube](https://youtube.com/@genbyte) | [Newsletter](https://genbyte.blogspot.com/newsletter) | [Ko-fi](https://ko-fi.com/genbyte) | [Telegram](https://t.me/genbyte) | [Discord](https://discord.gg/genbyte) | [GitHub](https://github.com/genbyte) | [Blog](https://genbyte.blogspot.com) | [Twitter](https://twitter.com/genbyte)