**Instalacion y configuracion de servicio DHCP**
Sí. En Ubuntu puedes instalar el servidor DHCP ISC DHCP desde la terminal así:

1. Actualizar los paquetes
sudo apt update

2. Instalar DHCP
sudo apt install isc-dhcp-server

Cuando termine, comprueba el servicio:

sudo systemctl status isc-dhcp-server

Es posible que aparezca failed inicialmente. No pasa nada: normalmente es porque todavía no has configurado la interfaz y la red.



Claro. Si estás configurando ISC DHCP Server en Ubuntu/Debian, hay dos archivos importantes:

/etc/default/isc-dhcp-server → indica en qué interfaz escucha DHCP.

/etc/dhcp/dhcpd.conf → indica qué direcciones IP y opciones entrega.

1. Configura la interfaz
Primero mira el nombre:

ip a

Por ejemplo, si tu interfaz es enp0s3, edita:

sudo nano /etc/default/isc-dhcp-server

Pon:

INTERFACESv4="enp0s3"

Guarda con Ctrl+O, Enter y sal con Ctrl+X.

2. Configura dhcpd.conf
Abre:

sudo nano /etc/dhcp/dhcpd.conf

Puedes poner, por ejemplo:

authoritative;

subnet 172.16.5.30 netmask 255.255.255.0 {
range 172.16.0.0 172.31.255.255;
default-lease-time 600;
max-lease-time 7200;
}

Esto significa:

Red: 192.168.10.0/24

DHCP entrega IPs desde 192.168.10.100 hasta 192.168.10.200

Puerta de enlace: 192.168.10.1

DNS: 8.8.8.8 y 1.1.1.1

Importante: estos valores tienen que coincidir con la IP/red configurada en la interfaz del servidor. No copies 192.168.10.x si tu interfaz está, por ejemplo, en 192.168.1.x.

3. Comprueba que no haya errores
sudo dhcpd -t -cf /etc/dhcp/dhcpd.conf

Si no muestra errores, reinicia:

sudo systemctl restart isc-dhcp-server

Y:

sudo systemctl status isc-dhcp-server

Si me pegas aquí el resultado de:

ip a



**Instalar el servicio dhcp en windows server**
1. Abrir el Administrador del servidor
Pulsa Inicio → Administrador del servidor.
Haz clic en Administrar.
Selecciona Agregar roles y características.
2. Elegir el tipo de instalación
Pulsa Siguiente.
Selecciona Instalación basada en roles o características.
Pulsa Siguiente.
Selecciona tu servidor.
Pulsa Siguiente.
3. Instalar DHCP
En Roles de servidor, marca Servidor DHCP.
Pulsa Agregar características.
Pulsa Siguiente varias veces.
Pulsa Instalar.
4. Configurar DHCP
Cuando termine la instalación:
Pulsa en la bandera de notificaciones arriba.
Selecciona Completar configuración de DHCP.
Pulsa Siguiente.
Pulsa Confirmar.
5. Comprobar que está instalado



Ahora vamos a configurarlo

Crear un ámbito DHCP en Windows Server

Primero abre:

Administrador del servidor → Herramientas → DHCP

1. Abrir IPv4

En la ventana de DHCP:

Tu servidor → IPv4

Haz clic derecho sobre IPv4 → Ámbito nuevo.

2. Nombre del ámbito

Pon un nombre, por ejemplo:

Red_LAN

Pulsa Siguiente.

3. Rango de direcciones IP

Aquí indicamos las IP que DHCP va a repartir.

Por ejemplo:

IP inicial: 192.168.1.100
IP final: 192.168.1.200
Longitud: 24
Máscara: 255.255.255.0

Pulsa Siguiente.

4. Exclusiones

Aquí puedes indicar IP que no quieres que DHCP reparta.

Por ejemplo, si quieres reservar las primeras IP para servidores:

Inicial: 192.168.1.1
Final: 192.168.1.20

Pulsa Agregar → Siguiente.

Si no necesitas exclusiones, simplemente pulsa Siguiente.

5. Duración de la concesión

Es el tiempo durante el que un equipo puede utilizar una IP.

Puedes dejar el valor predeterminado:

8 días

Pulsa Siguiente.

6. Configurar opciones DHCP

Selecciona:

Sí, deseo configurar estas opciones ahora

Pulsa Siguiente.

7. Puerta de enlace

Escribe la IP del router. Por ejemplo:

192.168.1.1

Pulsa Agregar → Siguiente.

8. Servidor DNS

Pon la dirección IP de tu servidor DNS.

Si el propio Windows Server tiene DNS:

192.168.1.10

Pulsa Siguiente.

9. Servidor WINS

Puedes dejarlo vacío.

Pulsa Siguiente.

10. Activar el ámbito

Selecciona:

Sí, deseo activar este ámbito ahora

Pulsa Siguiente → Finalizar.

11. Comprobar

En DHCP debería aparecer:

IPv4
 └── Red_LAN
      ├── Grupo de direcciones
      ├── Concesiones de direcciones
      ├── Reservas
      └── Opciones de ámbito

En Grupo de direcciones deberías ver:

192.168.1.100 - 192.168.1.200

Importante: el servidor DHCP debe tener una IP fija, no una IP obtenida por DHCP.

