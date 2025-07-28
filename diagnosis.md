# Comandos de Diagnóstico y Recuperación Ubuntu

## 🚨 ACCESO DE EMERGENCIA - Cuando el Sistema No Arranca

### Combinaciones de Teclas Críticas

```bash
# Durante el arranque (GRUB)
Shift (mantener presionado)    # Mostrar menú GRUB si está oculto
Esc (repetidamente)           # Alternativa para mostrar GRUB
e                             # Editar entrada de arranque en GRUB
c                             # Consola de comandos GRUB
F6                            # Opciones de arranque en Live USB

# Una vez en el sistema
Ctrl + Alt + F1-F6            # Cambiar a TTY (consola texto)
Ctrl + Alt + F7               # Volver al entorno gráfico (Ubuntu 16.04-)
Ctrl + Alt + F1               # Volver al entorno gráfico (Ubuntu 18.04+)
Ctrl + Alt + T                # Terminal en entorno gráfico
Ctrl + C                      # Interrumpir comando actual
Ctrl + Z                      # Suspender proceso
Ctrl + D                      # Salir/EOF
```

### Acceso por Consola de Texto (TTY)

Si el entorno gráfico no arranca:

```bash
# Al ver la pantalla de login gráfico que no responde:
Ctrl + Alt + F3               # Ir a TTY3 (consola texto)
# Luego hacer login normal con usuario y contraseña

# Para volver a intentar el entorno gráfico:
Ctrl + Alt + F1               # o F7 dependiendo de la versión

# Reiniciar el gestor de ventanas desde TTY:
sudo systemctl restart gdm3           # Para GNOME
sudo systemctl restart lightdm        # Para XFCE/LXDE
sudo systemctl restart sddm           # Para KDE
```

### Arranque en Modo Recuperación

```bash
# Desde GRUB:
1. Mantener Shift durante arranque
2. Seleccionar "Advanced options for Ubuntu"
3. Seleccionar "recovery mode"
4. En el menú recovery elegir:
   - "root" para shell de root
   - "fsck" para verificar discos
   - "network" para habilitar red

# Parámetros de kernel útiles (editar con 'e' en GRUB):
init=/bin/bash              # Arrancar directo a shell
single                      # Modo usuario único
recovery                    # Modo recuperación
nomodeset                   # Si hay problemas gráficos
acpi=off                    # Si hay problemas de energía
```

### Acceso desde Live USB/CD

```bash
# Preparar Live USB:
1. Descargar ISO de Ubuntu
2. Crear USB booteable con Rufus/Etcher/dd
3. Arrancar desde USB (F12/F2/Del según BIOS)

# En Live session:
sudo fdisk -l               # Ver particiones del disco duro
sudo mkdir /mnt/sistema     # Crear punto montaje
sudo mount /dev/sda1 /mnt/sistema  # Montar partición raíz

# Para chroot (entrar al sistema instalado):
sudo mount --bind /dev /mnt/sistema/dev
sudo mount --bind /dev/pts /mnt/sistema/dev/pts
sudo mount --bind /proc /mnt/sistema/proc
sudo mount --bind /sys /mnt/sistema/sys
sudo chroot /mnt/sistema
```

### GRUB Commands (Consola GRUB)

Si GRUB no encuentra el sistema:

```bash
# En la consola GRUB (presionar 'c'):
ls                          # Listar dispositivos
ls (hd0,1)/                # Ver contenido de partición
set root=(hd0,1)           # Establecer partición raíz
linux /boot/vmlinuz root=/dev/sda1
initrd /boot/initrd.img
boot                       # Arrancar

# Para ver particiones:
ls (hd0,msdos1)/boot       # Ver archivos de boot
```

### Resetear Contraseña desde Recovery

```bash
# Desde modo recovery o chroot:
passwd username             # Cambiar contraseña de usuario
passwd root                # Cambiar contraseña de root (si está habilitada)

# Habilitar usuario root si es necesario:
sudo passwd root
sudo passwd -u root         # Desbloquear cuenta root
```

