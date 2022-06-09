# Documentación de Rafael Romera Navarro

## Instalación y Configuración de LAPS

En esta guía vamos a ver como se instala y configura LAPS en el servidor Windows Server 2022, y la instalación automatizada en los clientes del dominio.

### Instalación en Windows Server 2022 

- El paquete de instalación de LAPS se encuentra en el siguiente [enlace](https://www.microsoft.com/en-us/download/details.aspx?id=46899), dentro de la página hacemos click en Download:

![](./imagenes/LAPS/pagDescarga1.png)

- Descargamos la versión de 64 bits (Aunque si tenemos equipos de 32 bits nos tendremos que descargar también la versión de 32 bits para la instalación de equipos clientes):

![](./imagenes/LAPS/pagDescarga2.png)

Ejecutamos el archivo de instalación:

![](./imagenes/LAPS/Inst1.png)

- Aceptamos los términos y condiciones:

![](./imagenes/LAPS/Inst2.png)

- Instalamos solo las características del servidor que son todas exepto la primera característica que es la del cliente:

![](./imagenes/LAPS/Inst3.png)

- Y con esto terminamos la instalación:

![](./imagenes/LAPS/Inst4.png)

### Configuración de LAPS en Windows Server 2022

- Una vez instalado tenemos que configurarlo para su funcionamiento, para ello tenemos que abrir una PowerShell y importar los módulos:

~~~~
Import-Module AdmPwd.PS
~~~~

- Importados los módulos tenemos que actualizar el esquema:
~~~~
Update-AdmPwdADSchema
~~~~

- Le asignamos el permiso para poder cambiar la contraseña a la unidad organizativa que en este caso será *Compras*:

~~~~
Set-AdmPwdComputerSelfPermission -OrgUnit Compras
~~~~

- Creamos un grupo para que tenga permisos para leer la contraseña de Administrador:

![](./imagenes/LAPS/conf1.png)

- En este caso el grupo se llamara *LAPS*:

![](./imagenes/LAPS/conf2.png)

- El grupo que hemos creado lo uniremos al grupo de administradores del dominio:

![](./imagenes/LAPS/conf3.png)

- Con los siguientes comandos le asignaremos el permiso de poder leer las contraseñas al grupo que hemos creado anteriormente a la unidad organizativa *Compras*:

~~~~
Set-AdmPwdReadPasswordPermission -OrgUnit "Compras" -AllowedPrincipals "LAPS"
Set-AdmPwdResetPasswordPermission -OrgUnit "Compras" -AllowedPrincipals "LAPS"
~~~~

- Por último vamos a crear una GPO a la unidad organizativa de Compras:

![](./imagenes/LAPS/conf4.png)

- La llamaremos *Configuración LAPS*:

![](./imagenes/LAPS/conf5.png)

- Editamos la GPO y no iremos a la siguiente ruta:

![](./imagenes/LAPS/conf6.png)

- En esta dirección tenemos las reglas que nos ofrece LAPS

    - **Password Setting:**  Es la directiva en la que se puede modificar cual va a ser la loSngitud y que tipo de caracteres va a tener la contraseña.

    - **Name of administrator account to manage:** Se utiliza cuando se ha cambiado el nombre de la cuenta del administrador, y se le refiere el nuevo nombre de dicha cuenta.

    - **Do not allow password expiration time longer than required by policy:** No permite que el tiempo de caducidad de la contraseña sea superior al requerido por la política.

    - **Enable Local Admin Password:** Habilita la cuenta de administrador.

- Habilitamos todas las opciones, la primera dejamos los ajustes por defecto, y en la segunda le vamos a poner *Compras_Admin*:

![](./imagenes/LAPS/conf7.png)

- Para que se cambie el nombre del Administrador nos vamos al siguiente directorio:

![](./imagenes/LAPS/conf8.png)

- Cambiamos el nombre de la cuenta del administador con el mismo nombre que pusimos en el LAPS y también activamos la directiva que activa la cuenta del Administador Local:

![](./imagenes/LAPS/conf9.png)

- Con esto el servidor ya estaría configurado.

### Instalación de LAPS de manera automática en el Cliente

- Lo primero que tenemos que hacer es crear una unidad compartida en mi caso se llamará *Programas* la cual tendrá todos los usuario con control total:

![](./imagenes/LAPS/InstCliente1.png)

- Dentro de la unidad compartida meteremos el archivo de [instalación del LAPS](#instalación-en-windows-server-2022 ) que nos descargamos al principio:

![](./imagenes/LAPS/InstCliente2.png)

- Creamos una GPO la cual la llamaremos *Instalar LAPS Clientes*:

![](./imagenes/LAPS/InstCliente3.png)

- La editamos y nos vamos a la siguiete ruta:

![](./imagenes/LAPS/InstCliente4.png)

- Añadimos un nuevo paquete:

![](./imagenes/LAPS/InstCliente5.png)

- Buscamos el instalador por la ruta de la unidad compartida:

![](./imagenes/LAPS/InstCliente6.png)

- Nos aparecerá el paquete:

![](./imagenes/LAPS/InstCliente7.png)

- Actualizamos las directivas tanto en el servidor como en el cliete con el siguiete comando de PowerShell:

~~~~
gpupdate /force
~~~~

- Nos pedirá que reiniciemos el Cliente Windows, una vez reiniciado nos vamos a Configuración/Aplicaciones para poder ver que se ha instalado correctamente:

![](./imagenes/LAPS/InstCliente8.png)

### Funcionamiento
- Una vez instalado y configurado en el servidor tenemos que abrir la aplicación **LAPS UI**:

![](./imagenes/LAPS/func1.png)

- En la casilla de *Computer name:* tenemos que poner el nombre del equipo que tengamos en la unidad organizativa de *Compras*, en mi caso es *ORD-COMPRAS* y le damos a buscar:

![](./imagenes/LAPS/func2.png)

- Como resultado nos dará la contraseña del usuario Administrador Local, cuando expira la contraseña y la opción de cambiar la fecha de expiración:

![](./imagenes/LAPS/func3.png)

- Para comprobar que funciona nos vamos a ir al cliente y nos vamos a identificar con el nuevo nombre y contraseña del Administrador:

![](./imagenes/LAPS/func4.png)

- Podremos ver que nos hemos identificado con el nuevo nombre y contraseña en la cuente de Administrador Local:

![](./imagenes/LAPS/func5.png)

Con esto ya estaría intalado y configurado LAPS.

## Instalación y Configuración del Proxy Traefik

En esta guia se verá como instalar y configurar Traefik con Docker Compose y ficheros de configuración .toml tanto estáticos como dinámicos.

### Creación del fichero Docker Compose y traefik.toml

- Para empezar tenemos que crear un red en docker para que todos los contenedores estén en esta nueva red, para ello usamos el siguiete comando:

~~~~
docker network create traefik
~~~~

- Ahora crearemos una carpeta en donde almacenaremos los ficheros de docker y traefik (en mi caso la carpeta se llamará traefik):

~~~~
mkdir traefik
~~~~

- Dentro de la carpeta que acabamos de crear, crearemos en primer lugar el fichero [docker-compose.yml](./files/docker-compose-traefik.yml) y en el esribiremos lo siguiente:

![](./imagenes/Traefik/traefik-docker-compose.png)

- Creamos el archivo de configuración [traefik.toml](./files/traefik.toml) que es el archivo de configuración estático:

![](./imagenes/Traefik/traefik.toml.png)

- Luego creamos la carpeta en donde almacenaremos los servicios:

~~~~
mkdir traefik/services
~~~~

- Un ejemplo de servicio [https](./files/proxmox.toml) es el siguiente:

![](./imagenes/Traefik/traefik-proxmox.png)

- Un ejemplo de servicio [TCP](./files/EscritorioRemoto.toml):

![](./imagenes/Traefik/traefik-Escritorio-Remoto.png)

### Configuración de MkDocs con traefik

-  Lo primero es crear otra carpeta separada de la carpeta de traefik en mi caso la llamaré documentos:

~~~~
mkdir documentos
~~~~

- Dentro de la carpeta crearemos un [docker compose](./files/docker-compose-mkdocs.yml) para el MkDocs, importante que la versión sea la misma que el archivo docker-compose del traefik.

- En esté archivo hay dos servicios, el primer servicio se encarga de darnos [páginas de error](https://github.com/tarampampam/error-pages) html personalizadas, y el segundo servicio es el MkDocs:

![](./imagenes/Traefik/mkdocs-docker-compose.png)

## Autentificación en RocketChat con Active Directory

Vamos a configurar el servicio RocketChat para que pueda utilizar la autentificación del Active Directory.

### Creación del usuario enlazador en el Active Directory

- Tenemos que crear un usuarios en el dominio en la carpeta Users (Aunque podría crease en cualquier otra unidad organizativa).

![](./imagenes/RockerChat/Screenshot_1.png)

- Importante que el usuario tenga el mismo **Nombre Completo** que el **Nombre de Inicio de Sesión**:

![](./imagenes/RockerChat/Screenshot_2.png)

### Configuración del RocketChat

- En la configuración del Rocket Chat nos vamos al apartado de **LDAP** el cual lo habilitaremos y lo configuraremos poniendo que el tipo de servidor es de Directorio Activo, la IP del servidor y su puerto por el que establecerá la conexión:

![](./imagenes/RockerChat/Screenshot_3.png)

- Nos autenticamos con el usuario que creamos anteriormente, poniendo la ruta completa del esquema:

![](./imagenes/RockerChat/Screenshot_4.png)

- Editamos el filtro de búsqueda de los usuarios poniendo la **Base DN** nuestro dominio, editamos el filtro escribiendo el siguiente **' (&(objectclass=user)(!(objectclass=computer))) '**, por último el campo de búsqueda le ponemos **sAMAccountName**:

![](./imagenes/RockerChat/Screenshot_5.png)

- Le ponemos el dominio predeterminado:

![](./imagenes/RockerChat/Screenshot_6.png)

- Con la configuración terminada lo guardamos y sincronizamos:

![](./imagenes/RockerChat/Screenshot_7.png)

- Hacemos una conexión de prueba:

![](./imagenes/RockerChat/Screenshot_8.png)

- Probamos a buscar un usuario:

![](./imagenes/RockerChat/Screenshot_9.png)

- Vamos a inciar sesión con un usuario del dominio (en mi caso probare con el siguiente usauario):

![](./imagenes/RockerChat/Screenshot_10.png)

![](./imagenes/RockerChat/Screenshot_11.png)

![](./imagenes/RockerChat/Screenshot_12.png)

