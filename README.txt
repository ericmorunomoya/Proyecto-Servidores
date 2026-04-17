Aquí tienes la documentación completa en formato README.md, estructurada exactamente por las fases que mencionas. He adaptado el contenido para que refleje fielmente el proceso técnico que hemos seguido, incluyendo la configuración de las cabeceras de cache para la última fase.

Documentació Pràctica: Infraestructura Web amb Docker, Nginx i Apache
Aquest document detalla el procés de creació d'un sistema d'alta disponibilitat utilitzant contenidors, des d'un servidor bàsic fins a un sistema amb balanç d'assignació i memòria cau.

📁 Estructura del Projecte
Plaintext
proyecto-apache/
├── docker-compose.yml    # Orquestració dels contenidors
├── nginx.conf           # Configuració del Proxy i Balanç de càrrega
├── web1/                # Contingut del Node 1
│   └── index.html
└── web2/                # Contingut del Node 2
    └── index.html
¡Perfecto! He integrado las 4 capturas que tienes en la carpeta Fotos dentro de la sección correspondiente del README.md. He usado una tabla para que los Logs y las Fotos de cada Apache queden emparejados y se vea súper profesional.

Aquí tienes el código completo del README.md listo para copiar, pegar y subir a GitHub:

Markdown
# Documentación Práctica: Infraestructura Web con Docker, Nginx y Apache

He diseñado y desplegado una arquitectura web profesional utilizando contenedores, pasando de un servidor simple a un sistema robusto con balanceo de carga y gestión de caché.

## 📁 Estructura del Proyecto
```plaintext
proyecto-apache/
├── docker-compose.yml    # Orquestación de los contenedores
├── nginx.conf           # Configuración del Proxy y Balanceo de carga
├── web/                 # Carpeta compartida (Volumen)
│   ├── index.html       # Mi página web principal
│   └── videoplayback.mp4 # Mi vídeo local
└── Fotos/               # Capturas de pantalla para la documentación

🚀 Fase 1 y 2 — Los nodos
Objetivo: Crear dos página web básica y servirla con dos contenedor Apache.
Qué he hecho: Definí dos servicio apache1 y apache2 en el archivo docker-compose.yml. Creé la carpeta web/ con el archivo index.html.
Configuración clave: Mapeo del puerto 8080:80.

🚀 Fase 3 — Volumen compartido
Objetivo: Que ambos nodos sirvan exactamente el mismo contenido.
Qué he hecho: Configuré el docker-compose.yml para que ambos servicios apunten a la carpeta local ./web. Así, cualquier cambio en el HTML se refleja en ambos.

🚀 Fase 4 — Proxy inverso con balanceo
Objetivo: Añadir Nginx como punto de entrada único que distribuya las peticiones (Round Robin).
Qué he hecho: Añadí Nginx como frontal. Eliminé los puertos públicos de los Apaches para que todo el tráfico pase obligatoriamente por el Proxy.
Lógica de balanceo: Configuré el bloque upstream en nginx.conf. He usado un script en el HTML para detectar visualmente qué nodo responde.

🚀 Fase 5 — Memoria caché (Cache)
Objetivo: Configurar el proxy caché en Nginx y verificar su funcionamiento.
Qué he hecho: Definí la ruta de caché y las directivas proxy_cache. Añadí la cabecera X-Cache para ver el estado (HIT/MISS) desde el navegador.

📜 Fitxer Docker Compose Final
YAML
services:
  apache1:
    image: httpd:latest
    container_name: apache1
    volumes:
      - ./web1:/usr/local/apache2/htdocs/
    expose:
      - "80"
    restart: unless-stopped

  apache2:
    image: httpd:latest
    container_name: apache2
    volumes:
      - ./web2:/usr/local/apache2/htdocs/
    expose:
      - "80"
    restart: unless-stopped

  nginx:
    image: nginx:latest
    container_name: nginx-proxy
    ports:
      - "8080:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - nginx_cache:/var/cache/nginx
    depends_on:
      - apache1
      - apache2

volumes:
  nginx_cache:

Capturas de funcionamiento 

https://github.com/ericmorunomoya/Proyecto-Servidores/blob/main/Fotos/Apache%201-Foto.png

https://github.com/ericmorunomoya/Proyecto-Servidores/blob/main/Fotos/Apache%201-Log.png

https://github.com/ericmorunomoya/Proyecto-Servidores/blob/main/Fotos/Apache%202-Foto.png

https://github.com/ericmorunomoya/Proyecto-Servidores/blob/main/Fotos/Apache%202-Log.png
