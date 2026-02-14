# Upgrade RTX 3060 → RX 9070 XT (CachyOS / Arch / Hyprland)

Guía paso a paso para cambiar de NVIDIA propietario a AMD (amdgpu).

------------------------------------------------------------------------

## 1)  ANTES DE APAGAR EL EQUIPO (con la GPU NVIDIA aún instalada)

1.1 Ver qué paquetes NVIDIA están instalados

`pacman -Qs nvidia`

------------------------------------------------------------------------

1.2 Desinstalar drivers NVIDIA

`sudo pacman -Rns nvidia-580xx-dkms nvidia-580xx-utils lib32-nvidia-580xx-utils opencl-nvidia-580xx lib32-opencl-nvidia-580xx libva-nvidia-driver egl-x11`

o

`sudo pacman -Rns $(pacman -Qq | grep nvidia)`

------------------------------------------------------------------------

1.3 Verificar:

`pacman -Qs nvidia` -> No debe quedar nada instalado excepto cosas tipo nvidia-settings

------------------------------------------------------------------------

1.4 Verificar flags del kernel

`cat /proc/cmdline`

Si aparece algo como:

`nvidia_drm.modeset=1`

Debo eliminarlo del bootloader.

En GRUB:

`sudo micro /etc/default/grub`

Eliminar `nvidia_drm.modeset=1` de la línea `GRUB_CMDLINE_LINUX_DEFAULT`.

Luego regenerar:

`sudo grub-mkconfig -o /boot/grub/grub.cfg`

Y regenerar initramfs:

`sudo mkinitcpio -P`

------------------------------------------------------------------------

1.5 Reboot (con la GPU NVIDIA puesta)

`reboot`

Reiniciar antes de cambiar físicamente la GPU y antes de instalar el stack AMD para verificar que:

- El sistema arranca sin los módulos NVIDIA  
- No hay errores en initramfs  
- No queda nada bloqueando el arranque  
- Está usando solo drivers genéricos (modesetting)

Si algo falla, aún está instalada la NVIDIA y es más fácil corregirlo.

Tras reiniciar, verificar:

`lsmod | grep nvidia` -> debe devolver vacío

------------------------------------------------------------------------

1.6 Instalar stack gráfico AMD

`sudo pacman -S mesa vulkan-radeon lib32-mesa lib32-vulkan-radeon linux-firmware`

Instalar soporte VAAPI (aceleración de vídeo):

`sudo pacman -S libva-mesa-driver`

Opcional (para verificar luego):

`sudo pacman -S libva-utils`

------------------------------------------------------------------------

## 2)  CAMBIO FÍSICO DE HARDWARE

    2.1  Apagar el equipo  
    2.2  Desconectar corriente  
    2.3  Cambiar la PSU (si corresponde)  
    2.4  Instalar la RX 9070 XT  
    2.5  Conectar correctamente los cables PCIe (sin adaptadores)  
    2.6  Encender el equipo

------------------------------------------------------------------------

## 3)  VERIFICACIONES AL ARRANCAR

3.1 Confirmar que carga amdgpu

`lspci -k | grep -A 3 -E "VGA|Display"`

Debe aparecer:

`Kernel driver in use: amdgpu`

------------------------------------------------------------------------

3.2 Verificar Vulkan

Si no está instalado:

`sudo pacman -S vulkan-tools`

Luego:

`vulkaninfo \| grep deviceName`

Debe mostrar la RX 9070 XT.

------------------------------------------------------------------------

3.3 Verificar aceleración de vídeo (VAAPI)

`vainfo`

Debe listar codecs como: `H264 HEVC AV1`

------------------------------------------------------------------------

## 4)  RECOMENDADO

Instalar CoreCtrl (undervolt / control fino):

`sudo pacman -S corectrl`

Permite: - Undervolt ligero - Ajuste de curvas de ventilador - Mejor
eficiencia térmica

------------------------------------------------------------------------

## 5)  CHECKLIST FINAL

\[ \] Drivers NVIDIA eliminados  
\[ \] Flags del kernel limpiados  
\[ \] Mesa y Vulkan instalados  
\[ \] amdgpu cargado correctamente  
\[ \] Vulkan funcionando  
\[ \] VAAPI funcionando
