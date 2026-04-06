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

git clone [https://github.com/Angel12328/sys_lab_cir_isc_unah_com.git](https://github.com/Angel12328/sys_lab_cir_isc_unah_com.git)
cd sys_lab_cir_isc_unah_com

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

    Cargar archivo: Selecciona el archivo .zip que te envié (el backup que incluye el filestore).

    Master Password: Utiliza el token de seguridad: dnrp-f8ws-cdfm

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

chcon -Rt svirt_sandbox_file_t ./config ./data ./extra-addons



