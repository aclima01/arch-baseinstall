# arch-baseinstall

#### --------------------------------------------------
### carrega layout do teclado
loadkeys br-abnt2

#### --------------------------------------------------
### conecta a internet pelo wifi
iwctl

station {wlan0} scan # buscar redes próximas
station {wlan0} connect {NomeDaRede}

#### --------------------------------------------------
### visualizar discos disponiveis
lsblk

#### --------------------------------------------------
### entra no modo de gerenciamento de disco (particionamento)
cfdisk /dev/{sda}
## 300M - EFI System
## 23G - SWAP
## 30G - /root
## XXG - /home

#### --------------------------------------------------
### formatação das partições
mkfs.vfat /dev/{sda1}   # cria partição de boot
mkswap /dev/{sda2}      # cria partição de swap
mkfs.ext4 /dev/{sda3}   # cria partição do sistema + home
mkfs.ext4 /dev/{sda4}   # cria partição do sistema + home

#### --------------------------------------------------
### criar pontos de montagem

mount /dev/{sda3} /mont         # /root

swapon /dev/{sda2}              # swap

mkdir -p /mnt/boot/efi          # boot 
mount /dev/{sda1} /mnt/boot/efi

mkdir /mnt/home                 # /home
mount /dev/{sda4} /mnt/home

#### --------------------------------------------------
### instalação dos pacotes basicos na partição root
pacstrap /mnt base linux linux-firmware git vim intel-ucode

#### --------------------------------------------------
### criação do arquivo de fstab baseados no UUID das partcições
genfstab -U /mnt >> /mnt/etc/fstab

#### --------------------------------------------------
### entrar no sistema pelas partições criadas e visualizar fstab
arch-chroot /mnt
cat /mnt/etc/fstab

#### --------------------------------------------------
### obter script de instalação
git clone https://github.com/alima01/arch-baseinstall
cd arch-baseinstall
vim base-uefi.sh        # alterar senhas e comandos necessários
chmod +x base-uefi.sh   # torna o script executável
cd /
./arch-baseinstall/base-uefi.sh # executa o script

#### --------------------------------------------------
### ajustar e criar mkinitcpio
vim /etc/mkinitcpio.conf # Linha 7 : "MODULES=(i915 nvidia)"
mkinitcpio -p linux

#### --------------------------------------------------
### criar configurações do grub
grub-mkconfig -o /boot/grub/grub.cfg

#### --------------------------------------------------
### Tudo Ok! Sair, desmontar e reiniciar
exit
umount -R /mnt
reboot

#### --------------------------------------------------
### Remover pendrive - Check grub menu e login
### aqui deve já aparecer o tty para login
username
password

#### --------------------------------------------------
### Conexão wifi no novo sistema
nmtui # Activate a connection, conectar no wifi desejado

#### --------------------------------------------------
### Desktop
cp -r /arch-baseinstall .   # copiar script para /home

Meu script básico de instalação para execução após chroot no Arch Linux
