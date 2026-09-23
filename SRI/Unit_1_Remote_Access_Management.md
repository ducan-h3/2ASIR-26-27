INTALAR OPENSSH
-Primero hemos hecho un sudo apt update para actualizar el sistema, despues hemos hecho un sudo apt upgrade para que se instalen todas las versiones mas recientes de todos los paquetes que tenemos ya instalados.
-Cuando ya hayamos hecho esos dos pasos ya podemos empezar a instalar el servicio SSH
-Empezamos poniendo en la terminal el comando: sudo apt install openssh-server, cuando le demos a enter nos pondra la opcion S/N que significa si queremos confirmar que se instale o no, en mi caso le doy a S para que se instale
-Ya lo tendriamos instalado, para comprobar que se ha instalado correctamente hacemos un sudo systemctl status SSH. Si vemos que el circulo esta en verde y pone enable lo habriamos instalado correctamente 

claves

