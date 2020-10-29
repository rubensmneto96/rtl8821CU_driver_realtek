# rtl8821CU_driver_realtek
Instruções de como instalar o driver da Realtek do adaptador USB  RTL8111/8168/8411 PCI Express Gigabit Ethernet Controller (rev 16)

Este é um guia, que depois de procurar muito foi feito para quem comprou este adaptador(https://produto.mercadolivre.com.br/MLB-1613201656-adaptador-receptor-wireless-usb-wi-fi-5ghz-dual-band-1020-_JM) que necessita instalar os drivers no Linux, traduzido para o PT-BR.
Repositŕoio original: https://github.com/brektrou/rtl8821CU

# Instruções
É necessário terem o `make`,`gcc`,`linux-header` e o `git` instalados no sistema.
## 1- Crie este diretório e clone o seguinte repositório:
```
mkdir -p ~/build
cd ~/build
git clone https://github.com/brektrou/rtl8821CU.git
```
## Fazendo o 'build' e o 'install' com o DKMS
O DKMS é um sistema que vai automaticamente instalar e recompilar o módulo do seu kernel quando um novo for instalado ou atualizado.

### Pelo Debian/Ubuntu
`sudo apt-get install dkms`

### Pelo Arch Linux/Manjaro
`sudo pacman -S dkms`

Para usar o **DKMS** com esse projeto, use:

`./dkms-install.sh`

Se você quiser remove-lo depois, use:

`./dkms-remove.sh`

## Plugue o adaptador USB no seu computador
Se o WI-FI detectar, ótimo. Senão, talvez você vai precisar instalar o `usb-modeswitch`, e vai precisar mudar o modo de execução do seu dispositivo dessa maneira pelo terminal:

  1- Ache seu dispositivo. Ele deve aparecer assim, por exemplo: "0bda:1a2b"
  Digite para listar seus dispositivos USB:
  
  `lsusb`
  
  2- Troque o modo de execução pelos comandos (Lembrando, que o ID tem que se do SEU dispositivo):
  
  ```
  sudo usb_modeswitch -KW -v 0bda -p 1a2b
  systemctl start bluetooth.service
  ```
  
  Este último comando serve para você ativar seu serviço do Bluetooth, caso ele esteja desativado.
  
  ### Deixe a mudança permanente
  Se deu tudo certo nos passos anteriores, para você deixar dessa maneira quando você re-ligar seu PC, você vai precisar editar o `usb_modeswitch`:
  
  1- Editando as regras do `usb_modeswitch`:
  
  `sudo nano /lib/udev/rules.d/40-usb_modeswitch.rules`
  
  2- Anexe no final a linha ANTES da linha `LABEL="modeswitch_rules_end"` o seguinte:
  
  ```
  # Realtek 8211CU Wifi AC USB
ATTR{idVendor}=="0bda", ATTR{idProduct}=="1a2b", RUN+="/usr/sbin/usb_modeswitch -K -v 0bda -p 1a2b"
```

## Fazendo o 'build' e 'install' sem o DKMS
Use os seguintes comandos:

```
cd ~/build/rtl8821CU
make
sudo make install
```

E se quiser remove-lo depois, use:

```
cd ~/build/rtl8821CU
sudo make uninstall
```

Para mim funcionou. Fiz primeiro o `build` e o `install` sem o DKMS primeiro. Não havia funcionado, então fiz com o DKMS, e então funcionou.
Há um pouco mais de informação no repositório original que recomendo verem. Espero ter ajudado.
