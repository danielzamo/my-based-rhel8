# My Rocky Linux

## Objetivo

En este repositorio comparto configuraciones de servicios y/o despliegues realizados sobre sistemas basados en la distribución Red Hat Enterprise Linux (RHEL) en su versión 8.

Las configuraciones mostradas aquí, deberían valer para sistemas Linux basados en RHEL versión 8 (CentOS Strean 8, Oracle Linux 8, CentOS 8, Alma Linux 8, Rocky Linux 8, etc).

**Consideraciones iniciales:** Todas las tareas para este repositorio y salvo que se indique expresamente, han sido realizadas sobre un servidor con Rocky Linux versión 8 (con las últimas actualizaciones aplicadas), y ejecutadas por el usuario normal, escalando a `root` mediante el comando `sudo`. Y se comparte aquí:

- la indicación de los paquetes que se fueron instalando (según el servicio a configurar). 
- se comparten los ficheros modificados y/o creados, hasta que el servicio quedo funcional. Se intenta en todos los casos, compartir las diferentes versiones de los ficheros que han sido modificados.

## Especificación del sistema utilizado

- Distribución Linux: Rocky Linux versión 8. Con los últimos parches aplicados.
- La instalación inicial, era solo con el metapaquete "Minimal"
- IP: `10.0.0.30/24`
- RAM: `2G`
- CPU - ALU: `1`

## Configuración servicios

Actualmente en este repo comparto:

- Actualizando el sistema e instalando algunos paquetes
- PXE boot (arranque por red). 
  - Configuración servidor PXE
  - Instalación vía red - arranque bios/estandar
  - Instalación vía red - arranque UEFI

## Actualizando el sistema e instalando algunos paquetes

```bash
sudo dnf -y update
sudo dnf -y install wget rsync vim-enhanced
```

## PXE boot : Configuración servidor PXE

```bash
sudo dnf -y install tftp-server 
sudo systemctl enable --now tftp.socket 
sudo firewall-cmd --add-service=tftp --permanent 
sudo firewall-cmd --reload
```

### Servidor DHCP

#### Instalar servidor DHCP (isc-dhcp)

```bash
sudo dnf -y install dhcp-server
```

Se edita el fichero `/etc/dhcp/dhcpd.conf`. 

Ver el archivo [dhcpd.conf, compartido aquí](./etc/dhcp/dhcpd.conf) (_Este es la versión final actual_).

```bash
sudo systemctl systemctl enable --now dhcpd
sudo firewall-cmd --add-service=dhcp --permanent
sudo firewall-cmd --reload
```

**_Nota servicio DHCP:_** Las leases asignados pueden verse en:

```bash
sudo cat /var/lib/dhcpd/dhcpd.leases
```

## PXE boot : Instalación vía red - arranque bios/estandar

```bash
sudo dnf -y install syslinux
sudo cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot/
sudo mkdir -p /var/pxe/rocky-linux-8
sudo mkdir /var/lib/tftpboot/rocky-linux-8
sudo mkdir /var/lib/isos
sudo wget -O /usr/local/src/Rocky-8.5-x86_64-minimal.iso \
    https://download.rockylinux.org/pub/rocky/8/isos/x86_64/Rocky-8.5-x86_64-minimal.iso
sudo mount -t iso9660 -o loop,ro /var/lib/isos /var/pxe/rocky-linux-8
sudo cp /var/pxe/centos-st8/images/pxeboot/{vmlinuz,initrd.img} \ 
    /var/lib/tftpboot/rocky-linux-8/
sudo cp /usr/share/syslinux/{menu.c32,vesamenu.c32,ldlinux.c32,libcom32.c32,libutil.c32} \ 
    /var/lib/tftpboot/
sudo mkdir /var/lib/tftpboot/pxelinux.cfg
sudo vim /var/lib/tftpboot/pxelinux.cfg/default
```

Ver el archivo [.../default, compartido aquí](./var/lib/tftpboot/pxelinux.cfg/default) (_Este es la versión final actual_).

### Servidor http

# Faltantes aun ....

- instalar http
- configurar http
- activar instalaciones desatendidas
- instalar haciendo IP estatico
