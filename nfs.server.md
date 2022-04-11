# Servidor NFS

## Objetivo 

Instalar y configurar un servidor de NFS (Network File System) para compartir directorios mediante el protocolo.

## Representación del despliegue

```bash
                      192.168.108.0/24
                             │
┌───────────────────────┐    │    ┌─────────────────┐
│    192.168.108.131    ├────┴────┤ 192.168.108.252 │
│      servidor NFS     |         |   cliente NFS   |
|comparte /home/nfsshare│         └─────────────────┘
└───────────────────────┘
```

## Instalación

```bash
dnf -y install nfs-utils
```

## Configuración

```
sed -i.org -e 's/^#Domain.*.edu$/&\nDomain = my-vmware.net/' /etc/idmapd.conf
/home/nfsshare 192.168.108.0/24(rw,no_root_squash)
mkdir -p /home/nfsshare
systmectl enable --now rpcbind nfs-server
```

### Firewalld activado

```bash
firewall-cmd --add-service=nfs --permanent
```

# Cliente NFS

## Instalación

```bash
dnf -y install nfs-utils
```

## Configuración

```bash
sed -i.org -e 's/^#Domain.*.edu$/&\nDomain = my-vmware.net/' /etc/idmapd.conf
```

## Montando directorio desde el servidor

```bash
mount -t nfs 192.168.108.131:/home/nfsshare /mnt
```

### Verificando

```bash
df -hT
```
# Notas

- Referencias: Mas referencias de NFS pueden consultarse [aquí][references.nfs.server] para el servidor de NFS, y en [este para el cliente NFS][references.nfs.client] (montar desde `/etc/fstab` y `autofs`)

[references.nfs.server]: https://www.server-world.info/en/note?os=CentOS_Stream_8&p=nfs&f=1 "Servidor NFS" 
[references.nfs.client]: https://www.server-world.info/en/note?os=CentOS_Stream_8&p=nfs&f=2 "Cliente NFS"
