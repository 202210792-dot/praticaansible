Tarea 3 y 4  Automatizado con Ansible + GitHub
Sistema Operativo: openSUSE 16.0


--------------------------------------------------------------------------------
A) CLONAR EL REPOSITORIO
   Descargue el proyecto y acceda al directorio de trabajo:

   git clone https://github.com/202210792-dot/praticaansible.git

B) INSTALAR COLECCIONES REQUERIDAS EN EL NODO CENTRAL

   sudo zypper install ansible
   ansible-galaxy collection install community.general community.mysql ansible.posix

C) CONFIGURAR ACCESO SSH SIN CONTRASEÑA HACIA EL SERVIDOR DESTINO
   Crea una llave digital para que Ansible se conecte al servidor automáticamente sin 
   pedir clave en cada paso, (reemplaza `virginia` y `192.168.1.66` por el usuario y la IP de tu servidor):

   ssh-keygen -t ed25519 -f ~/.ssh/id_ansible -C "ansible-control" -N ""
   ssh-copy-id -i ~/.ssh/id_ansible.pub virginia@192.168.1.66

D) CONFIGURAR EL ARCHIVO DE INVENTARIO
   Abre el archivo `inventory.ini` y reemplaza la IP de ejemplo por la dirección IP real de tu servidor:

   [opensuse_servers]
   servidor_opensuse ansible_host=192.168.1.66

E) CREDENCIALES  
   Todos los usuarios y contraseñas del sistema (SSH, administrador, MariaDB, Samba y SQL)
   están guardados en el archivo:'group_vars/all/credenciales.yml'.


F) VERIFICAR CONECTIVIDAD Y ESCALACIÓN DE PRIVILEGIOS
   Verifica que tu máquina pueda conectarse y comunicarse correctamente con el servidor openSUSE:

   ansible opensuse_servers -m ping
   ansible opensuse_servers -m command -a "whoami" --become

G) EJECUTAR EL PLAYBOOK PRINCIPAL (DESPLIEGUE)
   Revisa que el código no tenga errores de escritura y luego inicia la instalación automática en el servidor:

   ansible-playbook site.yml --syntax-check
   ansible-playbook site.yml


H) COMPROBAR Y VALIDAR LOS SERVICIOS DESPLEGADOS
   Cuando termine la instalación sin errores, haz estas pruebas desde tu computadora principal
   para confirmar que todo quedó bien:

   # 1. Verifica que todos los servicios estén activos y funcionando correctamente:

   ansible opensuse_servers -m shell -a "systemctl is-active apache2 mariadb smb nmb vsftpd" --become



   # 2. Verificar que Apache y PHP responden por HTTP (cambia por tu IP destino)

   curl -I http://192.168.1.66/info.php

   curl -I http://192.168.1.66/phpMyAdmin/
      
      * Credenciales de acceso a phpMyAdmin:
            - Usuario: virginia_user
            - Contraseña: aso_good_26!


   # 3. Verificar que Samba lista el recurso compartido con el usuario creado
   
   sudo zypper install -y samba-client
   
   smbclient -L //192.168.1.66 -U virginia_samba
   
   * Credenciales de acceso a Samba:
            - Usuario: virginia_samba
            - Contraseña: mundolibre
