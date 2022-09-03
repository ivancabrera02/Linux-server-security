# Linux-server-security
With this guide you can keep your Linux servers safe

## Seguridad física
Existen muchas herramientas para saltarse la seguridad de tus servidores de forma física. Un Pendrive, un cable OMG, un CD etc. Asi que no tengas tus servidores al alcance de cualquiera.

### BIOS/UEFI
* Edición de opciones de protegida por contraseña.
* Deshabilitar la selección de medio de arranque.
* Deshabilitar, si es posible, el arranque por USB y tarjetas de red, así si el atacante tiene acceso a los periféricos se encontrará una barrera extra.

### GRUB
Desde el GRUB se puede obtener acceso a root fácilmente, por lo que tenemos que añadir una contraseña maestra al GRUB. Esta contraseña estará cifrada en MD5 o si es posible pbkdf2. Otra recomendación sería cifrar los discos por si el atacante apaga los servidores y se lleva los discos.

### Buenas prácticas
* Variable de entorno TMOUT
Por defecto en las distribuciones que usan bash existe una varible llamada TMOUT que está deshabilitada inicialmente. Su contenido consiste en cerrar una sesión inactiva en el tiempo.

* Evitar el reinicio y apagado accidental 
(chmod a-x /etc/acpi/powerbtn-acpi-support.sh)

## Protección perimetral

### Iptables
Iptables es una aplicación que permite el gestión, configuración y el manejo de filtrado de paquetes.

### VPN
A RELLENAR

### Monitorización a nivel de la red
A RELLENAR

## Protección de la red interna

### Spoofing o suplantación de identidad
En muchos casos se confía en que los usuarios van a realizar un uso correcto de la misma. Los ataques más comunes son MITM o DDos pero existen más tipos.

* ARP Poisoning
Este ataque consiste, como su nombre indica, en envenenar las tablas de cache ARP de las máquinas víctimas. Envenenando la caché ARP es posible conseguir un entorno MITM.

* Protección contra ARP Poisoning
Queda comprobado que un ataque MITM aprovechando la funcionalidad ARP es muy peligroso si se lleva a cabo de forma transparente consiguiendo que el usuario no sospeche que está siendo atacado.

* Entradas ARP estáticas
La más obvia de ellas es la utilización de entradas ARP estáticas en las máquinas cliente para que estas no puedan ser atacadas con éxito. Se trata de un método muy simple y fácil de llevar a cabo solo si se está en un entorno pequeño.

* Monitorización de ARP
Como se comentaba con anterioridad, no siempre resulta posible o viable mantener entradas estáticas ARP en las máquinas de un entorno. Si fuese posible, sin duda se estaría evitando el ataque pero el administrador de la red puede que no supiera que está habiendo intentos de ataques ARP. Es por ello que existen aplicaciones que se ejecutan como demonios en las máquinas de una red que detectan si se está sufriendo un ataque ARP Poisoning, evitándolo y notificándolo al administrador de la red.

* Utilización del fichero /etc/hosts
Si las entradas ARP estáticas resultaban difíciles de mantener, es posible que establecer una equivalencia IP - Nombre sea aún más complicado.

* DHCP Spoofing
Este ataque consiste en implementar un servidor DHCP alternativo con la finalidad de asignar direcciones IP a aquellas máquinas que las adquieran de forma automática.
A RELLENAR

* ICMP Redirect
A RELLENAR

## Protección de la capa de aplicación

### Jaulas con chroot
Si una aplicación, PDF u otro archivo está infectado puede traer consecuencias graves. Para ello podríamos usar una máquina virtual o Sandbox.
Como su nombre hace pensar, con chroot se modifica la raíz del sistema para un determinado proceso, haciéndole creer que realmente esa es la raíz de ficheros del
sistema. Esto supone que el proceso ejecutado dentro de una jaula chroot “verá” solo lo que se quiera que vea. Inicialmente una jaula es una carcasa totalmente vacía, sin contenido alguno y será necesario poner en ella los componentes justos y necesarios para permitir la ejecución de una aplicación. Antes de pasar a un ejemplo de chroot es necesario conocer algunas particularidades y recomendaciones para su uso:

