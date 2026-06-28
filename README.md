<div align="center">

# 🖥️ Operating Systems Handbook

### Guía práctica de administración de sistemas, virtualización y entornos Linux/Windows

[![Bash](https://img.shields.io/badge/Bash-4EAA25?style=for-the-badge&logo=gnubash&logoColor=white)](#)
[![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)](#)
[![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)](#)
[![WSL](https://img.shields.io/badge/WSL-0078D4?style=for-the-badge&logo=windows&logoColor=white)](#)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)](#)

[![License](https://img.shields.io/badge/license-MIT-blue.svg?style=flat-square)](LICENSE)
[![Maintained](https://img.shields.io/badge/maintained-yes-success.svg?style=flat-square)](#)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](#)

</div>

---

## 📖 Acerca de

Colección de comandos, procedimientos y chuletas (*cheatsheets*) para tareas habituales de administración de sistemas: instalación de entornos Linux (nativo, live USB y WSL), gestión de almacenamiento con LVM, monitorización de recursos y manejo de contenedores Docker.

Pensado como referencia rápida de copia y pega para el día a día.

---

## 📑 Tabla de contenidos

| # | Sección | Descripción |
|---|---------|-------------|
| 1 | [🪟 Instalación de WSL](#-1-instalación-de-wsl) | Subsistema de Linux para Windows |
| 2 | [🐧 Instalación Live de Linux](#-2-instalación-live-de-linux) | Creación de USB arrancable |
| 3 | [💾 Ampliación de disco (LVM)](#-3-ampliación-de-disco-lvm--ssd) | Extender volúmenes en una VM |
| 4 | [🔍 Monitorización del sistema](#-4-monitorización-del-sistema) | CPU, disco y contenedores |
| 5 | [🐳 Guía rápida de Docker](#-5-guía-rápida-de-docker) | Comandos esenciales |

---

## 🪟 1. Instalación de WSL

> Instala el *Windows Subsystem for Linux* (WSL) y una distribución Ubuntu en tu máquina con Windows.

**Paso 1 — Instalar WSL**

```bash
wsl --install
```

**Paso 2 — Reiniciar el equipo**

Necesario para que se completen la instalación de WSL y las actualizaciones del kernel.

```text
(Reinicia tu PC)
```

**Paso 3 — Listar distribuciones disponibles**

```bash
wsl --list --online
```

**Paso 4 — Instalar Ubuntu 24.04**

```bash
wsl --install -d Ubuntu-24.04
```

**Paso 5 — Abrir una shell interactiva en Ubuntu 24.04**

```bash
wsl -d Ubuntu-24.04
```

O, de forma abreviada:

```bash
wsl
```

---

## 🐧 2. Instalación Live de Linux

> Procedimiento para crear un USB arrancable con una distribución Linux (ejemplo: Ubuntu 24.04).

> [!CAUTION]
> El comando `dd` escribe a nivel de bloque y **borra todo el contenido del disco de destino sin posibilidad de deshacerlo**. Verifica el dispositivo (`/dev/sdX`) con `lsblk -fp` antes de cada paso. Un error de dispositivo puede destruir tu disco principal.

**Paso 1 — Detectar la ruta del USB**

```bash
lsblk -fp
```

**Paso 2 — Desmontar el USB (si está montado)**

```bash
sudo umount /dev/sda1
sudo umount /dev/sda2
```

**Paso 3 — Borrar la tabla de particiones del USB**

> ⚠️ Solo el USB, nunca tu SSD/disco principal.

```bash
sudo wipefs -a /dev/sda
```

**Paso 4 — Descargar una ISO de Linux**

Ejemplo con Ubuntu 24.04:

```bash
wget https://releases.ubuntu.com/24.04/ubuntu-24.04-desktop-amd64.iso -O ubuntu.iso
```

**Paso 5 — Escribir la ISO en el USB**

```bash
sudo dd if=ubuntu.iso of=/dev/sda bs=4M status=progress oflag=sync
```

**Paso 6 — Sincronizar y expulsar**

```bash
sync
sudo eject /dev/sda
```

Ya puedes retirar el USB de forma segura.

**✅ Comprobación final**

Al ejecutar de nuevo `lsblk -fp` deberías ver algo similar a:

```text
/dev/sda iso9660 Ubuntu 24.04 amd64
```

---

## 💾 3. Ampliación de disco (LVM + SSD)

> Procedimiento para añadir un nuevo disco a un volumen lógico existente en una VM Linux mediante LVM.

**1. Actualizar el sistema e instalar herramientas LVM**

```bash
sudo apt update
sudo apt install lvm2 -y
```

**2. Borrar datos y preparar el nuevo disco**

Elimina cualquier dato de sistema de archivos/particiones existente e inicializa el disco como volumen físico.

```bash
sudo wipefs --all /dev/nvme1n1
sudo sgdisk --zap-all /dev/nvme1n1
sudo pvcreate /dev/nvme1n1
```

**3. Añadir el nuevo disco al grupo de volúmenes**

Extiende el grupo de volúmenes (`ubuntu-vg`) para incluir el nuevo disco.

```bash
sudo vgextend ubuntu-vg /dev/nvme1n1
```

**4. Confirmar los cambios en el grupo de volúmenes**

```bash
sudo vgs
```

**5. Extender el volumen lógico con todo el espacio libre**

```bash
sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
```

**6. Redimensionar el sistema de archivos**

```bash
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
```

**7. Verificar los cambios**

```bash
df -h /
df -T /
```

**8. (Opcional) Ver detalle del volumen lógico**

```bash
sudo lvdisplay /dev/ubuntu-vg/ubuntu-lv
```

---

## 🔍 4. Monitorización del sistema

> Comandos rápidos para vigilar el uso de hardware, disco y contenedores específicos.

**Uso de hardware (CPU, memoria, procesos)**

```bash
htop
```

**Uso de disco**

```bash
df -h
```

**Uso de disco — contenedor Geth**

```bash
docker exec -it geth du -sh /data
```

**Uso de disco — contenedor Prysm**

```bash
docker exec -it prysm du -sh /data
```

**Uso de disco — contenedor Aztec**

```bash
docker exec -it nodeaztec-node-1 du -sh /data/*
```

---

## 🐳 5. Guía rápida de Docker

> Comandos esenciales para el día a día con contenedores Docker.

| Comando | Descripción |
|---------|-------------|
| `docker ps` | Muestra todos los contenedores en ejecución. |
| `docker ps -a` | Lista todos los contenedores, incluidos los detenidos. |
| `docker stats` | Métricas en tiempo real: CPU, memoria, red y disco. |
| `docker stop <id\|nombre>` | Detiene un contenedor en ejecución de forma ordenada. |
| `docker rm <id\|nombre>` | Elimina permanentemente un contenedor. |
| `docker container prune` | Elimina todos los contenedores detenidos para liberar espacio. |

**Ver contenedores activos**

```bash
docker ps
```

**Listar todos los contenedores (incluidos los detenidos)**

```bash
docker ps -a
```

**Métricas en tiempo real**

```bash
docker stats
```

**Detener un contenedor**

```bash
docker stop <container_id_or_name>
```

**Eliminar un contenedor**

```bash
docker rm <container_id_or_name>
```

**Eliminar todos los contenedores detenidos**

```bash
docker container prune
```

**Desinstalación completa de Docker**

> [!WARNING]
> Esto elimina Docker y **todos los datos** asociados (imágenes, volúmenes, contenedores) de forma irreversible.

```bash
sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-compose-plugin
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

---

<div align="center">

**📌 Repositorio de referencia personal — comandos verificados en entornos Ubuntu 24.04 / WSL2**

⭐ Si te resulta útil, considera dejar una estrella al repositorio

</div>
