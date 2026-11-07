# configuración adicional

> Previamente es necesario [configurar windows server](configuracion-basica-windows-server.md)

## gpos

Las GPO's son paquetes de configuraciones que se definen en el DC y se almacenan en la carpeta SYSVOL que los clientes descargan. Se mapean a una OU (afecta a todos los equipos/usuarios de esa OU)

> Ojo: clientes aquí se refiere tanto a pcs como usuarios: la gpos de equipo se aplica al arancar el pc, la de usuario, cuando inicia sesión). También se aplican cada 90 minutos (aunque puede forzarse con `gpupdate /force`)

### gpos de usuarios

1. Abrir **gpmc.msc** en DC -> OU del dominio -> click derecho en Usuarios -> _Create a GPO in this domain, and Link it here_ → nombrarla

> Para este ejemplo vamos a bloquear el acceso al panel de control

![](https://imgur.com/qKFUF40.png)

2. Clic derecho en la GPO → **Edit** → _User Configuration → Policies → Administrative Templates → Control Panel_ → **Prohibit access to Control Panel and PC settings** → _Enabled_.

![](https://imgur.com/yQHYqKX.png)


3. Seguidamente, en la pc del usuario, ejecutamos `gpupdate /force` 

![](https://imgur.com/L0EkHGo.png)

4. Comprobamos que la GPO se aplicó correctamente con `gpresult /r`

![](https://imgur.com/N9md5dB.png)

5. Verificamos que no puede abrir el Panel de Control

![](https://imgur.com/oaU5hWa.png)

6. Esta GPO se aplica a todos los usuarios de la OU Usuarios, porque por defecto el filtrado de seguridad tiene a _Authenticated Users_ con Read + Apply. Si queremos que solo se aplique a un usuario tienes que hacer estos cambios:

- Quitar _Authenticated Users_ de _Security Filtering_. Mientras siga ahí, la GPO se aplica a todos.
- Añadir al usuario en _Scope → Security Filtering_. Así el usuario recibe Read + Apply y es el único al que se aplicará la GPO.

![](https://imgur.com/b6jdbEr.png)

- Añadir a _Domain Computers_ con Read en _Delegation → Advanced_. El PC descarga la GPO con su propia cuenta de equipo, y sin permiso de lectura no podría aplicarla ni al usuario.


![](https://imgur.com/kq3175G.png)


### gpos de grupos

1. **Creamos el grupo.** En dsa.msc, clic derecho en la OU **Grupos** → _New → Group_ → *nombre del grupo*, ámbito **Global**, tipo **Security**.

![](https://imgur.com/zFnJh0p.png)


2. **Añadir miembros.** Abrir el grupo → pestaña **Members** → añadir usuarios. 

![](https://imgur.com/sOmcep6.png)

3. **Cambiar el filtrado.** Finalmente, en la GPO, dentro de _Security Filtering_, quitar usuario específico y poner grupo. 

>_Domain Computers_ con Read se queda como estaba.

3. **Comprobar.** Cerrar la sesión los usuarios y volver a entrar (el token tiene que recoger el grupo nuevo).

### gpos de equipos

1. **Crear la GPO:** En gpmc.msc, clic derecho en **Equipos** → _Create a GPO in this domain, and Link it here_ → nombre -> *mensaje*

![](https://imgur.com/K1wOz8k.png)

2. **Configurar la GPO:** _Edit_ → _Computer Configuration → Policies → Windows Settings → Security Settings → Local Policies → Security Options_:
    - **Interactive logon: Message title for users attempting to log on** → por ejemplo _Aviso_.
    - **Interactive logon: Message text for users attempting to log on** → por ejemplo _Equipo propiedad de November Lab._


![](https://imgur.com/yGbK0l4.png)


3. **Aplicar.** En los clientes ejecutar **gpupdate /force**
4. **Comprobar.** Cerrar la sesión; el mensaje debe aparecer antes de la pantalla de inicio. 
5. Con **gpresult /r /scope computer** debe aparecer la GPO aplicada

![](https://imgur.com/0Gt3AVg.png)

### configuración avanzada gpos

#### Herencia
Si creas una sub-OU dentro de Equipos y modificas la misma GPO, el equipo hereda la de Equipos pero gana la que está más cerca del equipo, la de la sub-OU.

![](https://imgur.com/6Sbfs1U.png)

#### Block Inheritance
Se pone en una OU y corta todas las GPOs que vienen de arriba, menos las que están en _Enforced_.

![](https://imgur.com/WobfoyP.png)

#### Enforced
Se pone en el vínculo de una GPO (no en la OU), y gana a lo que digan las GPOs de las sub-OUs que haya debajo.

![](https://imgur.com/TcY3E6Q.png)

#### Loopback processing
Generalmente, las GPOs de usuario dependen de la OU del usuario. Con loopback que dependan solamente del equipo en el que inicia sesión, o del equipo más el usuario. 

> Tiene dos modos: **Merge**, el usuario recibe sus GPOS y las del equipo (si hay contradicción, gana el equipo). **Replace**, el usuario recibe las OUs del equipo e ignora las suyas.

1. **Crea la GPO.** Una nueva GPO vinculada a la sub-OU de equipos: _C-Loopback-Portatiles_.
2. **Activa loopback.** En _Computer Configuration → Policies → Administrative Templates → System → Group Policy → Configure user Group Policy loopback processing mode_, activa la directiva de loopback en modo **Merge**/**Replace**
3. **Añade la restricción.** En la misma GPO, activa la GPO deseada.
4. **Aplícala.** Ejecuta **gpupdate /force** en el equipo y cierra la sesión.
5. **Comprueba.** El mismo usuario, con un resultado distinto según el equipo.

![](https://imgur.com/bH6iVqz.png)

# SMB + NTFS