* Nunca utilizar chroot para un proceso ejecutado como root. No tiene ningún sentido hacerlo así pues el usuario root siempre podrá salir de la jaula al sistema de ficheros real.
* Incluir en la jaula los componentes mínimos necesarios para la ejecución de un proceso. En el ejemplo se verá cómo conocer dichos componentes y cómo agregarlos a la jaula.
* Mantener los permisos de los ficheros lo más restrictivos posibles para no conceder a un potencial atacante ni el mínimo ápice de oportunidad para modificar ficheros a su antojo, ejecutables, etc.
* Nunca utilizar una jaula chroot como único método de seguridad implementado. No servirá de nada en absoluto.

### Limitación de recursos
En cualquier tipo de entorno, ya sea en producción o de pruebas, resulta muy importante controlar los recursos que se utilizan en una máquina. En numerosas ocasiones las configuraciones por defecto o los límites que se establecen no son suficientes.

### Establecer una política de contraseñas
Las contraseñas caducarán cada "x" tiempo.
Un mínimo de caracteres, números, caracteres especiales.
Intentos máximos para acceder.

### Cuotas de almacenamiento
Cuando un disco o partición llega al máximo de su capacidad el sistema, dependiendo de la situación, dejará de responder y prestar sus servicios si no se libera espacio. Para prevenir estas situaciones se utilizan las llamadas cuotas, que no son otra cosa que el establecimiento de límites de almacenamiento para usuarios o grupos.

### Port-Knocking
Es es un mecanismo que permite crear una capa de seguridad por oscuridad, es decir ayuda a proteger un recurso de un sistema mediante su ocultación. 
Por ejemplo con el puerto SSH para tratar de securizar dicho entorno, se desea evitar que escaneos de red, con nmap por ejemplo, desvelen que esa máquina
dispone de un servicio SSH. La idea consiste en que inicialmente el puerto utilizado por SSH esté bloqueado mediante reglas de firewall y solo cuando un usuario desee acceder se le conceda el acceso exclusivamente a él.

### SPA, Single Packet Authorization
A pesar de que Port-Knocking puede resultar útil a priori, es un concepto demasiado básico y si algún atacante adquiere la secuencia de paquetes utilizada podrá terminar habilitando un acceso al servidor SSH. Por supuesto, después tendría que lidiar con la seguridad del servicio SSH pero esta primera capa la habría roto fácilmente. Puede considerarse como una variante de Port-Knocking pero sin embargo no se envía una secuencia de paquetes sino un solo único paquete con información cifrada. Esto añade privacidad y autenticación.

###  HIDS, Host-based Intrusión Detection System
Es un sistema o serie de mecanismos por el cual se detectan cambios, accesos e irregularidades en una máquina. El beneficio que obtiene un administrador de
sistemas utilizando este tipo de software es que será alertado e incluso puede que no se precise de su interacción para solventar un potencial problema detectado.

## Entorno de desarrollo

