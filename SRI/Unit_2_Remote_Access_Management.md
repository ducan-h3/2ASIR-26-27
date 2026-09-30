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


