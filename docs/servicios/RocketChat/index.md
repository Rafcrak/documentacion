# Configuración del Servicio RocketChat

## Autentificación en RocketChat con Active Directory

Vamos a configurar el servicio RocketChat para que pueda utilizar la autentificación del Active Directory.

### Creación del usuario enlazador en el Active Directory

- Tenemos que crear un usuarios en el dominio en la carpeta Users (Aunque podría crease en cualquier otra unidad organizativa).

![](./assets/Screenshot_1.png)
F

- Importante que el usuario tenga el mismo **Nombre Completo** que el **Nombre de Inicio de Sesión**:
  F
  ![](./assets/Screenshot_2.png)

### Configuración del RocketChat

- En la configuración del Rocket Chat nos vamos al apartado de **LDAP** el cual lo habilitaremos y lo configuraremos poniendo que el tipo de servidor es de Directorio Activo, la IP del servidor y su puerto por el que establecerá la conexión:

![](./assets/Screenshot_3.png)

- Nos autenticamos con el usuario que creamos anteriormente, poniendo la ruta completa del esquema:

![](./assets/Screenshot_4.png)

- Editamos el filtro de búsqueda de los usuarios poniendo la **Base DN** nuestro dominio, editamos el filtro escribiendo el siguiente **' (&(objectclass=user)(!(objectclass=computer))) '**, por último el campo de búsqueda le ponemos **sAMAccountName**:

![](./assets/Screenshot_5.png)

- Le ponemos el dominio predeterminado:

![](./assets/Screenshot_6.png)

- Con la configuración terminada lo guardamos y sincronizamos:

![](./assets/Screenshot_7.png)

- Hacemos una conexión de prueba:

![](./assets/Screenshot_8.png)

- Probamos a buscar un usuario:

![](./assets/Screenshot_9.png)

- Vamos a inciar sesión con un usuario del dominio (en mi caso probare con el siguiente usauario):

![](./assets/Screenshot_10.png)

![](./assets/Screenshot_11.png)

![](./assets/Screenshot_12.png)
