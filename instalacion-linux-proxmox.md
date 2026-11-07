# instalar linux en proxmox

1. Abrimos la consola de proxmox y traemos un qcow2 de debian desde sus servidores

```shell
wget https://cloud.debian.org/images/cloud/trixie/latest/debian-13-genericcloud-amd64.qcow2
```

2. Ahora descargamos **libguestfs-tools**, un paquete de herramientas que nos permitirá "inyectarle" el qemu agent (para que proxmox se comunique con la vm) al qcow2 antes de convertirlo en plantilla

```shell
apt install -y libguestfs-tools
virt-customize -a debian-13-genericcloud-amd64.qcow2 --install qemu-guest-agent --truncate /etc/machine-id
```

3. Creamos la VM:
	- **General:** VM ID `9000`, Name `debian13-tpl`
	- **OS:** _Do not use any media_, Type _Linux_
	- **System:** Qemu Agent marcado, SCSI Controller _VirtIO SCSI single_ (el resto por defecto)
	- **Disks:** borra el disco (el disco será el propio qcow2)
	- **CPU:** 1 core, Type _host_ (la vm ve la cpu del equipo directamente y logra mayor rendimiento)
	- **Memory:** 1024
	- **Network:** Bridge `vmbr1`, Model _VirtIO_, Firewall desmarcado
	- **Confirm:** _Finish_ sin arrancar


![](https://imgur.com/lh4Lw66.png)


4. Creada la VM vacía sin disco, ahora tocar mapear esa vm al qcow2 de debian. Desde la consola de proxmox, ejecutamos

```bash
qm disk import 9000 /root/debian-13-genericcloud-amd64.qcow2 local-lvm
```

5. Aparecerá un unused disk 0 en la pestaña VM -> Hardware -> Unused Disk 0. Lo configuramos con estos valores:
	- **Bus/Device:** SCSI 0 (el puerto donde se conecta el disco)
	- **Discard:** marcado (cuando debian borra ficheros, avisa al host para que recupere ese espacio)
	- **IO thread:** marcado (gana rendimiento)
	- **SSD emulation:** marcado (en _Advanced_) para que la vm vea un disco ssd


[![](https://imgur.com/XHXPwqb.png)


> Aquí conviene aumentar el tamaño por defecto del disco (3gb) para contar con espacio de sobra: _Hardware → Hard Disk (scsi0) → Disk Action → Resize_ → **Size Increment: 17** → **Resize disk**.

6. Ahora cloud-init. Debian (la imagen cloud, no una ISO normal) viene con un programa llamado cloud-init que es el que busca en el primer arranque para cargar la configuración del sistema (username, password, dns, ip, ssh, etc.). La idea aquí es montar una unidad virtual para que debian encuentre esos datos.
	- Para ello borramos la unidad de CD/DVD que trae la VM
	- **Add → CloudInit Drive**, con Bus _IDE 2_ y Storage `local-lvm` → **Add**

7. En la pestaña **Cloud-Init** rellenamos los datos sin tocar la ip config (como queremos convertirlo en plantilla, no definimos ip, cada clon tendrá la suya propia más adelante). Damos a **Regenerate Image**

![](https://imgur.com/QrX5Tk5.png)

8. En Options -> Boot Order: Ajustar el orden de arranque: marca SCSI0 y subirlo al primer puesto

![](https://imgur.com/rmB4HLo.png)

9. En Hardware -> Add -> Serial Port -> 0 -> **Add**, luego Hardware -> Display -> Graphic card -> Serial Terminal 0 -> OK

> Esto es para que la consola de promox utilice xterm.js. Las imágenes cloud están pensadas para servidores sin pantalla. Con este paso desviamos la consola al puerto serie para poder interactuar con debian.

![](https://imgur.com/gYgjOLq.png)


10. Ahora toca convertir la VM en plantilla. Clic derecho en **9000 (debian13-template) → Convert to template** → **Yes**.

> Esta paso no se puede deshacer: una plantilla no vuelve a ser VM. Si quisieramos modificar algo, hacemos un clon, modificamos y convertimos ese clon en la nueva plantilla.


11. Ahora click derecho en la plantilla -> Clone
	- VM ID: tal cual
	- Name: cualquiera
	- Mode: Full/Linked
	- Target: local-lvm

12. En Cloud-Init del nuevo clone, configuramos IP Config (ip estática y gateway de proxmox en vmbr1). Luego **Regenerate Image**

![](https://imgur.com/K3n9JrI.png)


13. Arrancamos la VM y comprobamos que obtuvo ip

```shell
ip -4 a; ip r; cat /etc/resolv.conf; ping -c 3 8.8.8.8
```

