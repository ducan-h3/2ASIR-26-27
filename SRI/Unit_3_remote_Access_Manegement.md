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
