# instalación de windows server

> Previamente hay que [instalar proxmox](instalacion-proxmox.md)

## instalación de windows server

1. La instalación de windows server sigue los mismos pasos que para [instalar windows](instalacion-windows-proxmox.md) 

> OJO: si hemos creado el bridge vmbr1, lo seleccionamos en la pestaña Network. El resto de pasos son los mismos.


![](https://imgur.com/Ac4TDWb.png)

2. Arrancamos la VM. Siguiente -> Siguiente -> Elegir Windows Server Desktop Experience
3. En la pestaña de discos aparecerá vacío (lo normal, hay que cargar los drivers para que windows vea los discos). `Load Driver -> Browse -> CD Drive virtio-win -> vioscsi -> 2k25 -> amd64`

> Cargamos también los drivers de red: `CD Drive virtio-win -> NetKVM -> 2k25 -> amd64`


![](https://imgur.com/DhDtKuF.png)

4. Instalar

![](https://imgur.com/weyPGDG.png)

5. Configuramos una contraseña e iniciamos sesión

> En la pestaña de la consola tenemos un acceso directo para `ctrl + alt + del`

6. En el escritorio, abrimos el CD e instalamos virtio-win-guest-tools

![](https://imgur.com/4OEoBgP.png)

7. Ahora convertimos a plantilla. En CMD:

```cmd
`C:\Windows\System32\Sysprep\sysprep.exe /generalize /oobe /shutdown`.
```

> Antes de ejecutar este comando configurar windows update, timezone, keyaboard, rdp, etc.

![](https://imgur.com/MqtlHw2.png)

8. Finalmente, click derecho en la vm -> Convert to Template
9. Clonamos la plantilla, le damos un nombre y elegimos Full/linked clone
10. Lo siguiente es [configurar el servidor](configuracion-basica-windows-server.md)