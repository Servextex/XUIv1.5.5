# Documentación de XUIv1.5.5

## Descripción General

XUIv1.5.5 es un panel de administración que permite gestionar servicios de streaming. Este repositorio contiene los archivos necesarios para la instalación y configuración del sistema.

## Contenido del Repositorio

El repositorio contiene los siguientes archivos principales:

- **XUI_1.5.5.zip**: Archivo principal que contiene:
  - `database.sql` - Estructura de la base de datos
  - `install` - Script de instalación
  - `xui.tar.gz` - Paquete principal del sistema

- **crackedxui.zip**: Archivo con modificaciones para el sistema
- **install.txt**: Instrucciones básicas de instalación
- **installandinstructions.txt**: Guía completa de instalación y uso
- **stepsandtools.txt**: Comandos y herramientas de administración

## Guía de Instalación

### Requisitos Previos
- Sistema operativo: Ubuntu 20.04 (recomendado)
- Si tiene paneles antiguos (Xtream UI/Xtream Codes o StreamCreed), debe eliminarlos primero

### Pasos de Instalación

1. Descargar el archivo principal:
   ```bash
   wget https://repo.ottshow.net/app/XUI_1.5.5.zip -O /tmp/XUI_1.5.5.zip
   ```

2. Cambiar al directorio temporal:
   ```bash
   cd /tmp
   ```

3. Instalar herramientas necesarias y descomprimir:
   ```bash
   apt install zip unzip -y ; unzip XUI_1.5.5.zip
   ```

4. Ejecutar el script de instalación:
   ```bash
   ./install.sh
   ```

5. Descargar archivo adicional:
   ```bash
   wget https://repo.ottshow.net/app/crackedxui.zip
   ```

6. Descomprimir el archivo adicional:
   ```bash
   unzip crackedxui.zip
   ```

7. Dar permisos de ejecución:
   ```bash
   chmod 777 install.sh
   ```

8. Ejecutar la instalación final:
   ```bash
   ./install
   ```

## Comandos de Administración

### Gestión del Panel

- **Detener panel**:
  ```bash
  /home/xui/service stop
  ```

- **Iniciar panel**:
  ```bash
  /home/xui/service start
  ```

- **Actualizar base de datos**:
  ```bash
  /home/xui/status
  ```

### Herramientas de Mantenimiento

- **Listar herramientas disponibles**:
  ```bash
  /home/xui/tools
  ```

- **Crear código de acceso de rescate**:
  ```bash
  /home/xui/tools rescue
  ```

- **Crear usuario administrador de rescate**:
  ```bash
  /home/xui/tools user
  ```

- **Reautorizar balanceadores de carga en MySQL**:
  ```bash
  /home/xui/tools mysql
  ```

- **Restaurar una base de datos en blanco**:
  ```bash
  /home/xui/tools database
  ```

- **Limpiar base de datos de migración**:
  ```bash
  /home/xui/tools migration
  ```

- **Vaciar todas las IPs bloqueadas**:
  ```bash
  /home/xui/tools flush
  ```

- **Regenerar puertos desde MySQL**:
  ```bash
  /home/xui/tools ports
  ```

- **Regenerar código de acceso desde MySQL**:
  ```bash
  /home/xui/tools access
  ```

### Gestión de Copias de Seguridad

- **Generar copia de seguridad completa**:
  ```bash
  mysqldump -u root xui > xuiLT-backup.sql
  ```

- **Restaurar copia de seguridad seleccionada**:
  ```bash
  mysql -u root xui < path/backup/file.sql
  ```

## Proceso de Migración

1. Restaurar archivo SQL a la base de datos de migración:
   ```bash
   mysql xui_migrate < database.sql
   ```

2. Hay dos formas de iniciar el proceso de migración:

   - **En el panel**: Visite la URL y haga clic en "Migrate"
   - **En la consola**:
     ```bash
     /home/xui/bin/php/bin/php /home/xui/includes/cli/migrate.php
     ```
   - **En el navegador**: Visite `http://host.com:port/accesscode/setup`

## Solución de Problemas

### Restaurar Base de Datos en Blanco

Si algo sale mal, puede restaurar una base de datos XUI en blanco:
```bash
mysql xui < /home/xui/bin/install/database.sql
```

### Restablecer Usuario y Contraseña de Administrador

