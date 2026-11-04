# instalación de proxmox

2. Descarga la ISO más reciente de Proxmox VE desde [https://www.proxmox.com/en/downloads](https://www.proxmox.com/en/downloads)

3. Descarga Balena Etcher

> Ojo: Balena Etcher necesita permisos de admin. Ejecútalo desde terminal: `sudo /Applications/balenaEtcher.app/Contents/MacOS/balenaEtcher`

4. En el target pc, booteas desde el usb y eliges **Install Proxmox VE (Graphical)**. A partir de ahí es todo siguiente -> siguiente (región, idioma, contraseña, etc.)

![](https://i.imgur.com/rXmUH4k.jpg)

5. En la sección de red, pones un FQDN cualquier (*hostname.domain_name*) y en la ip cualquiera que no esté en el rango dhcp del router ISP, gateway el del ISP y el servidor DNS. Luego -> Instalar

> El equipo proxmox necesita estar conectado por cable al router ISP

![](https://i.imgur.com/q6lTMZy.jpg)

6. Al reiniciar, mostrará pantalla de login. Ya puedes dejar el t480 cerrado y gestionarlo remoto.

> Ojo: en el t480, al bajar la tapa el equipo entra en suspensión. Modifica el `/etc/systemd/logind.conf.d` y todo lo que ponga `HandleLidSwitch` lo pones en ignore. Luego reinicias el proceso con `systemctl restart systemd-logind`

7. Ahora toca desactivar las suscripciones de proxmox (parches de seguridad, corrección de errores, etc.). En la vm -> updates -> repositories, desactiva la ceph y la pve (que son las oficiales de proxmox y hay que pagar) y añades un repo `No-Subscription` para recibir las actualizaciones

> Ambos repos reciben lo mismo; el No-Subscription lo recibe antes y el enterprise, después de más pruebas.


![](https://i.imgur.com/BZYAczW.png)

8. Ahora, desde ssh en el pc de gestión ejecutas `sudo apt update && sudo apto full-upgrade -y`
9. Ya puedes [instalar windows](instalar-windows-proxmox) o [instalar linux](instalar-linux-proxmox)

## setear threshold de batería

1. verifica primero el threshold

```
root@november:~# cat /sys/class/power_supply/BAT0/charge_control_start_threshold 
root@november:~# cat /sys/class/power_supply/BAT0/charge_control_end_threshold 
root@november:~# cat /sys/class/power_supply/BAT1/charge_control_start_threshold 
root@november:~# cat /sys/class/power_supply/BAT1/charge_control_end_threshold 
```

2. configura los valores deseados

```
root@november:~# echo 75 > /sys/class/power_supply/BAT0/charge_control_start_threshold
root@november:~# echo 80 > /sys/class/power_supply/BAT0/charge_control_end_threshold
root@november:~# echo 75 > /sys/class/power_supply/BAT1/charge_control_start_threshold
root@november:~# echo 80 > /sys/class/power_supply/BAT1/charge_control_end_threshold
```

> No queda demostrado que estos valores se conserven si el euiqpo se reinicia.

