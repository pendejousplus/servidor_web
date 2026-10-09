# AE2 — Servidor Web

Se creará un sitio web estático utilizando Zensical con servidor web Nginx.

Índice

- [Entorno e instalación](#entorno-e-instalacion)
- [Configuración](#configuracion)
- [Proyecto complementario: Jekyll](#proyecto-complementario-jekyll)
- [Comprobación](#comprobacion)
- [Problemas encontrados y solución](#problemas-encontrados-y-solucion)
- [Repositorio remoto](#repositorio-remoto)

## Entorno e instalación
Las herramientas necesarias para este proyecto son:

- Git para clonar el repositorio y revisar el historial.
- Python 3 y el entorno virtual `.venv` para ejecutar Zensical.
- NGINX para servir la web estática.
- Un navegador web para abrir la documentación final.

El repositorio base se encuentra en `pendejousplus/servidor_web` y se puede clonar con:

```bash
git clone git@github.com:pendejousplus/servidor_web.git
cd servidor_web
python3 -m venv .venv
. .venv/bin/activate
pip install zensical
```

Una vez instalado el generador, la documentación se compila con:

```bash
zensical build
```

La página de salida queda generada en la carpeta `site/`. Si se desea probarla localmente en un navegador, se puede abrir directamente `site/index.html` o servirse con NGINX usando la configuración del fichero `conf/nginx/zensical.conf`.

## Configuración
Se han tocado tres bloques fundamentales:

1. `zensical.toml`: define el nombre del sitio y los directorios de entrada y salida.
   ```toml
   site_name = "Documentación AE2.1 — Despliegue Estático con NGINX"
   docs_dir = "docs"
   site_dir = "site"
   ```

2. `docs/index.md`: contiene el contenido de la documentación en formato Markdown. Es la fuente principal del sitio.

3. `conf/nginx/zensical.conf`: prepara el servidor web para servir los archivos estáticos compilados desde `/var/www/zensical`.
   ```nginx
   server {
       listen 80;
       server_name localhost;

       root /var/www/zensical;
       index index.html index.htm;

       location / {
           try_files $uri $uri/ =404;
       }
   }
   ```

Además, la salida generada por Zensical se almacena en `site/` y está lista para desplegarse en un servidor HTTP estático. En esta práctica, se usa NGINX como servidor final de despliegue.

## Proyecto complementario: Jekyll
El repositorio también incluye un sitio personal generado con Jekyll y servido por NGINX en el puerto 8080. Su despliegue es independiente de la documentación de Zensical, que se sirve en el puerto 80. La guía de instalación, compilación y publicación está en [Despliegue de sitio personal con Jekyll](despliegue-jekyll.md).

## Comprobación
Se realizaron comprobaciones para verificar el estado del repositorio y la configuración del sitio. Los comandos principales fueron:

```bash
git --no-pager log --oneline --decorate --graph --all -n 20
git --no-pager remote -v
git --no-pager tag -n
```

Resultados relevantes:

- `git log` muestra la evolución del proyecto con estos hitos:
  - `6f79f93 Configuración inicial`
  - `f8cc450 Configuracion de archivos estáticos.`
  - `80658db Configuración final`
- `git remote -v` confirma el repositorio remoto:
  ```bash
  origin  git@github.com:pendejousplus/servidor_web.git (fetch)
  origin  git@github.com:pendejousplus/servidor_web.git (push)
  ```
- No se detectaron tags en el repositorio (`git tag -n` devolvió vacío), por lo que no hay versiones etiquetadas en este proyecto.

También se verificó que el contenido compilado quedaba generado correctamente en `site/`, y que la configuración de NGINX apunta a la ruta de despliegue adecuada.

## Problemas encontrados y solución

| Problema | Causa | Solución |
|---|---|---|
| La documentación no estaba desarrollada | El archivo `docs/index.md` tenía solo el esqueleto inicial con secciones vacías | Se rellenaron los apartados de instalación, configuración, comprobación y repositorio para documentar el proyecto completo |
| El sitio estaba sin una configuración de despliegue clara | No se indicaba bien la ruta raíz del servidor web ni el modo de servir los archivos estáticos | Se creó `conf/nginx/zensical.conf` con `root /var/www/zensical` y `try_files` para atender peticiones |
| Conflicto de fusión | Al combinar cambios de distintas versiones de la configuración, puede aparecer una mezcla del contenido final con la estructura inicial | Se resolvió manteniendo la versión definitiva del proyecto: la configuración de NGINX y la documentación de despliegue quedaron como la referencia final |

En resumen, el proyecto quedó documentado y configurado para que un sitio estático generado con Zensical pueda publicarse de forma sencilla mediante NGINX.

## Repositorio remoto
El repositorio público del proyecto es:

- https://github.com/pendejousplus/servidor_web

Este enlace permite acceder al código fuente y al historial del proyecto para futuras ampliaciones o correcciones.