```bash
mysql
USE `xui`; REPLACE INTO `users`(`id`, `username`, `password`, `member_group_id`, `status`, `owner_id`, `date_registered`) VALUES(1, 'admin', '$6$rounds=20000$xui$eQfRsD2gsIUhoY5RnnYN82qiB5VeZTcHICQrFxXpa98J2R1454b6lzVHVjiJ.NP0gi0X3K7NXVgxeR1VhVhg61', 1, 1, 0, UNIX_TIMESTAMP());
```

### Corregir Error de Licencia

```bash
/home/xui/status
```

## Desinstalación de Paneles Antiguos

### Desinstalar Xtream UI (Xtream Codes)

```bash
sudo systemctl stop mysql.service
sudo apt purge mysql-server mysql-client mysql-common mysql-server-core-* mysql-client-core-* -y
sudo rm -rf /etc/mysql /var/lib/mysql /var/log/mysql 2>/dev/null
sudo apt autoremove -y
sudo apt autoclean -y
```

Eliminar archivos relacionados con Xtream UI:

```bash
sudo pkill -u xtreamcodes 2>/dev/null
sudo kill $(ps aux | grep '[p]hp' | awk '{print $2}') 2>/dev/null
sudo kill $(ps aux | grep '[n]ginx' | awk '{print $2}') 2>/dev/null
sudo chattr -i /home/xtreamcodes/iptv_xtream_codes/GeoLite2.mmdb 2>/dev/null
sudo rm -rf /home/xtreamcodes/iptv_xtream_codes/tmp/* 2>/dev/null
sudo rm -rf /home/xtreamcodes/iptv_xtream_codes/streams/* 2>/dev/null
sudo umount -f /home/xtreamcodes/iptv_xtream_codes/streams 2>/dev/null
sudo umount -f /home/xtreamcodes/iptv_xtream_codes/tmp 2>/dev/null
sudo sed -i '/xtreamcodes/d' /etc/fstab
sudo sed -i '/xtreamcodes/d' /etc/crontab
sudo sed -i '/xtreamcodes/d' /etc/sudoers
sudo deluser xtreamcodes 2>/dev/null
sudo groupdel xtreamcodes 2>/dev/null
sudo chown root:root -R /home/xtreamcodes 2>/dev/null
sudo chmod -R 0644 /home/xtreamcodes 2>/dev/null
sudo rm /var/lib/dpkg/lock-frontend /var/cache/apt/archives/lock /var/lib/dpkg/lock 2>/dev/null
sudo apt-get remove libcurl3 -y 2>/dev/null
```

### Eliminar StreamCreed Panel

```bash
sudo systemctl stop mariadb.service 2>/dev/null
sudo apt-get purge mariadb-server -y
sudo rm -rf /etc/mysql /var/lib/mysql /var/log/mysql 2>/dev/null
sudo apt autoremove -y
sudo apt autoclean -y
```

Eliminar archivos relacionados con StreamCreed:

```bash
sudo pkill -u streamcreed 2>/dev/null
sudo kill $(ps aux | grep '[p]hp' | awk '{print $2}') 2>/dev/null
sudo kill $(ps aux | grep '[n]ginx' | awk '{print $2}') 2>/dev/null
sudo rm -rf /home/streamcreed/tmp/* 2>/dev/null
sudo rm -rf /home/streamcreed/streams/* 2>/dev/null
sudo umount -f /home/streamcreed/streams 2>/dev/null
sudo umount -f /home/streamcreed/tmp 2>/dev/null
sudo sed -i '/streamcreed/d' /etc/fstab
sudo sed -i '/streamcreed/d' /etc/crontab
sudo sed -i '/streamcreed/d' /etc/sudoers
sudo deluser streamcreed 2>/dev/null
sudo groupdel streamcreed 2>/dev/null
sudo chown root:root -R /home/streamcreed 2>/dev/null
sudo chmod -R 0644 /home/streamcreed 2>/dev/null
```

## Notas Adicionales

- La documentación se ha creado basándose en los archivos disponibles en el repositorio de GitHub.
- Se recomienda seguir las instrucciones exactamente como están descritas para evitar problemas durante la instalación.
- Para mayor seguridad, es recomendable realizar una copia de seguridad completa antes de realizar cualquier actualización o modificación importante.
