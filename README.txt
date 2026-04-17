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
🚀 Fase 1 — Un sol node web
Objectiu: Crear una pàgina web bàsica i servir-la amb un únic contenidor Apache.

Què hem fet: Hem definit un servei apache1 al fitxer docker-compose.yml utilitzant la imatge oficial httpd:latest. Hem creat una carpeta web/ amb un fitxer index.html que inclou text, imatges i un vídeo de YouTube.

Configuració clau: Mapeig del port 8080:80.

🚀 Fase 2 — Segon node web
Objectiu: Afegir un segon contenidor Apache i distingir quin node respon.

Què hem fet: Hem duplicat el servei al fitxer compose (apache2). Per poder distingir-los, hem creat dues carpetes separades (web1/ i web2/).

Distinció: * Node 1: Té un títol en vermell que diu "SOY APACHE 1".

Node 2: Té un títol en blau que diu "SOY APACHE 2".

Ports: El primer respon al port 8080 i el segon al 8081.

🚀 Fase 3 — Volum compartit
Objectiu: Ambdós nodes serveixen exactament el mateix contingut.

Què hem fet: En lloc d'apuntar a carpetes diferents, hem configurat el docker-compose.yml perquè ambdós serveis apuntin a la mateixa carpeta local ./web.

Resultat: Qualsevol canvi fet al fitxer index.html es reflecteix instantàniament en ambdós servidors, garantint la consistència de les dades.

🚀 Fase 4 — Proxy invers amb balanceig
Objectiu: Afegir Nginx com a punt d'entrada únic que distribueixi les peticions (Round Robin).

Què hem fet: Hem afegit un contenidor nginx que actua com a frontal. Hem eliminat l'accés públic directe als Apaches (ara usen expose en lloc de ports) perquè tot el trànsit passi obligatòriament per Nginx.

Lògica de balanç: Hem configurat un bloc upstream al fitxer nginx.conf.

Funcionament: En recarregar la pàgina a http://localhost:8080, Nginx alterna les peticions entre apache1 i apache2.

🚀 Fase 5 — Memòria cau (Cache)
Objectiu: Configurar el proxy cache a Nginx i verificar el seu funcionament amb capçaleres.

Què hem fet: Hem definit una ruta de cache (/var/cache/nginx) i hem configurat les directives proxy_cache.

Verificació (HIT/MISS): Hem afegit una capçalera personalitzada per poder veure des de l'inspector del navegador si la resposta prové del servidor real o de la memòria cau.

Configuració en nginx.conf:

Nginx
add_header X-Cache-Status $upstream_cache_status;
🛠 Problemes trobats i solucions
Error de muntatge (Not a directory): Docker intentava muntar nginx.conf com si fos una carpeta.

Solució: Esborrar la carpeta creada per error i crear el fitxer manualment abans d'executar docker compose up.

Persistent Sessions: El navegador mantenia la connexió amb un sol node i no es veia el balanceig.

Solució: Provar en mode incògnit o forçar la recàrrega amb Ctrl + F5.

Versió de Compose obsoleta: Apareixia un avís sobre la versió del fitxer YAML.

Solució: Eliminar la línia version: "3.9", ja que les versions actuals de Docker ja no la requereixen.

📸 Captures de pantalla demostratives
Nota per a l'alumne: Aquí hauries d'adjuntar les teves captures reals.

Funcionament Node 1: Pantalla amb el text en vermell (localhost:8080).

Funcionament Node 2: Pantalla amb el text en blau (després de fer refresh).

Estat dels contenidors: Execució de docker ps mostrant els 3 contenidors corrent.

Verificació de Cache: Imatge de la pestanya "Network" de l'inspector (F12) on es vegi la capçalera X-Cache-Status: HIT.

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
