# Koha-Upacifico

Proyecto de configuracion y despliegue de Koha desarrollado por **Isaac Esteban Haro Torres**.

---

## Descripcion

Guia completa para instalar y levantar Koha en un servidor Ubuntu 24.04 LTS.

Versiones validadas al 2 de marzo de 2026.

---

## Descargas Oficiales

- Sitio oficial Koha (descarga): https://koha-community.org/download-koha/
- Repositorio oficial APT Koha: https://debian.koha-community.org/koha/
- Wiki oficial para instalacion en Debian/Ubuntu: https://wiki.koha-community.org/wiki/Debian

---

## Caracteristicas

- Sistema integrado de gestion bibliotecaria (ILS)
- Catalogacion MARC21
- Gestion de prestamos y usuarios
- OPAC web para consultas publicas
- Soporte para Z39.50

---

## Stack Tecnologico

- Koha (rama estable oficial)
- MariaDB
- Apache2
- Zebra o Elasticsearch (segun configuracion)
- Perl

---

## Requisitos del Servidor

- Ubuntu Server 22.04 LTS
- 2 vCPU minimo
- 4 GB RAM minimo
- 40 GB de disco recomendado
- Dominio o subdominio apuntando al servidor

---

## Instalacion Paso a Paso (Servidor)

### 1. Actualizar el sistema

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y gnupg curl wget software-properties-common
```

### 2. Agregar repositorio oficial de Koha

```bash
echo 'deb http://debian.koha-community.org/koha stable main' | sudo tee /etc/apt/sources.list.d/koha.list
wget -q -O- https://debian.koha-community.org/koha/gpg.asc | sudo gpg --dearmor -o /usr/share/keyrings/koha.gpg
echo 'deb [signed-by=/usr/share/keyrings/koha.gpg] http://debian.koha-community.org/koha stable main' | sudo tee /etc/apt/sources.list.d/koha.list
sudo apt update
```

### 3. Instalar Koha

```bash
sudo apt install -y koha-common
koha-common --version
```

### 4. Configurar nombre de dominio de la instancia

Editar:

```bash
sudo nano /etc/koha/koha-sites.conf
```

Ajustar:

```ini
DOMAIN="biblioteca.tudominio.com"
INTRAPORT="8080"
INTRAPREFIX=""
OPACPORT="80"
```

### 5. Crear instancia Koha

```bash
sudo koha-create --create-db upacifico
```

### 6. Habilitar modulos web

```bash
sudo a2enmod rewrite
sudo a2enmod cgi
sudo koha-enable upacifico
sudo systemctl restart apache2
```

### 7. Obtener credenciales iniciales

```bash
sudo cat /etc/koha/sites/upacifico/koha-conf.xml | grep -E "<user>|<pass>"
sudo koha-passwd upacifico bibliotecario
```

### 8. Inicializar servicios de indexacion

```bash
sudo koha-start-zebra upacifico
sudo koha-rebuild-zebra -v -f upacifico
```

### 9. Levantar y verificar servicios

```bash
sudo systemctl restart apache2
sudo systemctl restart mariadb
sudo systemctl status apache2
sudo systemctl status mariadb
```

URLs esperadas:

- Staff (intranet): `http://upacifico.biblioteca.tudominio.com:8080`
- OPAC: `http://upacifico.biblioteca.tudominio.com`

### 10. Habilitar HTTPS con Let's Encrypt

```bash
sudo apt install -y certbot python3-certbot-apache
sudo certbot --apache -d upacifico.biblioteca.tudominio.com
```

---

## Post Instalacion Recomendado

- Crear categorias de usuarios
- Configurar reglas de circulacion
- Cargar framework MARC
- Configurar respaldos diarios de MariaDB

Respaldo rapido:

```bash
sudo mysqldump -u root -p koha_upacifico > /var/backups/koha_upacifico.sql
```

---

## Comandos de Operacion

- Iniciar Koha: `sudo koha-start upacifico`
- Detener Koha: `sudo koha-stop upacifico`
- Reiniciar Apache: `sudo systemctl restart apache2`
- Reindexar Zebra: `sudo koha-rebuild-zebra -v -f upacifico`

---

## Desarrollado por Isaac Esteban Haro Torres

**Ingeniero en Sistemas | Full Stack | Automatizacion | Data**

- Email: zackharo1@gmail.com
- WhatsApp: 098805517
- GitHub: https://github.com/ieharo1
- Portafolio: https://ieharo1.github.io/portafolio-isaac.haro/

---

Copyright 2026 Isaac Esteban Haro Torres - Todos los derechos reservados.




