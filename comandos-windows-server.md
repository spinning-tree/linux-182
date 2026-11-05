# Comandos para windows server

## Básicos de red

| Comando                                                    | Para qué                               |
| ---------------------------------------------------------- | -------------------------------------- |
| **Test-NetConnection** *nombre_DC* **-Port** *port_number* | Comprobar si llegas a un puerto del DC |
## DNS

| Comando                               | Para qué                                                                     |
| ------------------------------------- | ---------------------------------------------------------------------------- |
| **ipconfig /all**                     | Ver IP, puerta de enlace y DNS configurados                                  |
| **ipconfig /flushdns**                | Vaciar la caché DNS                                                          |
| **ipconfig /displaydns**              | Muestra la cache dns del equipo (Get-DnsClientCache)                         |
| **ipconfig /registerdns**             | Volver a registrar el equipo en DNS                                          |
| **nslookup** *dominio*                | Comprobar que el DNS resuelve el dominio                                     |
| **nslookup -type=SRV** *fqdn_dominio* | Comprobar que DNS anuncia el DC (ejemplo: `ldap.tcp.dc._msdcs.november.lab`) |
| **Resolve-DnsName** *dominio*         | Igual que nslookup, en PowerShell                                            |
| **dcdiag /test:dns**                  | Prueba DNS completa del DC                                                   |
## NTP

| Comando                                                                                                   | Para qué                                                                          |
| --------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| **w32tm /config /manualpeerlist:**"*domain1 domain2*" **/syncfromflags:manual** **/reliable:yes /update** | Sincroniza el dc con servidores externos (Ejemplo: `pool.ntp.org`, `hora.roa.es`) |
| **w32tm /query /status**                                                                                  | Fuente y estado de la sincronización                                              |
| **w32tm /resync**                                                                                         | Forzar la sincronización                                                          |
| **w32tm /resync /rediscover**                                                                             | Volver a detectar fuentes y sincronizar (tras cambiar la configuración)           |
## Diagnóstico del DC

| Comando                        | Para qué                                |
| ------------------------------ | --------------------------------------- |
| **dcdiag /v**                  | Detalles completo de diagnóstico del DC |
| **dcdiag /q**                  | Solo muestra errores                    |
| **nltest /dsgetdc:** *dominio* | Ver qué DC localiza el equipo           |
| **nltest /dsregdns**           | Volver a registrar los SRV del DC       |
| **Get-SmbShare**               | Ver que existan SYSVOL y NETLOGON       |
