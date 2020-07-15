# Skytap-VPN-Virtual-Private-Cloud :closed_lock_with_key:

En la sigiente guía se muestra el paso a paso para la configuración y creación de una VPN en Skytap y una VPN for VPC en IBM Cloud con el objetivo de establecer la conexión entre una maquina virutal (VM) alojada en Skytap y un virtual server (VSI) alojado en una Virtual Private Cloud (VPC)  de IBM.

# Indice:
1. [Crear y configurar VPN Skytap](#1-crear-y-configurar-vpn-skytap).

1.1. [Crear IP Pública estática](#11-crear-ip-pública-estática).

1.2. [Crear una VPN](#12-crear-una-vpn).


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
- **IBM subnet**: Subred a la que pertenece la máquina virtual del ambiente sobre el que se está trabajando.
- **IBM Peer IP**: IP Pública estática
- **Remote peer IP**: IP Pública del lado remoto, es la misma **IP de pasarela** que se configura en VPC for VPN. (En los próximos pasos)
- **Preshared Key**: Contraseña a disposición del usuario.
- **NAT**: OFF (no se tendrán varias máquinas conectadas al mismo ambiente por lo que no es necesario tenerlo habilitado).

En los parámetros de Fase 1 y 2, se modifican de la siguiente manera:
- **Phase encryption algorithm**: aes 256 (este tipo de encriptación de alta seguridad)
- **Phase hash algorithm**: sha256
- **Group DH**: modp1536
- **Key Life (in seconds)**: 20000
- **Phase 2 perfect forward secrecy (PFS)**: OFF 
Las demás configuraciones se dejan por defecto como vienen.


Una vez realizada esta configuración se procede a la creación y configuración de la VPN for VPC dentro de IBM, en donde se obtendrán los parámetros necesarios para terminar esta configuración en Skytap, pero antes de eso, al guardar la configuración realizada le aparecerá en pantalla todos los detalles de la VPN. Al lado izquierdo encontrará un recuadro, tal como se muestra en la imagen a continuación.

<img width="294" alt="img9" src="https://user-images.githubusercontent.com/60628267/86848768-3966d000-c074-11ea-8f90-ae46753b7a6f.PNG">
 
Agregue la **subred remota** (a la que pertenece la **VSI**, se le proporcionará en el próximo paso como **Subredes locales** en la configuración de la VPN para VPC), también agregue la subred a la que pertenece la **Remote Peer IP** que acaba de configurar.




## 2. Crear y configurar una VPN for VPC en IBM 

Para el segundo paso de esta guía, se realizará la creación y configuración de una VPN for VPC, la cual nos permitira establercer la conexión entre la VPN de Skytap y de esta manera la conexión entre la VM alojada en Skytap y la VSI alojada en la VPC de IBM Cloud. Para iniciar tenemos que crear una VPN for VPC, para ello nos dirijimos a la siguiente ruta:

**Infraestructura VPC -> VPN **



<img width="384" alt="img6" src="1.png">

1. Cree una nueva pasarela VPN haciendo clic en **+ Nueva pasarela VPN**.
   Especifique la información siguiente:

**Nombre**: especifique un nombre para la pasarela VPN en la nube privada virtual, como por ejemplo my-vpn-gateway.
**Nube privada virtual**: seleccione la VPC.
**Grupo de recursos**: seleccione un grupo de recursos para la VPN.
**Subred**: seleccione la subred en la que se creará la pasarela VPN. Para obtener el mejor rendimiento, cree la pasarela VPN en una subred sin ningún otro recurso de VPC para asegurarse de que haya suficientes IP privadas disponibles para la pasarela. Una pasarela VPN necesita 8 direcciones IP privadas para dar cabida a la alta disponibilidad y a las actualizaciones continuas.

2. En la sección Nueva conexión VPN, defina una conexión entre esta pasarela y una red externa a la VPC especificando la información siguiente.
**Nombre de conexión**: escriba un nombre para la conexión, como por ejemplo my-connection.
**Dirección de pasarela igual**: especifique la dirección IP de la pasarela VPN para la red externa a la VPC.
**Clave precompartida**: especifique la clave de autenticación de la pasarela VPN para la red externa a la VPC.
**Subredes locales**: especifique una o varias subredes de la VPC con las que desea conectar a través del túnel VPN.
**Subredes iguales**: especifique una o varias subredes de la otra red con las que desea conectar a través del túnel VPN.

**Importante**: Dado que la configuración de la VPN de Skytap utiliza el protocolo IPSEC es necesario realizar una configuración tipo espejo en la VPN for VPC que estamos creando, por lo que se recomienda utilizar los mismos parametros que se usaron anteriormente en la configuración de la VPN de Skytap. De manera que las politicas IKE (VPN FOR VCP) = PHASE 1 (VPN SKYTAP) y las politicas IPSEC (VPN FOR VPC) = PHASE 2(VPN SKYTAP)

Teniendo en cuenta lo anterior, y la configuración realizada en la VPN de Skytap especifique los parámetros de Internet Key Exchange (IKE) e Internet Protocol Security (IPsec) para utilizar la negociación de fase 1 y fase 2 de la conexión.

### Consejos:
2.1 Seleccione Automático si desea que la pasarela de nube intente establecer automáticamente la conexión. (aplica para conexiones que no requieren del protoco IPSEC)
2.2 Seleccione o cree políticas personalizadas si tiene que imponer requisitos de seguridad específicos o si la pasarela VPN de la otra red no da soporte a las propuestas de seguridad que se intentan mediante la negociación automática.2.3 Importante: los parámetros de seguridad de IKE e IPsec que especifique para la conexión deben coincidir con los parámetros que se han establecido en la pasarela para la red externa a la VPC. 

Una vez se ha creado la nueva pasarela vpn y la nueva conexión VPN, se nos debera visualizar estado, y el nombre de nuestra conexión como se muestra a continuación:


para mas información visite: https://cloud.ibm.com/docs/vpc-on-classic?topic=vpc-on-classic-creating-a-vpc-using-the-ibm-cloud-console#creating-a-vpn



## 3. Probar conexión entre VPNs.:white_check_mark:

Después de crear la conexión VPNs, pruébela con la herramienta de **Test WAN**.

<img width="406" alt="img10" src="https://user-images.githubusercontent.com/60628267/86849883-f7d72480-c075-11ea-9de5-9be4d5be12a2.PNG">

Ingrese una dirección IP y un número de puerto remoto desde una máquina en una de las subredes remotas incluidas. Idealmente, ingrese la dirección IP del VSI de IBM el cual puede responder a pings.

<img width="306" alt="img11" src="https://user-images.githubusercontent.com/60628267/86850111-543a4400-c076-11ea-85ff-88da9215e2c5.PNG">

Skytap realiza cuatro pruebas de conectividad y muestra los resultados, si aparecen tal como se muestra a continuación estas correctamente configuradas las dos VPN y se estan comunicando entre sí. Una vez obtenido este resultado, habilite la VPN, haciendo clic en **Enable**.

A continuación, se muestra la prueba de conectividad entre ambas VPNs.

- Del lado de Skytap: accedemos a la máquina virtual y hacemos directamente ping a la IP privada del VSI de IBM.
<img width="526" alt="1" src="https://user-images.githubusercontent.com/60628267/87592889-368d6000-c6b0-11ea-8902-709b8f1305cb.png">

- Del lado de IBM:
Se ingresa al VSI mediante ssh, usando el siguiente comando: ssh -i root@ip_flotante, hacemos ping a la máquina virtual de Skytap.
<img width="408" alt="Sin título" src="https://user-images.githubusercontent.com/60628267/87592171-0abdaa80-c6af-11ea-93e0-0c446fd7d660.png">

Observamos que para ambos casos la respuesta al ping es satisfactoria comprabando así que el intercambio de paquetes está seguro, puesto que se realizaron pruebas desde otro equipo excluido de las subredes incluidas y no es posible su acceso a las máquinas virtuales.

## Autores.:woman: :man:

IBM Cloud Tech Sales



