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

- [Actualizando el sistema e instalando algunos paquetes](./paquetes.previos.md)
- PXE boot (arranque por red). 
  - Configuración servidor PXE
  - Instalación vía red - arranque bios/estandar
  - Instalación vía red - arranque UEFI
