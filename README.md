# arch-baseinstall

#### --------------------------------------------------
### carrega layout do teclado
_loadkeys br-abnt2_

#### --------------------------------------------------
### conecta a internet pelo wifi
_iwctl_

_station {wlan0} scan # buscar redes próximas_
_station {wlan0} connect {NomeDaRede}_

#### --------------------------------------------------
### visualizar discos disponiveis
_lsblk_

#### --------------------------------------------------
### entra no modo de gerenciamento de disco (particionamento)
_cfdisk /dev/{sda}_

Tamanho   | Tipo
--------- | ------
300M | EFI System
23G  | Linux Swap
30G  | /
XXG  | /home

#### --------------------------------------------------
### formatação das partições
_mkfs.vfat /dev/{sda1}_   ###### cria partição de boot
_mkswap /dev/{sda2}_      ###### cria partição de swap
_mkfs.ext4 /dev/{sda3}_   ###### cria partição do sistema + home
_mkfs.ext4 /dev/{sda4}_   ###### cria partição do sistema + home

#### --------------------------------------------------
### criar pontos de montagem

_mount /dev/{sda3} /mont_         ###### /root

_swapon /dev/{sda2}_              ###### swap

_mkdir -p /mnt/boot/efi_          ###### boot 
_mount /dev/{sda1} /mnt/boot/efi_

_mkdir /mnt/home_                 ###### /home
_mount /dev/{sda4} /mnt/home_

#### --------------------------------------------------
### instalação dos pacotes basicos na partição root
_pacstrap /mnt base linux linux-firmware git vim intel-ucode_

#### --------------------------------------------------
### criação do arquivo de fstab baseados no UUID das partcições
_genfstab -U /mnt >> /mnt/etc/fstab_

#### --------------------------------------------------
### entrar no sistema pelas partições criadas e visualizar fstab
_arch-chroot /mnt_
_cat /mnt/etc/fstab_

#### --------------------------------------------------
### obter script de instalação
_git clone https://github.com/alima01/arch-baseinstall_
_cd arch-baseinstall_
_vim base-uefi.sh_        ###### alterar senhas e comandos necessários
_chmod +x base-uefi.sh_   ###### torna o script executável
_cd /_
_./arch-baseinstall/base-uefi.sh ###### executa o script_

#### --------------------------------------------------
### ajustar e criar mkinitcpio
_vim /etc/mkinitcpio.conf_    ###### Linha 7 : "MODULES=(i915 nvidia)"
_mkinitcpio -p linux_

#### --------------------------------------------------
### criar configurações do grub
_grub-mkconfig -o /boot/grub/grub.cfg_

#### --------------------------------------------------
### Tudo Ok! Sair, desmontar e reiniciar
_exit_
_umount -R /mnt_
_reboot_

#### --------------------------------------------------
### Remover pendrive - Check grub menu e login
### aqui deve já aparecer o tty para login
_{username}_
_{password}_

#### --------------------------------------------------
### Conexão wifi no novo sistema
_nmtui_       ###### "Activate a connection" ==> conectar no wifi desejado

#### --------------------------------------------------
### Desktop
_cp -r /arch-baseinstall ._   ###### copiar script para /home
