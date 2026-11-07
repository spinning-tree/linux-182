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

> **Ojo con la herencia.** Si creas una sub-OU dentro de equipos y modificas la misma GPO, el equipo mostrará las dos GPOs, porque hereda la de Equipos y además tiene la suya. Cuando dos GPOs configuran lo mismo, gana la que está más cerca del equipo, la de la sub-OU.




