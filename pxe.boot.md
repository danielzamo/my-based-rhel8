# PXE boot : Configuración servidor PXE

```bash
sudo dnf -y install tftp-server 
sudo systemctl enable --now tftp.socket 
sudo firewall-cmd --add-service=tftp --permanent 
sudo firewall-cmd --reload
```

## Servidor DHCP

### Instalar servidor DHCP (isc-dhcp)

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

# PXE boot : Instalación vía red - arranque bios/estandar

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

## Servidor http

# Faltantes aun ....

- instalar http
- configurar http
- activar instalaciones desatendidas
- instalar haciendo IP estatico
