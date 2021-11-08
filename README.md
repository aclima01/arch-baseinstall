# arch-baseinstall


1. carrega layout do teclado
~~~root@archiso_$
$ loadkeys br-abnt2
~~~

2. conecta a internet pelo wifi
~~~root@archiso_$
$ iwctl
~~~
~~~root@archiso_$
[iwctl] $ station {wlan0} scan // buscar redes próximas
[iwctl] $ station {wlan0} connect {NomeDaRede}
~~~
3. visualizar discos disponiveis
~~~root@archiso_$
$ lsblk
~~~

4. entra no modo de gerenciamento de disco (particionamento)
~~~root@archiso_$
$ cfdisk /dev/{sda}
~~~

Tamanho   | Tipo
--------- | ------
300M | EFI System
23G  | Linux Swap
30G  | /
XXG  | /home

5. formatação das partições
~~~root@archiso_$
$ mkfs.vfat /dev/{sda1}   // cria partição de boot
$ mkswap /dev/{sda2}      // cria partição de swap
$ mkfs.ext4 /dev/{sda3}   // cria partição do sistema (/)
$ mkfs.ext4 /dev/{sda4}   // cria partição da home (/home)
~~~

6. criar pontos de montagem
~~~root@archiso_$
$ mount /dev/{sda3} /mont         // /root

$ swapon /dev/{sda2}              // swap

$ mkdir -p /mnt/boot/efi          // boot 
$ mount /dev/{sda1} /mnt/boot/efi

$ mkdir /mnt/home                 // /home
$ mount /dev/{sda4} /mnt/home
~~~

7. instalação dos pacotes basicos na partição root
~~~root@archiso_$
$ pacstrap /mnt base linux linux-firmware git vim intel-ucode
~~~

8. criação do arquivo de fstab baseados no UUID das partcições
~~~root@archiso_$
$ genfstab -U /mnt >> /mnt/etc/fstab
~~~

9. entrar no sistema pelas partições criadas e visualizar fstab
~~~root@archiso_$
$ arch-chroot /mnt
$ cat /mnt/etc/fstab
~~~

10. obter script de instalação
~~~root@archiso_$
$ git clone https://github.com/alima01/arch-baseinstall
$ cd arch-baseinstall
$ vim base-uefi.sh        // alterar senhas e comandos necessários
$ chmod +x base-uefi.sh   // torna o script executável
$ cd /
$ ./arch-baseinstall/base-uefi.sh     // executa o script
~~~

11. ajustar e criar mkinitcpio
~~~root@archiso_$
$ vim /etc/mkinitcpio.conf    // Linha 7 : "MODULES=(i915 nvidia)"
$ mkinitcpio -p linux
~~~

12. criar configurações do grub
~~~root@archiso_$
$ grub-mkconfig -o /boot/grub/grub.cfg
~~~

13. Tudo Ok! Sair, desmontar e reiniciar
~~~root@archiso_$
$ exit
$ umount -R /mnt
$ reboot
~~~

14. Remover pendrive - Check grub menu e login - aqui já deve aparecer o tty para login no novo sistema
~~~root@archiso_$
{username}
{password}
~~~

15. Conexão wifi no novo sistema
~~~root@archiso_$
$ nmtui       // "Activate a connection" ==> conectar no wifi desejado
~~~

16. Desktop
~~~root@archiso_$
$ cp -r /arch-baseinstall .   // copiar script para /home
~~~
