# comandos básicos
# windows
## básicos de red

| Comando                                                    | Para qué                                    |
| ---------------------------------------------------------- | ------------------------------------------- |
| **Test-NetConnection** *nombre_DC* **-Port** *port_number* | Comprobar si llegas a un puerto del DC      |
| **ipconfig /all**                                          | Ver IP, puerta de enlace y DNS configurados |
## dns

| Comando                               | Para qué                                                                    |
| ------------------------------------- | --------------------------------------------------------------------------- |
| **ipconfig /flushdns**                | Vaciar la caché DNS                                                         |
| **ipconfig /displaydns**              | Muestra la cache dns del equipo (Get-DnsClientCache)                        |
| **ipconfig /registerdns**             | Volver a registrar el equipo en DNS                                         |
| **nslookup** *dominio*                | Comprobar que el DNS resuelve el dominio                                    |
| **nslookup -type=SRV** *fqdn_dominio* | Comprobar que DNS anuncia el DC (ejemplo: *ldap.tcp.dc.msdcs.november.lab)* |
| **Resolve-DnsName** *dominio*         | Igual que nslookup, en PowerShell                                           |
| **dcdiag /test:dns**                  | Prueba DNS completa del DC                                                  |
## ntp

| Comando                                                                                                   | Para qué                                                                        |
| --------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| **w32tm /config /manualpeerlist:**"*domain1 domain2*" **/syncfromflags:manual** **/reliable:yes /update** | Sincroniza el dc con servidores externos (Ejemplo: *pool.ntp.org, hora.roa.es*) |
| **w32tm /query /status**                                                                                  | Fuente y estado de la sincronización                                            |
| **w32tm /resync**                                                                                         | Forzar la sincronización                                                        |
| **w32tm /resync /rediscover**                                                                             | Volver a detectar fuentes y sincronizar (tras cambiar la configuración)         |
## diagnóstico del dc

| Comando                        | Para qué                                |
| ------------------------------ | --------------------------------------- |
| **dcdiag /v**                  | Detalles completo de diagnóstico del DC |
| **dcdiag /q**                  | Solo muestra errores                    |
| **nltest /dsgetdc:** *dominio* | Ver qué DC localiza el equipo           |
| **nltest /dsregdns**           | Volver a registrar los SRV del DC       |
| **Get-SmbShare**               | Ver que existan SYSVOL y NETLOGON       |
# proxmox
## nodo

| Comando                                 | Qué hace                                          |
| --------------------------------------- | ------------------------------------------------- |
| **pveversion -v**                       | Versión de Proxmox y paquetes                     |
| **pvesh get /nodes**                    | Lista nodos y estado ram, cpu, online             |
| **apt update && apt full-upgrade -y**   | Actualizar el sistema (nunca apt upgrade a secas) |
| **systemctl status pveproxy pvedaemon** | Estado de la interfaz web y el daemon             |
| **systemctl restart pveproxy**          | Reiniciar la web (puerto 8006) si no carga        |
| **journalctl -xe**                      | Logs recientes del sistema                        |
| **pvereport**                           | Informe completo del nodo (diagnóstico)           |
| **pvesm status**                        | Almacenamientos y espacio libre                   |
| **pvesm list local**                    | Contenido de un almacenamiento                    |

## máquinas virtuales

| Comando                                            | Qué hace                                      |
| -------------------------------------------------- | --------------------------------------------- |
| **cat** */etc/pve/qemu-server/vmid.conf*           | Config de cada VM                             |
| **qm list**                                        | Lista VMs con estado                          |
| **qm start** *vmid*                                | Arrancar                                      |
| **qm shutdown** *vmid*                             | Apagado ordenado (ACPI)                       |
| **qm stop** *vmid*                                 | Apagado forzado (como quitar corriente)       |
| **qm reboot** *vmid*                               | Reiniciar                                     |
| **qm status** *vmid*                               | Estado de una VM                              |
| **qm config** *vmid*                               | Ver configuración (CPU, RAM, discos, red)     |
| **qm set** *vmid* **--memory 4096**                | Cambiar RAM (MB)                              |
| **qm set** *vmid* **--cores 2**                    | Cambiar núcleos                               |
| **qm set** *vmid* **--onboot 1**                   | Arranque automático con el nodo               |
| **qm clone** *vmid* *nuevo_id* **--name** *nombre* | Clonar VM                                     |
| **qm template** *vmid*                             | Convertir en plantilla                        |
| **qm destroy** *vmid* **--purge**                  | Borrar VM y sus discos                        |
| **qm unlock** *vmid*                               | Quitar bloqueo (tras backup/snapshot fallido) |
| **qm terminal** *vmid*                             | Consola serie (si está configurada)           |
## snapshots

| Comando                                   | Qué hace             |
| ----------------------------------------- | -------------------- |
| **qm snapshot** *vmid* *snapshot_name*    | Crear snapshot       |
| **qm listsnapshot** *vmid*                | Listar snapshots     |
| **qm rollback** *vmid* *snapshot_name*    | Volver a un snapshot |
| **qm delsnapshot** *vmid* *snapshot_name* | Borrar snapshot      |
## backups y restauración

| Comando                                                               | Qué hace             |
| --------------------------------------------------------------------- | -------------------- |
| **vzdump** *vmid* **--storage local --mode snapshot --compress zstd** | Backup en caliente   |
| **vzdump --all --storage local**                                      | Backup de todo       |
| **qmrestore** *archivo.vma.zst* *vmid*                                | Restaurar VM         |



