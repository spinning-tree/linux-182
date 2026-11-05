# proxmox-182


- **DNS y DHCP** en el DC, y unir al dominio los clientes Windows 11.
- **OUs, usuarios y grupos**, y tareas típicas de soporte: restablecer contraseñas, desbloquear cuentas y deshabilitar bajas.
- **GPOs**: política de contraseñas, unidades de red mapeadas, fondo de pantalla e impresoras.
- **Servidor de ficheros**: carpetas compartidas con permisos NTFS por grupos. Es una de las tareas más frecuentes en soporte.
- **RSAT** en un cliente Windows 11, para administrar el dominio sin entrar al DC.
- **Unir `deb01` al dominio** con `realmd`/`sssd`, para practicar entornos mixtos.
- **Backups** de las VMs en Proxmox y una restauración de prueba.
- **GLPI**: gestión de tickets e inventario, muy usado en España, sobre todo en sector público y consultoras. Puedes montarlo en un Debian clonado de tu plantilla y gestionar con él las incidencias de tu propio lab.
- **Monitorización** con Zabbix o Uptime Kuma: alertas cuando cae un servicio o se llena un disco.
- **Scripts de PowerShell** para dar de alta usuarios desde un CSV o generar informes de cuentas inactivas. Automatizar tareas es lo que separa un N1 de un N2.
- **Windows LAPS**: contraseñas únicas del administrador local en cada equipo, gestionadas desde AD.
- **Despliegue de equipos**: WDS para instalar Windows por red (PXE).
- **Segmentación con OPNsense**: VLANs de clientes y servidores con reglas de firewall entre ellas.
- **VPN** con WireGuard, para entrar al lab desde fuera de casa.


Luego Microsoft 365
Luego Jira
Luego Linux