### MySQL
* Dirección de escucha
Si añadimos el parámetro (bind-address=127.0.0.1) conseguimos bloquear los accesos que no vengan de localhost.
* Carga de ficheros locales
Por defecto, desde la consola de MySQL podría realizarse lo siguiente: mysql> select load_file('/etc/passwd');
Para deshabilitar la carga de ficheros desde MySQL y evitar una posible fuga de información desde el sistema de ficheros mediante un SQLi, es necesario establecer la siguiente configuración en el fichero (/etc/mysql/my.cnf)
* Renombrar el usuario root
mysql> update mysql.user set user='ivan' where user='root';
mysql> flush privileges;
* Comprobar existencia de usuarios anónimos
En numerosas instalaciones de MySQL se crean usuarios anónimos por defecto que podrían ser utilizados para extraer información. Para solucionarlo:
mysql> select user usuarios,host from mysql.user where user='''';
* Controlar los privilegios de los usuarios
* mysql_secure_installation
Se trata de un script encargado de asegurar determinados aspectos de MySQL. En concreto buscará usuarios anónimos, establecerá el password del usuario administrador, eliminará las bases de datos test, etc. Es recomendable ejecutar este script, ya sea antes o después de las configuraciones ya mencionadas.

### PHP
* expose_php
Si un atacante realiza 'fingerprinting' puede obtener la versión de PHP utilizada. Para evitarlo en el fichero /etc/php/apache2/php.ini y añadimos (expose_php= Off)
* displayerrors
Para deshabilitar la salida de errores que puedan mostrar información sensible (display_errors= Off)
* openbasedir
Uno de los ataques más comunes en aplicaciones web son los llamados Path Traversal. El ejemplo más clásico para esto consiste en tratar de conseguir ficheros con información sensible como / etc/passwd. Para evitar esto (open_basedir= /var/www)
* disablefunctions
Es posible deshabilitar determinadas funciones de PHP. Esto puede resultar útil en situaciones en las que un atacante consigue inyectar algún tipo de shell.
(disable_functions= phpinfo, exec, system, eval, shell_exec, ini_set)
* Deshabilitar RFI
En numerosas ocasiones existen scripts mal programados que mediante parámetros GET o POST permitirían la inclusión de código malicioso por parte de un atacante. Existen dos opciones para deshabilitar este comportamiento, tanto para incluir código de un script, como para abrir ficheros.
(allow_url_fopen= Off)
(allow_url_include= Off)

### Seguridad en el protocolo SSH
* Directivas básicas
En primer lugar se trata el fichero sshdconfig:
* Port: Indica en que puerto se colocará a la escucha el servicio SSH, por defecto el 22 pero podemos cambiarlo.
* PermiiRootLogin: Con esta directiva se prohíbe que un usuario se loguee en el servidor con el usuario root. De este modo se evita ataques de fuerza bruta al usuario root.
* MaxAuthTries: Esta directiva evita que los ataques de fuerza bruta puedan estar probando indefinidamente credenciales. 
* LoginGraceTime: Esta directiva indica el tiempo máximo, en segundos, para introducir las credenciales en la autenticación.
* AllowGroups: Esta directiva especifica el nombre de los grupos a los que pertenecen los usuarios que pueden iniciar sesión de manera remota mediante el protocolo SSH.
* AllowUsers: Se puede especificar en el fichero de configuración seguida de una lista de usuarios que pueden iniciar sesión mediante el protocolo SSH.
* Ciphers: Esta directiva especifica que cifrados admitirá la versión del protocolo.
* TCPKeepAlive: Deshabilitar esta directiva permite prevenir ataques de suplantación, ataques de tipo spoofing.
* DenyGroups: Esta directiva es similar a la de AllowGroups pero con un enfoque de denegación.
* DenyUsers: Contraria a la directiva AllowUsers, todo usuario que se encuentre asociado a esta directiva no podrá iniciar sesión de manera remota mediante el protocolo SSH.

* Fail2ban
Su función es penalizar la conexión, ya sea por medio de un bloqueo, de un origen que intenta realizar un proceso de fuerza bruta. En otras palabras, cuando una dirección IP o varias intentan realizar un ataque de fuerza bruta sobre un servicio, como puede ser FTP, SSH, etcétera, esta aplicación detectará y penalizará dichos comportamientos.


### Escalada de privilegios
En Linux existen muchas formas de obtener privilegios, vamos a comentar algunas de ellas:
* Por SUDO
* Desbordamiento del buffer
* Permiso de escritura en el archivo passwd
* Capabilities
* Permisos especiales
* Exploits
* Tarea CRON

