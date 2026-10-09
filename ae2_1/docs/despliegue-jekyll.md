# Despliegue de sitio personal con Jekyll y NGINX

## 1. Introducción y objetivo

Como complemento a la documentación de AE2.1, el repositorio contiene un segundo proyecto: un sitio personal tipo currículum generado con **Jekyll** y servido por **NGINX** en el puerto **8080**. La documentación de Zensical continúa sirviéndose por separado en el puerto 80.

El código fuente del sitio está en `ae2_2/cv-site/`. Jekyll genera sus archivos estáticos en `_site/`, que NGINX publica desde `/var/www/jekyll-cv`, según la configuración `ae2_2/conf/nginx/jekyll.conf`.

## 2. Instalación de dependencias

En Ubuntu se necesitan Ruby, Bundler y las herramientas de compilación de gemas:

```bash
sudo apt update
sudo apt install -y ruby-full build-essential zlib1g-dev
```

Configura las gemas en el entorno del usuario y añade la configuración a `~/.bashrc` para futuras terminales:

```bash
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
gem install bundler
```

El `Gemfile` del proyecto define Jekyll 4.4 y el tema Minima. Desde el directorio del sitio, instala las versiones fijadas por `Gemfile.lock`:

```bash
cd ae2_2/cv-site
bundle install
```

## 3. Compilación y prueba local

Para compilar el sitio y generar los archivos estáticos en `ae2_2/cv-site/_site/`:

```bash
bundle exec jekyll build
```

Para previsualizarlo durante el desarrollo, Jekyll inicia un servidor local —por defecto, en el puerto 4000— y reconstruye el contenido al detectar cambios:

```bash
bundle exec jekyll serve
```

El contenido principal se edita en `index.markdown`; los artículos se guardan en `_posts/` y deben usar el formato `AAAA-MM-DD-titulo.markdown`, con metadatos *front matter* al inicio. La configuración general del sitio está en `_config.yml`.

## 4. Publicación con NGINX

La configuración `ae2_2/conf/nginx/jekyll.conf` define un servidor que escucha en el puerto 8080, usa `/var/www/jekyll-cv` como raíz y sirve `index.html`. También deniega el acceso a archivos y directorios ocultos y configura la página 404.

Después de compilar el sitio, copia el resultado al directorio publicado:

```bash
sudo install -d /var/www/jekyll-cv
sudo cp -a _site/. /var/www/jekyll-cv/
```

Instala y habilita la configuración de NGINX (crea el enlace solo si todavía no existe):

```bash
sudo cp ../conf/nginx/jekyll.conf /etc/nginx/sites-available/jekyll
sudo ln -s /etc/nginx/sites-available/jekyll /etc/nginx/sites-enabled/jekyll
sudo nginx -t
sudo systemctl reload nginx
```

Ejecuta estos comandos desde `ae2_2/cv-site`. Si la configuración ya está habilitada, no repitas el comando `ln -s`. Para comprobar el sitio en el servidor:

```bash
curl -I http://localhost:8080/
```

También se puede abrir `http://localhost:8080/` en un navegador. Si NGINX está en otra máquina, sustituye `localhost` por el nombre o la dirección de ese servidor.

## 5. Comprobación y problemas habituales

| Problema | Comprobación o solución |
|---|---|
| Bundler no encuentra las dependencias | Ejecuta `bundle install` dentro de `ae2_2/cv-site` y usa `bundle exec` para los comandos de Jekyll |
| El sitio muestra contenido antiguo | Vuelve a compilar con `bundle exec jekyll build` y copia el nuevo contenido de `_site/` a `/var/www/jekyll-cv/` |
| NGINX no inicia o no carga la página | Valida la configuración con `sudo nginx -t` y consulta el estado con `sudo systemctl status nginx` |
| El puerto 8080 no responde | Confirma que la configuración de Jekyll esté habilitada, que NGINX esté activo y que el puerto esté permitido por el cortafuegos |
| Se obtiene un error 404 | Comprueba que exista `/var/www/jekyll-cv/index.html` y que la raíz configurada en `jekyll.conf` coincida con el directorio publicado |

En resumen, Jekyll compila el currículum como un sitio estático y NGINX lo sirve en el puerto 8080, manteniéndolo independiente de la documentación Zensical del puerto 80.
