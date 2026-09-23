**Definicion de SSH**
SSH (Secure Shell) es un protocolo que permite conectarse y administrar un equipo de forma remota y segura mediante una terminal.


**PASOS PARA INSTALAR OPENSSH**
-Primero hemos hecho un sudo apt update para actualizar el sistema, despues hemos hecho un sudo apt upgrade para que se instalen todas las versiones mas recientes de todos los paquetes que tenemos ya instalados.
-Cuando ya hayamos hecho esos dos pasos ya podemos empezar a instalar el servicio SSH
-Empezamos poniendo en la terminal el comando: sudo apt install openssh-server, cuando le demos a enter nos pondra la opcion S/N que significa si queremos confirmar que se instale o no, en mi caso le doy a S para que se instale
-Ya lo tendriamos instalado, para comprobar que se ha instalado correctamente hacemos un sudo systemctl status SSH. Si Active: active (running) significa que SSH está funcionando correctamente. 


**SSH y creacion de claves**
SSH en Ubuntu Server
1. ¿Qué es SSH?

SSH (Secure Shell) es un protocolo que permite conectarse y administrar un equipo de forma remota y segura mediante una terminal.

En este caso utilizaremos SSH para conectarnos desde un ordenador cliente a un Ubuntu Server.

┌──────────────┐                 ┌──────────────┐
│    Cliente   │      SSH        │ Ubuntu Server│
│              │ ──────────────► │              │
│     ssh      │                 │     sshd     │
└──────────────┘                 └──────────────┘


Por defecto, SSH utiliza el puerto 22.

2. Instalar SSH en Ubuntu Server

Primero actualizamos los paquetes:

sudo apt update


Instalamos el servidor SSH:

sudo apt install openssh-server


Comprobamos que el servicio está funcionando:

sudo systemctl status ssh


Si aparece:

Active: active (running)


significa que SSH está funcionando correctamente.

También podemos hacer que se inicie automáticamente al arrancar el servidor:

sudo systemctl enable ssh

3. Conectarse al servidor

Desde el ordenador cliente utilizamos:

ssh usuario@IP_DEL_SERVIDOR


Por ejemplo:

ssh alumno@192.168.1.100


La primera vez que nos conectemos puede aparecer un mensaje preguntando si confiamos en el servidor. Escribimos:

yes


Después se solicitará la contraseña del usuario.

4. Configuración de SSH

El archivo principal de configuración del servidor es:

/etc/ssh/sshd_config


Podemos editarlo con:

sudo nano /etc/ssh/sshd_config


Por ejemplo, podemos cambiar el puerto:

Port 22


por:

Port 2222


Después debemos comprobar que la configuración es correcta:

sudo sshd -t


Y reiniciar SSH:

sudo systemctl restart ssh


Si hemos cambiado el puerto, tendremos que indicarlo al conectarnos:

ssh -p 2222 usuario@IP_DEL_SERVIDOR


Para una práctica de clase, podemos mantener el puerto 22 para simplificar la configuración.

5. Claves SSH

SSH permite utilizar claves públicas y privadas para autenticarnos sin tener que introducir la contraseña del usuario cada vez.

Tenemos dos claves:

Clave privada

Es la clave secreta.

Se guarda en nuestro ordenador y no debemos compartirla.

Por ejemplo:

~/.ssh/id_ed25519

Clave pública

Es la clave que podemos copiar al servidor.

Por ejemplo:

~/.ssh/id_ed25519.pub


La relación entre ambas es:

       CLIENTE
┌─────────────────────┐
│                     │
│  🔑 Clave privada   │
│  🔓 Clave pública   │
│                     │
└──────────┬──────────┘
           │
           │ clave pública
           ▼
┌─────────────────────┐
│    UBUNTU SERVER    │
│                     │
│  ~/.ssh/             │
│  authorized_keys    │
└─────────────────────┘


La clave privada permanece en el cliente y la clave pública se instala en el servidor.

6. Crear las claves

En el ordenador cliente podemos crear un par de claves con:

ssh-keygen -t ed25519


El programa nos preguntará dónde guardar las claves.

Podemos pulsar Enter para utilizar la ubicación predeterminada:

~/.ssh/id_ed25519


Se crearán dos archivos:

id_ed25519
id_ed25519.pub


id_ed25519 → clave privada.

id_ed25519.pub → clave pública.

Es recomendable proteger la clave privada utilizando una passphrase.

7. Copiar la clave pública al servidor

Podemos copiar la clave pública utilizando:

ssh-copy-id usuario@IP_DEL_SERVIDOR


Por ejemplo:

ssh-copy-id alumno@192.168.1.100


La clave se guardará en el servidor dentro de:

~/.ssh/authorized_keys


A partir de ese momento podemos conectarnos:

ssh alumno@192.168.1.100


SSH utilizará nuestra clave para comprobar nuestra identidad.

8. ¿Cómo funcionan las claves?

El funcionamiento básico es:

1. El cliente inicia la conexión
              │
              ▼
2. El servidor comprueba si
   existe una clave pública
              │
              ▼
3. El cliente demuestra que
   posee la clave privada
              │
              ▼
4. El servidor verifica la prueba
   utilizando la clave pública
              │
              ▼
5. Usuario autenticado


La clave privada nunca se envía al servidor.

Por eso es muy importante protegerla y no compartirla.




Clave pública → se copia al servidor.

