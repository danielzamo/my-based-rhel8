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

- PXE boot (arranque por red). 
  - Configuración servidor PXE
  - Instalación vía red - arranque bios 
  - Instalación vía red - arranque UEFI

## PXE boot : Configuración servidor PXE

```bash
dnf -y install tftp-server 
systemctl enable --now tftp.socket 
firewall-cmd --add-service=tftp --permanent 
firewall-cmd --reload
```

### Servidor DHCP

#### Instalar servidor DHCP

```bash
dnf -y install dhcp-server
```

Para el servicio de DHCP se edita el fichero `/etc/dhcp/dhcpd.conf`. 

Archivo: [dhcpd.conf](./etc/dhcp/dhcpd.conf)

```bash
# Se editan diferentes ficheros. Los mismos son compartidos en este mismo repositorio. En este paso se edit el:
## /etc/dhcp/dhcpd.conf
systemctl restart dhcpd
```


## Se adjunta a continuación el historial de comandos ejecutados.

Este trabajo aun no esta correctamente documentado.

```bash
systemctl disable --now sssd nis-domainname auditd
sed -i.org -e 's/=enforcing/=disabled/g' /etc/selinux/config 
# < Conf. PXE (Preboot eXecution Environment) >
dnf -y install tftp-server 
systemctl enable --now tftp.socket 
 firewall-cmd --add-service=tftp --permanent 
firewall-cmd --reload
#<setting server DHCP>
dnf -y install dhcp-server 
dnf install vim-enhanced
vim /etc/profile
source /etc/profile
vi /etc/dhcp/dhcpd.conf 
systemctl enable --now dhcpd
firewall-cmd --add-service=dhcp --permanent 
firewall-cmd --reload 

# <PXE Boot : Network Installation>
dnf -y install syslinux
cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot/ -v
mkdir -p /var/pxe/rocky-linux-8
mkdir -p /var/lib/tftpboot/rocky-linux-8
mkdir -p /var/lib/isos
dnf install rsync
mount -t iso9660 -o loop,ro /var/lib/isos/Rocky-8.5-x86_64-minimal.iso /var/pxe/rocky-linux-8
lsblk 
df -h
cp /var/pxe/rocky-linux-8/images/pxeboot/{vmlinuz,initrd.img} /var/lib/tftpboot/rocky-linux-8/
cp /usr/share/syslinux/{menu.c32,vesamenu.c32,ldlinux.c32,libcom32.c32,libutil.c32} /var/lib/tftpboot/ -v
mkdir /var/lib/tftpboot/pxelinux.cfg
vi /var/lib/tftpboot/pxelinux.cfg/default 
mount -t iso9660 -o loop,ro /var/lib/isos/Rocky-8.5-x86_64-minimal.iso /var/pxe/rocky-linux-8
# <install httpd>
dnf -y install httpd
mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.conf.org -v
vim /etc/httpd/conf/httpd.conf 
cp /etc/httpd/conf/httpd.conf{,.org}
vim /etc/httpd/conf/httpd.conf
systemctl enable --now httpd
firewall-cmd --add-service=http --permanent && firewall-cmd --reload 
# </install httpd>
vi /var/www/html/index.html 
vi /etc/hosts
vi /etc/httpd/conf.d/pxeboot.conf
systemctl restart httpd
mkdir /var/www/html/ks
vi /var/www/html/ks/rocky-linux-8-ks.cfg 
printf "%02X" 10; printf "%02X" 0; printf "%02X" 0; printf "%02X\n" 21
vi /var/lib/tftpboot/pxelinux.cfg/0A000015
```