## 🖥️ Información del Sistema

```bash
# Información básica del sistema
uname -a                    # Versión del kernel y arquitectura
hostnamectl                 # Información completa del sistema
lsb_release -a             # Versión de Ubuntu
uptime                     # Tiempo encendido y carga del sistema
whoami                     # Usuario actual
id                         # ID y grupos del usuario actual
```

## 💾 Estado de Discos y Almacenamiento

```bash
# Ver estructura de discos
lsblk                      # Árbol de dispositivos de bloque
lsblk -f                   # Con información de sistemas de archivos
fdisk -l                   # Lista todas las particiones (requiere sudo)
parted -l                  # Información detallada de particiones

# Espacio en disco
df -h                      # Espacio usado/libre en sistemas de archivos montados
df -i                      # Información de inodos
du -sh /*                  # Tamaño de directorios en raíz
du -sh /home/*            # Tamaño de directorios de usuarios
ncdu /                     # Navegador interactivo de uso de disco

# Estado de dispositivos
blkid                      # UUIDs y tipos de sistemas de archivos
mount | column -t          # Puntos de montaje actuales
findmnt                    # Árbol de puntos de montaje
```

## 🧠 Memoria y Procesos

```bash
# Memoria
free -h                    # Memoria RAM libre/usada
cat /proc/meminfo         # Información detallada de memoria
vmstat 1 5                # Estadísticas de memoria virtual

# Procesos
ps aux                     # Todos los procesos
ps aux --sort=-%mem       # Procesos ordenados por uso de memoria
ps aux --sort=-%cpu       # Procesos ordenados por uso de CPU
top                        # Monitor interactivo de procesos
htop                       # Monitor mejorado (si está instalado)
pstree                     # Árbol de procesos

# Carga del sistema
w                          # Usuarios conectados y carga
cat /proc/loadavg         # Carga promedio del sistema
```

## 🌐 Red y Conectividad

```bash
# Interfaces de red
ip addr show               # Interfaces y direcciones IP
ip route show             # Tabla de rutas
ss -tuln                  # Puertos abiertos
netstat -tuln             # Puertos abiertos (alternativo)
ping -c 4 8.8.8.8         # Prueba de conectividad
```

## 👤 Usuarios y Permisos

```bash
# Información de usuarios
cat /etc/passwd           # Lista de usuarios del sistema
getent passwd             # Usuarios (incluyendo LDAP/NIS)
who                       # Usuarios conectados actualmente
last                      # Últimos inicios de sesión
groups                    # Grupos del usuario actual
sudo -l                   # Permisos sudo del usuario actual

# Cambiar usuario
su - username              # Cambiar a otro usuario
sudo su -                 # Cambiar a root
```

## 🔧 Montaje de Discos

```bash
# Crear punto de montaje
sudo mkdir /mnt/recovery

# Montar por dispositivo
sudo mount /dev/sdb1 /mnt/recovery

# Montar por UUID (más seguro)
sudo mount UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx /mnt/recovery

# Montar solo lectura
sudo mount -o ro /dev/sdb1 /mnt/recovery

# Montar diferentes sistemas de archivos
sudo mount -t ext4 /dev/sdb1 /mnt/recovery
sudo mount -t ntfs /dev/sdb1 /mnt/recovery
sudo mount -t vfat /dev/sdb1 /mnt/recovery

# Desmontar
sudo umount /mnt/recovery
# o
sudo umount /dev/sdb1

# Forzar desmontaje si está ocupado
sudo umount -l /mnt/recovery  # lazy unmount
sudo fuser -kim /mnt/recovery # matar procesos que usan el punto
```

## 🔍 Diagnóstico de Problemas

