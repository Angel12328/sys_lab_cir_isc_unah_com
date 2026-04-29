# 🚀 Sistema de Gestión Odoo 18 - Laboratorio de Circuitos ISC (UNAH COMAYAGUA)

Este repositorio contiene la configuración completa para desplegar la instancia de Odoo 18 personalizada para el inventario del laboratorio. Está optimizado para ejecutarse con **Podman** en entornos Linux (Fedora/Debian/Ubuntu).

## 📋 Requisitos Previos en el Servidor (Proxmox)
Asegúrate de que la VM tenga instalado el motor de contenedores:
```bash
sudo dnf install podman podman-compose -y  # En Fedora/RHEL
# O bien:
sudo apt install podman podman-compose -y  # En Debian/Ubuntu


📂 Credenciales de Infraestructura (Importante)

Estos datos están configurados en el compose.yaml y son necesarios para la conexión interna:
Servicio	Usuario	Contraseña	Base de Datos
PostgreSQL	odoo	odoo_pass	postgres
Odoo (App)	odoo	odoo_pass	(Se restaura del backup)

Master Password de Odoo: dnrp-f8ws-cdfm
(Necesario para restaurar el backup y gestionar bases de datos).

🛠️ Pasos para el Despliegue

Clonar el proyecto:

git clone https://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@github.com/Angel12328/sys_lab_cir_isc_unah_com.git sys_lab_cir_isc_unah_com 
cd sys_lab_cir_isc_unah_com

# Crear las carpetas de datos
mkdir -p ~/odoo-docker/odoo_data ~/odoo-docker/postgres_data

# Dar permisos totales (Solución para rootless)
chmod -R 777 ~/odoo-docker/odoo_data ~/odoo-docker/postgres_data

# Aplicar el contexto de seguridad de SELinux (Vital en Fedora)
sudo chcon -Rt svirt_sandbox_file_t ~/odoo-docker/odoo_data
sudo chcon -Rt svirt_sandbox_file_t ~/odoo-docker/postgres_data

Levantar los servicios:
Ejecuta el siguiente comando para iniciar la base de datos y Odoo en segundo plano:

podman-compose up -d

Verificar que todo esté corriendo:

podman ps

Deberías ver los contenedores odoo-app y odoo-db activos.

💾 Restauración de Datos (IMPORTANTE)

Para recuperar todas las confirmaciones y registros actuales, sigue estos pasos:

    Abre en el navegador: http://[IP_DEL_SERVIDOR]:8069/web/database/manager.

    Haz clic en Restore Database.

    Cargar archivo: Selecciona el archivo .zip  (el backup que incluye el filestore).

    Master Password: Utiliza el token de seguridad: el_que_te_da_odoo

    Nombre de la DB: Puedes usar is_unah_com_lab_cir o el que prefieras.

    Selecciona la opción "This database is a copy" para finalizar.

📁 Notas de Configuración

    Puerto: El sistema escucha en el puerto 8069.

    Credenciales de DB: Definidas en el compose.yaml (Usuario: odoo / Pass: odoo_pass).

    Módulos: Cualquier módulo extra debe ir en la carpeta extra-addons/.



🔧 Solución de Problemas (Troubleshooting)
Permisos de Carpeta (SELinux en Fedora)

Si los contenedores no pueden escribir en las carpetas locales, ejecuta esto en la carpeta del proyecto:
Bash

chcon -Rt svirt_sandbox_file_t ./config ./odoo_data ./extra-addons ./postgres_data

Limpieza de Contenedores con Errores

Si algo sale mal, este comando borra los contenedores "trabados" para empezar de cero.

# Detener y borrar contenedores y redes del proyecto
podman-compose down

# Borrar contenedores por nombre si quedaron huérfanos
podman rm -f odoo-app odoo-db

Reparación Manual de la Base de Datos (SQL)

Si el Manager de Odoo da "Internal Server Error", entramos a Postgres para borrar la base de datos corrupta:

# Entrar al prompt de Postgres
podman exec -it odoo-db psql -U odoo -d postgres

Dentro de Postgres (copia y pega uno por uno):

-- 1. Bloquear nuevas conexiones a la base dañada
ALTER DATABASE "is_unah_com_lab_cir2" WITH ALLOW_CONNECTIONS = false;

-- 2. Expulsar a los usuarios conectados (Odoo)
SELECT pg_terminate_backend(pid) FROM pg_stat_activity 
WHERE datname = 'is_unah_com_lab_cir2' AND pid <> pg_backend_pid();

-- 3. Borrar la base de datos
DROP DATABASE "is_unah_com_lab_cir2";

-- 4. Salir
\q

Lanzamiento y Monitoreo

# Levantar los contenedores en segundo plano
podman-compose up -d

# Ver los logs en tiempo real para detectar errores de carga
podman-compose logs -f


