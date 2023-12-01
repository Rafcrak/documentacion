# Configuración del Servidor Minecraft en Debian

## Preparación del Servidor

Para crear un servidor del Minecraft lo primero es configurar una IP fija en el servidor desde el siguiente archivo **/etc/network/interfaces**:

```

# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
allow-hotplug ens18 # Que se conecte automática mente la tarjeta de red ens18
#iface ens18 inet dhcp (Comento la línea para quitar el DHCP)
# This is an autoconfigured IPv6 interface
iface ens18 inet6 auto
iface ens18 inet static # Pongo la  tarjeta de red en estática
address 192.168.1.251/24 # Pongo la IP fija + máscara de red
gateway 192.168.1.1 # Y la puerta de enlace

```

Luego nos vamos instalamos en el servidor el Java:

```

apt install default-jre default-jdk

```

Una vez instalado el java nos vamos la carpeta **/opt** y dentro creamos la carpeta donde alojaremos el servidor:

´´´

mkdir \*nombre del servidor\*

´´´

## Instalar servidor Spigot
