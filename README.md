# Skytap-VPN-Virtual-Private-Cloud
En esta guía aprenderá cómo crear una conexión VPN entre su cuenta de Skytap y una VPN dentro de una VPC en IBM. El propósto de las VPNs permitir la comunicación entre as máquinas virtuales de Skytap y un servidor virtual VSI, el cual está dentro de la VPC.


una red externa, la cual para este caso será una VPN IPsec de IBM. El propósto de las VPNs es conectar las máquinas virtuales de Skytap a otra máquina de la red de conexión.

# Indice:
1. [Crear y configurar VPN Skytap](#1-crear-y-configurar-vpn-skytap).

1.1. [Crear IP Pública estática](#1.1.-crear-ip-pública-estática).

1.2. [Crear una VPN](#1.2.-crear-una-vpn).

2. [Crear y configurar IPSec VPN](#2-crear-y-configurar-ipsec-vpn).

3. [Probar conexión VPN](#3-probar-conexión-entre-vpns).



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
- Remote peer IP: IP Pública del lado remoto, es la misma **IP de pasarela** que se configura en VPC for VPN. (En los próximos pasos)
- Preshared Key: Contraseña a disposición del usuario.
- NAT: OFF (no se tendrán varias máquinas conectadas al mismo ambiente por lo que no es necesario tenerlo habilitado).

En los parámetros de Fase 1 y 2, se modifican de la siguiente manera:
- Phase encryption algorithm: aes 256 (este tipo de encriptación de alta seguridad)
- Phase hash algorithm: sha256
- Group DH: modp1536
- Key Life (in seconds): 20000
- Phase 2 perfect forward secrecy (PFS) : OFF 
Las demás configuraciones se dejan por defecto como vienen.


Una vez realizada esta configuración se procede a la creación y configuración de la VPN for VPC dentro de IBM, en donde se obtendrán los parámetros necesarios para terminar esta configuración en Skytap, pero antes de eso, al guardar la configuración realizada le aparecerá en pantalla todos los detalles de la VPN. Al lado izquierdo encontrará un recuadro, tal como se muestra en la imagen a continuación.

<img width="294" alt="img9" src="https://user-images.githubusercontent.com/60628267/86848768-3966d000-c074-11ea-8f90-ae46753b7a6f.PNG">
 
Agregue la **subred remota** (a la que pertenece la **VSI**, se le proporcionará en el próximo paso como **Subredes locales** en la configuración de la VPN para VPC), también agregue la subred a la que pertenece la **Remote Peer IP** que acaba de configurar.

## 2. Crear y configurar VPN for VPC


## 3. Probar conexión entre VPNs.

Después de crear la conexión VPNs, pruébela con la herramienta de **Test WAN**.

<img width="406" alt="img10" src="https://user-images.githubusercontent.com/60628267/86849883-f7d72480-c075-11ea-9de5-9be4d5be12a2.PNG">

Ingrese una dirección IP y un número de puerto remoto desde una máquina en una de las subredes remotas incluidas. Idealmente, ingrese la dirección IP del VSI de IBM el cual puede responder a pings.

<img width="306" alt="img11" src="https://user-images.githubusercontent.com/60628267/86850111-543a4400-c076-11ea-85ff-88da9215e2c5.PNG">

Skytap realiza cuatro pruebas de conectividad y muestra los resultados, si aparecen tal como se muestra a continuación estas correctamente configuradas las dos VPN y se estan comunicando entre sí. Una vez obtenido este resultado, habilite la VPN, haciendo clic en **Enable**.

A continuación, se muestra la prueba de conectividad entre ambas VPNs.


