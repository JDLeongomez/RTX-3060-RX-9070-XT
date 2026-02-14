# RTX 3060 → RX 9070 XT (CachyOS)

## Pre-cambio (con NVIDIA puesta)

- Desinstalar drivers NVIDIA  
- Quitar `nvidia_drm.modeset=1` si existe  
- `grub-mkconfig` + `mkinitcpio -P`  
- Reboot  
- `lsmod | grep nvidia` → debe estar vacío  

## Preparar AMD

- `sudo pacman -S mesa vulkan-radeon lib32-mesa lib32-vulkan-radeon`

## Cambio físico

- Apagar  
- Cambiar GPU  
- Encender  

## Verificar

- `lspci -k | grep -A3 VGA` → `amdgpu`
- `vulkaninfo | grep deviceName`
- `vainfo`
