# Guía de Despliegue en Dokploy: JKA ID Portal

Dado que la web actual es un sitio estático (un archivo `index.html` y sus imágenes locales asociadas), alojarlo en tu servidor de **Dokploy** es la opción ideal, rápida y robusta. 

Dokploy cuenta con soporte nativo para **sitios estáticos** y genera certificados SSL (HTTPS) de forma automática con un solo clic.

A continuación, tienes la guía paso a paso y la estructura recomendada para poner la web en línea con tu dominio `jkaregistration.nexopsystem.com`.

---

## Paso 1: Estructurar los Archivos del Proyecto
Para que Dokploy (a través de Nixpacks o Docker) reconozca y sirva el sitio correctamente, organiza los archivos de tu proyecto en una sola carpeta antes de subirlos a tu repositorio de Git (GitHub o GitLab).

Asegúrate de incluir los siguientes archivos en la raíz del proyecto:
* `index.html` (El archivo principal del portal).
* `about_jka.jpeg` (Imagen de la sección "¿Qué es JKA?").
* `hero_karate.jpeg` (Imagen de fondo del Hero).
* `jka_id_system_preview.jpg` (Imagen del sistema JKA ID).
* `who_we_are.jpeg` (Imagen de la sección "Quiénes Somos").
* `JKA-Logo-NEW-png.png` (Logo grande de la cabecera).
* `JKA-Logo-NEW-white.png` (Logo blanco del pie de página).
* `Solo-Logo-JKA.png` (Logo circular de la barra de navegación).

### (Opcional) Agregar un `Dockerfile` para Máxima Robustez
Para garantizar que se sirva de la manera más rápida y ligera, puedes agregar un archivo llamado exactamente **`Dockerfile`** (sin extensión) en la raíz del proyecto con este contenido:

```dockerfile
# Usamos un servidor Nginx ultraligero
FROM nginx:alpine

# Copiamos todos los archivos estáticos al directorio público de Nginx
COPY . /usr/share/nginx/html/

# Exponemos el puerto 80
EXPOSE 80
```

---

## Paso 2: Configurar el Dominio en tu DNS
Antes de configurar Dokploy, debes apuntar tu dominio `jkaregistration.nexopsystem.com` a la dirección IP de tu servidor Dokploy:

1. Ve al proveedor donde administras los DNS de `nexopsystem.com` (Cloudflare, GoDaddy, Namecheap, etc.).
2. Crea un nuevo registro:
   * **Tipo**: `A`
   * **Nombre (Host)**: `jkaregistration`
   * **Valor (IP)**: `[La dirección IP pública de tu servidor Dokploy]`
   * **TTL**: Automático o 3600 segundos.
   * *(Si usas Cloudflare, puedes activar o desactivar la nube naranja de Proxy, Dokploy maneja SSL de forma nativa en ambos casos).*

---

## Paso 3: Crear la Aplicación en el Panel de Dokploy

1. **Entra a tu panel de Dokploy**.
2. **Crea o selecciona un Proyecto**:
   * En el menú lateral, haz clic en **Projects** y selecciona tu proyecto actual (o crea uno nuevo llamado `JKA Portal`).
3. **Crea una nueva Aplicación (Application)**:
   * Haz clic en **Create Service** y selecciona **Application**.
   * Ponle un nombre descriptivo, por ejemplo: `jka-registration-web`.

---

## Paso 4: Conectar tu Repositorio y Desplegar

1. **Configura el Provider (GitHub/GitLab)**:
   * Selecciona tu cuenta de GitHub conectada a Dokploy.
   * Elige el repositorio donde subiste los archivos y la rama (habitualmente `main` o `master`).
2. **Configura el Build Type**:
   * **Si usas el Dockerfile opcional**: Selecciona **Dockerfile** (Dokploy detectará el archivo de forma automática y lo compilará).
   * **Si NO usas Dockerfile**: Selecciona **Nixpacks**. Nixpacks detectará de forma automática que es una web estática (`index.html`) y levantará un servidor Nginx para servirlo.
3. **Guarda y Despliega**:
   * Haz clic en **Save** y luego en **Deploy** para iniciar la primera compilación. Tardará menos de un minuto.

---

## Paso 5: Asignar tu Dominio Personalizado en Dokploy

Una vez que la aplicación compile y su estado sea "Running" (en verde):

1. Dentro del servicio `jka-registration-web`, dirígete a la pestaña **Domains**.
2. Haz clic en **Add Domain**.
3. Rellena los campos:
   * **Host**: `jkaregistration.nexopsystem.com`
   * **Path**: `/`
   * **Port**: `80`
4. Marca la casilla **Generate SSL Certificate (Let's Encrypt)** para que la web cuente con HTTPS seguro de forma gratuita.
5. Haz clic en **Save**.

¡Listo! Dokploy configurará el proxy inverso Nginx interno y generará el certificado de seguridad en unos segundos. Tu portal JKA ID estará activo en línea de manera oficial.
