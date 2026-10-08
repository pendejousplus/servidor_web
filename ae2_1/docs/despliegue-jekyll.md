# Despliegue de Sitio Personal / CV con Jekyll en NGINX

## 1. Introducción y Objetivo
Como ampliación de la práctica para la máxima calificación, se ha configurado un segundo servidor virtual (*virtual host*) en NGINX para alojar una web estática generada con el SSG **Jekyll** (https://jekyllrb.com), escuchando de forma independiente en el puerto **8080**.

---

## 2. Instalación de Dependencias (Ruby y Jekyll)
Jekyll requiere el entorno de ejecución de Ruby y dependencias de desarrollo para compilar gemas nativas:

```bash
# 1. Instalación de paquetes del sistema
sudo apt update && sudo apt install -y ruby-full build-essential zlib1g-dev

# 2. Configuración de gemas en el entorno del usuario (sin permisos de superusuario)
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# 3. Instalación de Jekyll y Bundler
gem install jekyll bundler
