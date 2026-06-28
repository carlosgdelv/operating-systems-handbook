<div align="center">

# 🖥️ Operating Systems Handbook

### Guía práctica de administración de sistemas, virtualización y entornos Linux / macOS / Windows

[![Bash](https://img.shields.io/badge/Bash-4EAA25?style=for-the-badge&logo=gnubash&logoColor=white)](#)
[![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)](#)
[![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)](#)
[![macOS](https://img.shields.io/badge/macOS-000000?style=for-the-badge&logo=apple&logoColor=white)](#)
[![WSL](https://img.shields.io/badge/WSL-0078D4?style=for-the-badge&logo=windows&logoColor=white)](#)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)](#)

[![License](https://img.shields.io/badge/license-MIT-blue.svg?style=flat-square)](LICENSE)
[![Maintained](https://img.shields.io/badge/maintained-yes-success.svg?style=flat-square)](#)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](#)

</div>

---

## 📖 Acerca de

Colección de comandos, procedimientos y chuletas (*cheatsheets*) para tareas habituales de administración de sistemas en **Linux**, **macOS** y **Windows (WSL)**: instalación de entornos, gestión de almacenamiento, monitorización de recursos, contenedores Docker y herramientas generales de sistema (SSH, tmux, rsync, cron, firewall).

Pensado como referencia rápida de copia y pega para el día a día.

---

## 📑 Tabla de contenidos

| # | Sección | Descripción |
|---|---------|-------------|
| 1 | [🪟 WSL — Windows Subsystem for Linux](#-1-wsl--windows-subsystem-for-linux) | Instalación, gestión y configuración de recursos |
| 2 | [🐧 Instalación Live de Linux](#-2-instalación-live-de-linux) | Creación de USB arrancable, verificación de ISO |
| 3 | [🍎 macOS](#-3-macos) | Homebrew, gestión de discos, Docker y equivalencias con Linux |
| 4 | [💾 Almacenamiento y LVM](#-4-almacenamiento-y-lvm) | Diagnóstico, extensión y reducción de volúmenes |
| 5 | [🔍 Monitorización del sistema](#-5-monitorización-del-sistema) | CPU, disco, red, logs y contenedores |
| 6 | [🐳 Docker](#-6-docker) | Contenedores, Compose, volúmenes, imágenes y redes |
| 7 | [🛠️ Herramientas generales de sistema](#-7-herramientas-generales-de-sistema) | SSH, tmux, rsync, cron, firewall |

---

## 🪟 1. WSL — Windows Subsystem for Linux

> Instalación, gestión de distribuciones y configuración de recursos de WSL en Windows.

### 1.1 Instalación inicial

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

O, de forma abreviada (abre la distribución por defecto):

```bash
wsl
```

---

### 1.2 Gestión de distribuciones

**Listar distribuciones instaladas y su estado**

```bash
wsl --list --verbose
```

**Establecer una distribución como predeterminada**

```bash
wsl --set-default Ubuntu-24.04
```

**Apagar todas las instancias de WSL**

Útil cuando WSL consume demasiada RAM o tras cambiar la configuración de `.wslconfig`.

```bash
wsl --shutdown
```

**Apagar una distribución concreta**

```bash
wsl --terminate Ubuntu-24.04
```

---

### 1.3 Backup y migración de una distribución

**Exportar una distribución a un archivo `.tar`**

```bash
wsl --export Ubuntu-24.04 backup-ubuntu.tar
```

**Importar una distribución desde un backup**

```bash
wsl --import Ubuntu-24.04-Restored C:\WSL\Ubuntu-24.04-Restored backup-ubuntu.tar
```

---

### 1.4 Acceso a archivos entre Windows y Linux

**Acceder al sistema de archivos de Windows desde WSL**

```bash
cd /mnt/c/Users/<tu_usuario>
```

**Acceder al sistema de archivos de WSL desde Windows**

Desde el Explorador de archivos de Windows, escribe en la barra de direcciones:

```text
\\wsl$\Ubuntu-24.04\
```

---

### 1.5 Configuración de recursos (`.wslconfig`)

> Limita la RAM y CPU que WSL2 puede consumir en el sistema anfitrión. Edita o crea el archivo en `C:\Users\<tu_usuario>\.wslconfig`.

```ini
[wsl2]
memory=8GB
processors=4
swap=2GB
```

Tras editarlo, aplica los cambios con:

```bash
wsl --shutdown
```

---

## 🐧 2. Instalación Live de Linux

> Procedimiento para crear un USB arrancable con una distribución Linux (ejemplo: Ubuntu 24.04).

> [!CAUTION]
> El comando `dd` escribe a nivel de bloque y **borra todo el contenido del disco de destino sin posibilidad de deshacerlo**. Verifica el dispositivo (`/dev/sdX`) con `lsblk -fp` antes de cada paso. Un error de dispositivo puede destruir tu disco principal.

### 2.1 Detectar y preparar el USB

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

---

### 2.2 Descargar y verificar la imagen ISO

**Paso 4 — Descargar una ISO de Linux**

Ejemplo con Ubuntu 24.04:

```bash
wget https://releases.ubuntu.com/24.04/ubuntu-24.04-desktop-amd64.iso -O ubuntu.iso
```

**Paso 5 — Verificar la integridad de la ISO**

Antes de grabar la imagen, comprueba que no está corrupta ni manipulada comparando su checksum con el oficial publicado por Ubuntu.

```bash
sha256sum ubuntu.iso
```

Compara el resultado con el valor publicado en [releases.ubuntu.com](https://releases.ubuntu.com/24.04/SHA256SUMS).

---

### 2.3 Grabar la ISO en el USB

**Paso 6 — Escribir la ISO en el USB**

```bash
sudo dd if=ubuntu.iso of=/dev/sda bs=4M status=progress oflag=sync
```

> 💡 **Alternativa con barra de progreso (`pv`)**: si prefieres una barra de progreso más detallada que `status=progress`, puedes canalizar la escritura a través de `pv`:
>
> ```bash
> sudo apt install pv -y
> pv ubuntu.iso | sudo dd of=/dev/sda bs=4M oflag=sync
> ```

> 💡 **Alternativa sin terminal**: si prefieres una herramienta gráfica, puedes usar **[Rufus](https://rufus.ie/)** (solo Windows) o **[balenaEtcher](https://etcher.balena.io/)** (Windows/macOS/Linux), ambas gratuitas y pensadas para crear USBs arrancables de forma guiada.

**Paso 7 — Sincronizar y expulsar**

```bash
sync
sudo eject /dev/sda
```

Ya puedes retirar el USB de forma segura.

---

### 2.4 Comprobación final

Al ejecutar de nuevo `lsblk -fp` deberías ver algo similar a:

```text
/dev/sda iso9660 Ubuntu 24.04 amd64
```

---

## 🍎 3. macOS

> Gestión de paquetes, discos, virtualización y equivalencias de comandos para usuarios de macOS.

### 3.1 Homebrew — gestor de paquetes

**Instalar Homebrew**

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

**Actualizar la lista de paquetes disponibles**

```bash
brew update
```

**Actualizar todos los paquetes instalados**

```bash
brew upgrade
```

**Instalar un paquete**

```bash
brew install <paquete>
```

**Instalar una aplicación gráfica (Cask)**

```bash
brew install --cask <aplicación>
```

**Listar paquetes instalados**

```bash
brew list
```

**Desinstalar un paquete**

```bash
brew uninstall <paquete>
```

**Limpiar versiones antiguas y caché de descargas**

```bash
brew cleanup
```

**Diagnosticar problemas de instalación**

```bash
brew doctor
```

---

### 3.2 Gestión de discos y particiones (`diskutil`)

**Listar todos los discos y particiones conectados**

Equivalente macOS de `lsblk -fp` en Linux.

```bash
diskutil list
```

**Ver información detallada de un disco**

```bash
diskutil info /dev/disk2
```

**Desmontar un disco completo (todas sus particiones)**

```bash
diskutil unmountDisk /dev/disk2
```

**Borrar y formatear un disco/USB**

```bash
diskutil eraseDisk FAT32 USBDRIVE MBR /dev/disk2
```

**Expulsar un disco de forma segura**

```bash
diskutil eject /dev/disk2
```

---

### 3.3 Crear un USB arrancable en macOS

> [!CAUTION]
> Igual que en Linux, `dd` en macOS escribe a bajo nivel y **borra todo el contenido del disco de destino**. En macOS los discos se identifican como `/dev/diskN` (no `/dev/sdX`). Usa siempre el disco completo (`rdiskN`) en lugar de una partición (`diskNs1`) para mayor velocidad.

**Paso 1 — Identificar el disco USB**

```bash
diskutil list
```

**Paso 2 — Desmontar el disco**

```bash
diskutil unmountDisk /dev/disk2
```

**Paso 3 — Escribir la imagen ISO/IMG en el USB**

Usar `r` antes del nombre del disco (`rdisk2`) accede al dispositivo en modo "raw", mucho más rápido que `disk2`.

```bash
sudo dd if=ubuntu.iso of=/dev/rdisk2 bs=4m status=progress
```

**Paso 4 — Expulsar el disco**

```bash
diskutil eject /dev/disk2
```

---

### 3.4 Docker en macOS

> macOS no tiene soporte nativo de contenedores Linux: Docker Desktop (o sus alternativas) levantan una máquina virtual ligera por debajo.

**Instalar Docker Desktop vía Homebrew**

```bash
brew install --cask docker
```

**Alternativa ligera a Docker Desktop: Colima**

[Colima](https://github.com/abiosoft/colima) ofrece un runtime de contenedores en macOS sin la sobrecarga de Docker Desktop, usando la CLI estándar de Docker.

```bash
brew install colima docker
colima start
```

**Verificar que el motor de Docker está activo**

```bash
docker info
```

**Detener el entorno de Colima**

```bash
colima stop
```

> 💡 Todos los comandos de la sección [🐳 Docker](#-6-docker) funcionan igual en macOS una vez que Docker Desktop o Colima están en ejecución.

---

### 3.5 Equivalencias de comandos Linux ↔ macOS

> macOS usa utilidades BSD en lugar de GNU; algunos comandos habituales en Linux no existen o se comportan de forma distinta.

| Tarea | Linux | macOS |
|-------|-------|-------|
| Listar discos/particiones | `lsblk -fp` | `diskutil list` |
| Monitor de procesos interactivo | `htop` | `htop` (vía `brew install htop`) o `top` |
| Información de memoria | `free -h` | `vm_stat` o `top -l 1` |
| Buscar archivos por nombre | `find . -name "*.txt"` | `find . -name "*.txt"` (igual) |
| Ver puertos abiertos | `ss -tulpn` | `lsof -i -P` |
| Editar variables de entorno persistentes | `~/.bashrc` / `~/.profile` | `~/.zshrc` (zsh es el shell por defecto) |
| Copiar al portapapeles | `xclip` | `pbcopy` |
| Pegar desde el portapapeles | `xclip -o` | `pbpaste` |
| Gestor de paquetes | `apt` / `dnf` / `pacman` | `brew` |
| Información del sistema | `uname -a` / `lscpu` | `system_profiler SPHardwareDataType` |

---

### 3.6 Gestión de servicios (`launchctl`)

> macOS usa `launchd` en lugar de `systemd`. Es el equivalente a `systemctl` en Linux.

**Listar todos los servicios/daemons cargados**

```bash
launchctl list
```

**Cargar un servicio**

```bash
launchctl load ~/Library/LaunchAgents/com.ejemplo.servicio.plist
```

**Descargar (detener) un servicio**

```bash
launchctl unload ~/Library/LaunchAgents/com.ejemplo.servicio.plist
```

---

### 3.7 Mantenimiento y limpieza del sistema

**Ver espacio en disco**

```bash
df -h
```

**Analizar visualmente qué ocupa espacio (requiere Homebrew)**

```bash
brew install ncdu
ncdu /
```

**Vaciar la papelera desde terminal**

```bash
rm -rf ~/.Trash/*
```

**Reparar permisos y verificar el disco de arranque**

```bash
diskutil verifyVolume /
```

---

## 💾 4. Almacenamiento y LVM

> Diagnóstico previo, extensión y reducción de volúmenes lógicos (LVM) sobre discos físicos en una VM Linux.

### 4.1 Diagnóstico previo (antes de tocar nada)

**Listar discos, particiones y puntos de montaje**

```bash
lsblk -fp
```

**Listar particiones con detalle (tabla de particiones, tamaños, tipos)**

```bash
sudo fdisk -l
```

---

### 4.2 Extender un volumen lógico (añadir disco nuevo)

**Paso 1 — Actualizar el sistema e instalar herramientas LVM**

```bash
sudo apt update
sudo apt install lvm2 -y
```

**Paso 2 — Borrar datos y preparar el nuevo disco**

Elimina cualquier dato de sistema de archivos/particiones existente e inicializa el disco como volumen físico.

```bash
sudo wipefs --all /dev/nvme1n1
sudo sgdisk --zap-all /dev/nvme1n1
sudo pvcreate /dev/nvme1n1
```

**Paso 3 — Añadir el nuevo disco al grupo de volúmenes**

Extiende el grupo de volúmenes (`ubuntu-vg`) para incluir el nuevo disco.

```bash
sudo vgextend ubuntu-vg /dev/nvme1n1
```

**Paso 4 — Confirmar los cambios en el grupo de volúmenes**

```bash
sudo vgs
```

**Paso 5 — Extender el volumen lógico con todo el espacio libre**

```bash
sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
```

**Paso 6 — Redimensionar el sistema de archivos**

```bash
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
```

**Paso 7 — Verificar los cambios**

```bash
df -h /
df -T /
```

**Paso 8 — (Opcional) Ver detalle del volumen lógico**

```bash
sudo lvdisplay /dev/ubuntu-vg/ubuntu-lv
```

---

### 4.3 Reducir un volumen lógico

> [!WARNING]
> Reducir un volumen es una operación de riesgo: si el nuevo tamaño es menor que los datos existentes, **se pierden datos**. Haz siempre una copia de seguridad antes y verifica el sistema de archivos en cada paso.

**Paso 1 — Desmontar el volumen**

No se puede reducir un sistema de archivos `ext4` en caliente.

```bash
sudo umount /dev/ubuntu-vg/ubuntu-lv
```

**Paso 2 — Verificar el sistema de archivos**

```bash
sudo e2fsck -f /dev/ubuntu-vg/ubuntu-lv
```

**Paso 3 — Reducir primero el sistema de archivos**

Reduce el filesystem a un tamaño *menor o igual* al que tendrá el volumen lógico (ejemplo: 20 GB).

```bash
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv 20G
```

**Paso 4 — Reducir el volumen lógico**

```bash
sudo lvreduce -L 20G /dev/ubuntu-vg/ubuntu-lv
```

**Paso 5 — Volver a montar el volumen**

```bash
sudo mount /dev/ubuntu-vg/ubuntu-lv
```

---

### 4.4 Gestión gráfica de particiones (alternativa)

Para quien prefiera una interfaz visual en lugar de la terminal:

```bash
sudo apt install gparted -y
sudo gparted
```

---

## 🔍 5. Monitorización del sistema

> Comandos para vigilar el uso de CPU, memoria, disco, red, logs y contenedores específicos.

### 5.1 CPU y memoria

**Monitor interactivo de procesos (CPU, memoria, usuario)**

```bash
htop
```

**Resumen de memoria RAM y swap**

```bash
free -h
```

---

### 5.2 Disco

**Uso de disco por sistema de archivos**

```bash
df -h
```

**Explorador visual interactivo de uso de disco (más cómodo que `du`)**

```bash
sudo apt install ncdu -y
ncdu /
```

**Uso de disco por proceso, en tiempo real**

```bash
sudo apt install iotop -y
sudo iotop
```

---

### 5.3 Red

**Uso de red por proceso, en tiempo real**

```bash
sudo apt install nethogs -y
sudo nethogs
```

**Tráfico de red por interfaz, en tiempo real (estilo `top`)**

```bash
sudo apt install iftop -y
sudo iftop
```

---

### 5.4 Logs del sistema

**Ver logs del sistema en tiempo real (systemd)**

```bash
journalctl -f
```

**Ver logs de un servicio concreto**

```bash
journalctl -u <nombre-del-servicio> -f
```

---

### 5.5 Monitorización de contenedores específicos

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

## 🐳 6. Docker

> Comandos esenciales para el día a día con contenedores, Docker Compose, volúmenes, imágenes y redes.

### 6.1 Contenedores

| Comando | Descripción |
|---------|-------------|
| `docker ps` | Muestra todos los contenedores en ejecución. |
| `docker ps -a` | Lista todos los contenedores, incluidos los detenidos. |
| `docker stats` | Métricas en tiempo real: CPU, memoria, red y disco. |
| `docker logs <id\|nombre>` | Muestra los logs de un contenedor. |
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

**Ver logs de un contenedor**

Útil para depurar un nodo (geth, prysm, aztec, etc.) sin entrar dentro del contenedor.

```bash
docker logs -f geth
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

---

### 6.2 Docker Compose

> Gestión de servicios multi-contenedor definidos en un `docker-compose.yml` (típico cuando hay varios nodos como geth, prysm y aztec en el mismo stack).

**Levantar todos los servicios en segundo plano**

```bash
docker compose up -d
```

**Detener y eliminar todos los servicios del stack**

```bash
docker compose down
```

**Ver logs de todos los servicios del stack**

```bash
docker compose logs -f
```

**Reiniciar un servicio concreto**

```bash
docker compose restart <nombre_servicio>
```

---

### 6.3 Volúmenes

**Listar volúmenes**

```bash
docker volume ls
```

**Eliminar volúmenes no utilizados**

> [!WARNING]
> Esto borra de forma permanente los datos de cualquier volumen que no esté en uso por un contenedor activo.

```bash
docker volume prune
```

---

### 6.4 Imágenes

**Listar imágenes descargadas**

```bash
docker images
```

**Eliminar todas las imágenes no utilizadas por ningún contenedor**

```bash
docker image prune -a
```

---

### 6.5 Redes

**Listar redes de Docker**

```bash
docker network ls
```

---

### 6.6 Desinstalación completa de Docker

> [!WARNING]
> Esto elimina Docker y **todos los datos** asociados (imágenes, volúmenes, contenedores) de forma irreversible.

```bash
sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-compose-plugin
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

---

## 🛠️ 7. Herramientas generales de sistema

> Utilidades transversales para trabajo con servidores remotos, transferencia de archivos, automatización y seguridad básica.

### 7.1 SSH — acceso remoto

**Generar un par de claves SSH**

```bash
ssh-keygen -t ed25519 -C "tu_email@ejemplo.com"
```

**Copiar la clave pública a un servidor remoto**

```bash
ssh-copy-id usuario@servidor
```

**Conectarse a un servidor remoto**

```bash
ssh usuario@servidor
```

**Conectarse usando un puerto distinto al 22**

```bash
ssh -p 2222 usuario@servidor
```

---

### 7.2 tmux — sesiones persistentes

> Permite mantener procesos corriendo en un servidor remoto aunque se cierre la conexión SSH.

**Crear una nueva sesión con nombre**

```bash
tmux new -s nombre_sesion
```

**Listar sesiones activas**

```bash
tmux ls
```

**Desconectarse de la sesión sin cerrarla**

Atajo de teclado dentro de tmux:

```text
Ctrl + b, luego d
```

**Volver a conectarse a una sesión existente**

```bash
tmux attach -t nombre_sesion
```

**Cerrar una sesión por completo**

```bash
tmux kill-session -t nombre_sesion
```

---

### 7.3 rsync — transferencia y backup de archivos

**Copiar una carpeta local a un servidor remoto**

```bash
rsync -avz /ruta/local/ usuario@servidor:/ruta/remota/
```

**Copiar desde un servidor remoto a local**

```bash
rsync -avz usuario@servidor:/ruta/remota/ /ruta/local/
```

**Sincronizar borrando en destino lo que ya no existe en origen**

> [!CAUTION]
> La opción `--delete` elimina en destino los archivos que ya no estén en origen. Revisa siempre antes con `--dry-run`.

```bash
rsync -avz --delete --dry-run /ruta/local/ usuario@servidor:/ruta/remota/
```

---

### 7.4 cron — tareas programadas

**Editar las tareas programadas del usuario actual**

```bash
crontab -e
```

**Listar las tareas programadas actuales**

```bash
crontab -l
```

**Ejemplo: ejecutar un script todos los días a las 3:00 AM**

```cron
0 3 * * * /ruta/al/script.sh
```

---

### 7.5 ufw — firewall básico (Ubuntu)

**Activar el firewall**

```bash
sudo ufw enable
```

**Ver el estado y las reglas activas**

```bash
sudo ufw status verbose
```

**Permitir un puerto concreto (ejemplo: SSH)**

```bash
sudo ufw allow 22/tcp
```

**Denegar un puerto concreto**

```bash
sudo ufw deny 8080/tcp
```

**Eliminar una regla**

```bash
sudo ufw delete allow 22/tcp
```

---

<div align="center">

**📌 Repositorio de referencia personal — comandos verificados en entornos Ubuntu 24.04 / macOS / WSL2**

⭐ Si te resulta útil, considera dejar una estrella al repositorio

</div>
