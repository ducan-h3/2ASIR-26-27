**Instalacion y configuracion de servidor DNS**

**Paso 1: Actualizar la lista de paquetes**

Bash
sudo apt update
Verificación: Al finalizar debe mostrar la lista de paquetes actualizados o indicar All packages are up to date.

**Paso 2: Actualizar los programas del sistema**

Bash
sudo apt upgrade -y
Verificación: El proceso debe terminar sin errores y devolverte el control del terminal (user@server:~$).

**Paso 3: Instalar el servidor DNS Bind9**

Bash
sudo apt install bind9 bind9utils bind9-doc -y
Verificación: Ejecuta sudo systemctl status bind9 y comprueba que en la salida aparezca la línea Active: active (running).

**Paso 4: Abrir el archivo de opciones de Bind9**

Bash
sudo nano /etc/bind/named.conf.options
Verificación: Se abrirá el editor de texto nano en pantalla con el contenido del archivo.

**Paso 5: Configurar los servidores DNS reenviadores (Forwarders)**

(Dentro del archivo nano, desmarca el bloque forwarders quitando las barras // y edítalo para que quede así)

Fragmento de código
forwarders {
    1.1.1.1;
    8.8.8.8;
};
Nota: Para guardar en nano pulsa Ctrl + O, luego Enter y para salir pulsa Ctrl + X.

**Paso 6: Comprobar la sintaxis de la configuración global**

Bash
sudo named-checkconf
Verificación: Si el archivo está bien configurado, la terminal no devolverá ningún mensaje ni error.

Paso 7: Abrir el archivo de definición de zonas locales

Bash
sudo nano /etc/bind/named.conf.local
Verificación: Se abrirá el editor nano listo para editar.

**Paso 8: Declarar tu dominio local**

(Añade este bloque al final del archivo en nano)

Fragmento de código
zone "midominio.local" {
    type master;
    file "/etc/bind/zones/db.midominio.local";
};
Nota: Guarda (Ctrl + O, Enter) y sale (Ctrl + X).

**Paso 9: Crear la carpeta para los archivos de zona**

Bash
sudo mkdir /etc/bind/zones
Verificación: Ejecuta ls /etc/bind y comprueba que la carpeta zones se ha creado.

**Paso 10: Copiar la plantilla por defecto para tu zon**a

Bash
sudo cp /etc/bind/db.local /etc/bind/zones/db.midominio.local
Verificación: Ejecuta ls /etc/bind/zones para confirmar que el archivo db.midominio.local existe.

**Paso 11: Abrir el archivo de tu zona para editarlo**

Bash
sudo nano /etc/bind/zones/db.midominio.local
Verificación: Se abrirá la plantilla en nano.

**Paso 12: Escribir los registros DNS de tu dominio**

(Reemplaza todo el contenido por estas líneas, adaptando la IP 192.168.1.50 a la IP fija de tu servidor)

Plaintext
$TTL    604800
@       IN      SOA     ns1.midominio.local. admin.midominio.local. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns1.midominio.local.
ns1     IN      A       192.168.1.50
server  IN      A       192.168.1.50
www     IN      A       192.168.1.50
Nota: Guarda (Ctrl + O, Enter) y sale (Ctrl + X).

**Paso 13: Validar que el archivo de zona no tenga errores**

Bash
sudo named-checkzone midominio.local /etc/bind/zones/db.midominio.local
Verificación: La salida debe responder explícitamente con OK.

**Paso 14: Reiniciar el servicio Bind9 para aplicar los cambios**

Bash
sudo systemctl restart bind9
Verificación: Ejecuta sudo systemctl status bind9 y verifica que el estado sigue en active (running).

Paso 15: Probar la resolución DNS localmente

Bash
dig @127.0.0.1 www.midominio.local
Verificación: En la salida enviada por pantalla, busca la sección ANSWER SECTION. Deberá responder asignando la IP configurada (192.168.1.50).


**Configuracion de zona inversa**
**Paso 1**
Modificar named.conf.local
En el servidor
Añade el bloque de la zona inversa al final del archivo /etc/bind/named.conf.local:

Plaintext
zone "5.16.172.in-addr.arpa" {
    type master;
    file "/etc/bind/zones/db.172.16.5";
};
Verificación: El archivo completo debe contener tanto la zona directa como la inversa. Guarda los cambios con Ctrl + O, Enter y sal con Ctrl + X.

**Paso 2**
Crear el archivo de zona inversa
En el servidor
Crea el archivo de registros inversos:

Bash
sudo nano /etc/bind/zones/db.172.16.5
Y añade la siguiente estructura básica con los registros PTR para tus equipos:

Plaintext
$TTL 604800
@   IN  SOA ns1.midominio.local. admin.midominio.local. (
              3     ; Serial
         604800     ; Refresh
          86400     ; Retry
        2419200     ; Expire
         604800 )   ; Negative Cache TTL

@   IN  NS  ns1.midominio.local.
51  IN  PTR equipo1.midominio.local.
52  IN  PTR equipo2.midominio.local.
53  IN  PTR equipo3.midominio.local.
**PASO 3**
Reiniciar BIND9
En el servidor
Aplica la configuración reiniciando el servicio:

Bash
sudo systemctl restart bind9


Para probar que la resolución inversa está funcionando correctamente tras realizar los cambios, debes ejecutar la consulta desde la terminal del cliente.
**Paso 1**
.Ejecutar el comando dig inverso:En el cliente.Lanza la consulta inversa para comprobar la IP del equipo1:
dig @172.16.5.30 -x 172.16.5.51
Verificación: Comprueba que el campo status: en la cabecera muestre NOERROR en lugar de NXDOMAIN.

**Paso 2**
Verificar la respuesta
Revisa la sección de respuesta (ANSWER SECTION) en la salida de la terminal.
Verificación: Debe aparecer el registro PTR asociando la IP al nombre: 51.5.16.172.in-addr.arpa. IN PTR equipo1.midominio.local

**Paso 3**
Verificar el resto de equipos inversos
dig @172.16.5.30 -x 172.16.5.52
dig @172.16.5.30 -x 172.16.5.53


**Regitro de dominio**

<img width="1919" height="1041" alt="image" src="https://github.com/user-attachments/assets/10aa90fb-543f-43c6-8389-d63f925b9d11" />

<img width="1919" height="996" alt="image" src="https://github.com/user-attachments/assets/61ab7951-5757-4a0e-a503-18e9c9254876" />



**1. Configuración de los Servidores DHCP
Cada servidor gestiona su propia mitad del rango de la subred 172.16.6.0/24.

Archivo de configuración: /etc/dhcp/dhcpd.conf

Comandos de gestión:

Bash
# Validar la sintaxis del DHCP
sudo dhcpd -t -cf /etc/dhcp/dhcpd.conf

# Reiniciar el servicio DHCP
sudo systemctl restart isc-dhcp-server

# Ver las concesiones (leases) actuales
sudo tail -f /var/lib/dhcp/dhcpd.leases
2. Configuración de Servidores DNS (BIND9)
Servidor 1 (Maestro - IP: 172.16.6.30)
Archivo de configuración local (/etc/bind/named.conf.local):

Plaintext
zone "servidor1.local" {
    type master;
    file "/etc/bind/db.servidor1.local";
    allow-transfer { 172.16.6.31; };
    also-notify { 172.16.6.31; };
};
Servidor 2 (Esclavo - IP: 172.16.6.31)
Archivo de configuración local (/etc/bind/named.conf.local):

Plaintext
zone "servidor1.local" {
    type slave;
    file "/var/cache/bind/db.servidor1.local";
    masters { 172.16.6.30; };
};
Comandos de gestión y validación de DNS:

Bash
# Comprobar errores de sintaxis en BIND9
sudo named-checkconf

# Reiniciar el servicio DNS
sudo systemctl restart bind9
3. Comprobaciones y Pruebas Finales
Verificar la resolución de nombres contra el servidor esclavo:

Bash
nslookup servidor1.local 172.16.6.31
Comprobar los logs del sistema para ver las transferencias de zona:

Bash
sudo journalctl -u bind9 -e
Renovar la IP en la máquina cliente:

Bash
sudo dhclient -r
sudo dhclient
ip a**





