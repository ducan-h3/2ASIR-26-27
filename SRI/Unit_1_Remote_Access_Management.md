**Definicion de SSH**
SSH (Secure Shell) es un protocolo que permite conectarse y administrar un equipo de forma remota y segura mediante una terminal.


**PASOS PARA INSTALAR OPENSSH**
-Primero hemos hecho un sudo apt update para actualizar el sistema, despues hemos hecho un sudo apt upgrade para que se instalen todas las versiones mas recientes de todos los paquetes que tenemos ya instalados.
-Cuando ya hayamos hecho esos dos pasos ya podemos empezar a instalar el servicio SSH
-Empezamos poniendo en la terminal el comando: sudo apt install openssh-server, cuando le demos a enter nos pondra la opcion S/N que significa si queremos confirmar que se instale o no, en mi caso le doy a S para que se instale
-Ya lo tendriamos instalado, para comprobar que se ha instalado correctamente hacemos un sudo systemctl status SSH. Si Active: active (running) significa que SSH está funcionando correctamente. 


**SSH y creacion de claves**
¿Qué es SSH?
SSH (Secure Shell) es un protocolo que permite conectarse de forma segura a otro ordenador o servidor a través de una red. Se utiliza mucho para administrar servidores Linux de manera remota desde una terminal.

Por ejemplo, si tenemos un Ubuntu Server en otra máquina, podemos conectarnos desde nuestro ordenador utilizando su dirección IP y un usuario. La conexión está cifrada, por lo que la información que se intercambia entre ambos equipos queda protegida.

Para conectarnos mediante SSH normalmente utilizamos el comando ssh, seguido del nombre del usuario y la dirección IP del servidor. Por ejemplo: ssh usuario@192.168.1.100.

Una de las formas más seguras de autenticarse en SSH es mediante un par de claves. Este par está formado por una clave privada y una clave pública. La clave privada se guarda en nuestro ordenador y nunca debemos compartirla. La clave pública, en cambio, se coloca en el servidor. Cuando intentamos conectarnos, SSH utiliza ambas claves para comprobar nuestra identidad.

**Crear un par de claves SSH en Ubuntu Server**
Para crear el par de claves utilizamos el comando ssh-keygen -t ed25519.

Al ejecutarlo, Ubuntu nos preguntará dónde queremos guardar las claves. Si pulsamos Enter, se utilizará la ubicación predeterminada, normalmente /home/usuario/.ssh/.

Después nos pedirá una passphrase, que es una contraseña adicional para proteger la clave privada. Es recomendable utilizar una, ya que añade una capa extra de seguridad.

Una vez terminado el proceso, se crearán dos archivos:

id_ed25519: es la clave privada. Debemos mantenerla protegida y nunca compartirla.

id_ed25519.pub: es la clave pública. Esta es la que podemos copiar al servidor.

Para copiar la clave pública al servidor podemos utilizar el comando ssh-copy-id usuario@IP_DEL_SERVIDOR. Este comando añade nuestra clave pública al archivo de claves autorizadas del usuario en el servidor.

A partir de ese momento, cuando nos conectemos mediante SSH, el servidor podrá comprobar nuestra identidad utilizando la clave pública y nosotros podremos autenticarnos con nuestra clave privada, sin tener que introducir la contraseña del usuario del servidor en cada conexión.