```bash
# Verificar integridad del sistema de archivos
sudo fsck /dev/sdb1           # Verificar partición específica
sudo fsck -f /dev/sdb1        # Forzar verificación
sudo e2fsck -f /dev/sdb1      # Para ext2/3/4
sudo dosfsck /dev/sdb1        # Para FAT32

# Estado de hardware
lscpu                         # Información del CPU
lspci                         # Dispositivos PCI
lsusb                         # Dispositivos USB
lshw -short                   # Resumen de hardware
dmesg | tail -50             # Últimos mensajes del kernel
dmesg | grep -i error        # Errores en el kernel

# Temperatura y sensores
sensors                       # Temperaturas (requiere lm-sensors)
cat /sys/class/thermal/thermal_zone*/temp

# Servicios del sistema
systemctl status             # Estado de servicios
systemctl --failed           # Servicios fallidos
journalctl -xe              # Logs del sistema (systemd)
journalctl -f               # Seguir logs en tiempo real
```

## 📋 Logs del Sistema

```bash
# Logs principales
sudo tail -f /var/log/syslog        # Log general del sistema
sudo tail -f /var/log/kern.log      # Log del kernel
sudo tail -f /var/log/auth.log      # Log de autenticación
sudo tail -f /var/log/apache2/error.log  # Si tienes Apache

# Buscar errores específicos
sudo grep -i error /var/log/syslog
sudo grep -i fail /var/log/syslog
sudo grep -i "out of memory" /var/log/syslog
```

## 🔄 Recuperación y Backup Rápido

```bash
# Respaldar configuraciones importantes
sudo tar -czf /tmp/etc-backup.tar.gz /etc
cp -r /home/usuario /mnt/recovery/  # Respaldar home

# Buscar archivos importantes
find /home -name "*.doc*" -o -name "*.pdf" -o -name "*.jpg" 2>/dev/null
find /home -type f -mtime -7        # Archivos modificados última semana

# Copiar datos importantes
rsync -av /home/usuario/ /mnt/recovery/usuario/
```

## 🚑 Modo Recuperación

Si el sistema no arranca normalmente:

1. **Desde GRUB**: Selecciona "Advanced options" → "recovery mode"

2. **Desde Live USB**:
```bash
# Identificar partición del sistema
sudo fdisk -l
lsblk

# Montar partición raíz
sudo mount /dev/sda1 /mnt
sudo mount /dev/sda2 /mnt/home  # si /home está separado

# Montar sistemas especiales para chroot
sudo mount --bind /dev /mnt/dev
sudo mount --bind /proc /mnt/proc
sudo mount --bind /sys /mnt/sys

# Entrar al sistema instalado
sudo chroot /mnt

# Ahora puedes ejecutar comandos como si estuvieras en tu sistema
```

## ⚡ Comandos de Emergencia

```bash
# Liberar espacio rápidamente
sudo apt clean                    # Limpiar cache de paquetes
sudo apt autoremove               # Remover paquetes innecesarios
sudo journalctl --vacuum-time=3d  # Limpiar logs antiguos

# Procesos que consumen recursos
sudo kill -9 PID                  # Matar proceso específico
sudo pkill -f "nombre_proceso"    # Matar por nombre
sudo killall proceso              # Matar todos los procesos con ese nombre

# Reiniciar servicios críticos
sudo systemctl restart networking
sudo systemctl restart gdm3       # Reiniciar login gráfico
```

## 📝 Notas Importantes

- **Siempre usa `sudo`** para comandos que requieren permisos de administrador
- **Haz backups** antes de hacer cambios importantes
- **Anota los UUIDs** de tus particiones para montaje automático


## 🔗 Archivos de Configuración Críticos

```bash
/etc/fstab          # Configuración de montaje automático
/etc/passwd         # Usuarios del sistema
/etc/group          # Grupos del sistema
/etc/sudoers        # Configuración sudo
/etc/network/       # Configuración de red
/home/usuario/      # Datos personales
```
