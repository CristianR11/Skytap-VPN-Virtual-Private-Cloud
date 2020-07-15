# Skytap-VPN-Virtual-Private-Cloud


# Indice:
1. [Crear y configurar VPN Skytap](#1-crear-y-configurar-vpn-skytap).

1.1. [Crear IP Pública estática](#1.1.-crear-ip-pública-estática).

1.2. [Crear una VPN](#1.2.-crear-una-vpn).



## 1. Crear y configurar VPN Skytap
Acceda desde la consola de IBM al servicio de Skytap **Launch Skytap on IBM Cloud**, servicio que previamente debió adquirir.

<img width="960" alt="img1" src="https://user-images.githubusercontent.com/60628267/86811209-43bca600-c043-11ea-9c14-30a0b92662d7.PNG">

Desde la barra de navegación, haga clic en **Environments** y seleccione el ambiente en el cual creará la VPN.

### 1.1. Crear IP Pública estática 
Desde la barra de navegación, ingrese a **Manage -> Public IPs**. En este paso se creará una dirección IP pública estática la cual se utiliza como la dirección IP de Skytap para la conexión VPN. 

<img width="426" alt="img3" src="https://user-images.githubusercontent.com/60628267/86812051-2f2cdd80-c044-11ea-96d4-0666e708f537.PNG">

Haga clic en **Static Public IP address**, deberá elegir de la lista de regiones disponibles la que pertenece su ambiente de Skytap y luego haga clic en añadir **Add IP address**. Como respuesta obtendrá una mensaje en pantalla: Added public IP address [Static Public IP address] in region [region].

<img width="674" alt="img4" src="https://user-images.githubusercontent.com/60628267/86812174-5388ba00-c044-11ea-9319-a436b4cc8187.PNG">


### 1.2. Crear una VPN 
Desde la barra de navegación, haga clic en **Manage -> WAN -> New VPN**. Se abrirá un recuadro como el siguiente en el cual deberá proporcionar los datos de configuración. Verifique que la casilla de **IBM peer IP** tenga la IP Pública estática que creó en el paso anterior.

<img width="263" alt="img5" src="https://user-images.githubusercontent.com/60628267/86812143-4966bb80-c044-11ea-9f7a-174a6ee62603.PNG">

Especificaciones de los parametros a configurar:
- IBM subnet: Subred a la que pertenece la máquina virtual del ambiente sobre el que se está trabajando.
- IBM Peer IP: IP Pública estática 
- Remote peer IP: IP Pública del lado remoto, es la misma **Hosted Peer Address** que se configura en IPSec VPN. (Se configurará en los próximos pasos)
- Preshared Key: Contraseña a disposición del usuario.
- NAT: OFF (no se tendrán varias máquinas conectadas al mismo ambiente por lo que no es necesario tenerlo habilitado).

En los parámetros de Fase 1 y 2, se modifican de la siguiente manera:
- Phase encryption algorithm: aes 256 (este tipo de encriptación de alta seguridad)
- Phase hash algorithm: sha256
- Group DH: modp1536

Para Fase 1:
- Key Life (in seconds): 28800

Para Fase 2:
- Key Life (in seconds): 3600

- Phase 2 perfect forward secrecy (PFS) : ON 
Las demás configuraciones se dejan por defecto como vienen.


Una vez realizada esta configuración se procede a la creación y configuración de IPSec VPN, en donde se obtendrán los parámetros necesarios para terminar esta configuración en Skytap, pero antes de eso, al guardar la configuración realizada le aparecerá en pantalla todos los detalles de la VPN. Al lado izquierdo encontrará un recuadro, tal como se muestra en la imagen a continuación.

<img width="294" alt="img9" src="https://user-images.githubusercontent.com/60628267/86848768-3966d000-c074-11ea-8f90-ae46753b7a6f.PNG">
 
Agregue la subred remota (**Hosted Private Subnets**, se le proporcionará en el próximo paso), también agregue la subred a la que pertenece la **Remote Peer IP** que acaba de configurar.
