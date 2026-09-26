---
authors: Daniel Bazo Correa
description:
    Plataforma de virtualización Proxmox, actualización de PVE, creación de máquinas
    virtuales y comparación con contenedores.
title: Proxmox
---

!!! warning

    Contenido transcrito automáticamente a partir de apuntes manuscritos. Puede
    contener errores de lectura y está pendiente de revisión.

## Definición

Proxmox es una plataforma de virtualización que también permite crear contenedores. La
principal diferencia respecto a otras alternativas es su interfaz web (_webOS_).

## Actualizar PVE (Proxmox Virtual Environment)

Si no usamos la versión _enterprise_ de Proxmox, podemos utilizar el repositorio de
"No-Subscription". Para ello:

Datacenter (panel lateral) → pve → Updates → Repositorios.

Dentro de Repositorios, pulsamos en Add → OK → No-Subscription → Add. Luego, en la
pestaña Updates, seleccionamos Refresh y cerramos la ventana que aparece. A continuación
pulsamos en Upgrade. Esto actualizará el servidor; luego lo reiniciamos.

> No utilizar este repositorio para producción a nivel empresarial.

## Crear una máquina virtual

Podemos copiar el enlace (URL) de la ISO del sistema operativo a descargar, por ejemplo
Debian. Para descargarla, vamos al disco local del nodo PVE y seleccionamos ISO Images.
Luego, en Download from URL, empezará a descargar la ISO.

Con ello, pulsamos en Create VM y creamos la máquina virtual siguiendo los pasos que se
indican.

Podemos crear discos asociados a cada máquina virtual desde la opción Disk. En LVM se
indica el porcentaje del disco que se usará para la máquina virtual, pero no indica el
porcentaje de ocupación del disco. Eso se consulta en LVM-Thin.

## Contenedores frente a máquinas virtuales

Una de las diferencias es que las máquinas virtuales se pueden migrar en tiempo real de
un servidor a otro, sin necesidad de apagar o cortar el servicio. Esto no ocurre con los
contenedores.

Aparte, existen también diferencias en el uso de recursos.

## Comandos y conceptos sueltos

- **iommu**: permite hacer _PCIe passthrough_.
- `ip addr show`: ver la IP en Linux.

```bash
scp directorio-host nombre@ip:directorio
```

Comando para pasar archivos por SSH.

```bash
sudo usermod -aG docker $USER
```

Añadir un usuario al grupo `docker` (permisos).

## Contenido eliminado por estar ya en la wiki

No se encontró solape con la wiki: la wiki no tiene ninguna sección de virtualización ni
de hipervisores, y el capítulo de contenedores
(`docs/06_operations/section_1_containers.md`) distingue contenedores de máquinas
virtuales de forma general pero no cubre Proxmox, PVE, la creación de VMs, `iommu`/_PCIe
passthrough_, ni los comandos incluidos en este apunte.

## Procedencia

Cursos/Data Engineer/Apuntes.pdf, páginas 5 a 7. No se eliminó contenido: no se encontró
solapamiento con la wiki publicada.
