---
title: Configuração grub proativa do Console Serial Azure| Microsoft Docs
description: Configure o GRUB em várias distribuições, permitindo acesso a um único usuário e modo de recuperação em máquinas virtuais do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: mimckitt
ms.openlocfilehash: 573bd0797e63fc512e59b0e0882c718e4569111c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262886"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Garantir proativamente que você tenha acesso ao GRUB e sysrq pode economizar muito tempo de inatividade

Ter acesso ao Console Serial e AO GRUB melhorará os tempos de recuperação da sua Máquina Virtual Linux IaaS na maioria dos casos. O GRUB oferece opções de recuperação que, de outra forma, levariam mais tempo para recuperar sua VM. 


As razões para realizar uma recuperação de VM são muitas e podem ser atribuídas a cenários como:

   - Sistemas de arquivos corrompidos/kernel/MBR (Registro de inicialização mestre)
   - Atualizações de kernel com falha
   - Parâmetros incorretos do kernel GRUB
   - Configurações incorretas de fstab
   - Configurações de firewall
   - Senha perdida
   - Arquivos de configurações sshd mutilados
   - Configurações de rede

 Muitos outros cenários como detalhado [aqui](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console)

Verifique se você pode acessar grub e o console Serial em suas VMs implantadas no Azure. 

Se você é novo no Console Serial, consulte [este link](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/).

> [!TIP]
> Certifique-se de fazer backups de arquivos antes de fazer alterações

Assista a este vídeo abaixo para ver como você pode recuperar rapidamente sua VM Linux uma vez que você tem acesso ao GRUB

[Recuperar vídeo Linux VM](https://youtu.be/KevOc3d_SG4)

Existem uma série de métodos para ajudar na recuperação de VMs Linux. Em um ambiente cloud, esse processo tem sido desafiador.
O progresso está sendo feito continuamente para ferramentas e recursos para garantir que os serviços sejam recuperados rapidamente.

Com o Console Serial Azure, você pode interagir com o seu VM Linux como se estivesse no console de um sistema.

Você pode manipular muitos arquivos de configuração, incluindo como o kernel será inicializar. 

Os administradores mais experientes do Linux/Unix apreciarão os **modos** **de emergência e único usuário** que são acessíveis através do Console Serial Azure, tornando a exclusão de Disk Swap e VM para muitos cenários de recuperação redundantes.

O método de recuperação depende do problema que está sendo experimentado, por exemplo, uma senha perdida ou extraviada pode ser redefinida através das opções do portal Azure -> **Redefinir senha**. O recurso **Redefinir senha** é conhecido como uma extensão e se comunica com o agente convidado do Linux.

Outras extensões como o Custom Script estão disponíveis, no entanto, essas opções exigem que o **waagent** Linux esteja em um estado saudável, o que nem sempre é o caso.

![status agente](./media/virtual-machines-serial-console/agent-status.png)


Garantir que você tenha acesso ao Azure Serial Console e AO GRUB significa que uma alteração de senha ou uma configuração incorreta pode ser corrigida em questão de minutos em vez de horas. Você pode até forçar o VM a inicializar a partir de um kernel alternativo caso você tenha vários kernels no disco no cenário em que seu kernel principal se torna corrompido.

![multi kernel](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Ordem sugerida de métodos de recuperação:

- Azure Serial Console

- Disk Swap – pode ser automatizado usando qualquer um:

   - [Scripts de recuperação da concha de energia](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [scripts de recuperação bash](https://github.com/sribs/azure-support-scripts)

- Método Legado

## <a name="disk-swap-video"></a>Vídeo de troca de disco:

Se você não tiver acesso ao GRUB assista [a este](https://youtu.be/m5t0GZ5oGAc) vídeo e veja, como você pode automatizar facilmente o procedimento de troca de disco para recuperar sua VM

## <a name="challenges"></a>Desafios:

Nem todas as VMs do Linux Azure são configuradas por padrão para acesso GRUB e nem todas estão configuradas para serem interrompidas com os comandos sysrq. Algumas distros mais antigas, como o SLES 11, não estão configuradas para exibir o prompt de login no Console Serial Azure

Neste artigo, vamos rever várias distribuições Linux e configurações de documentos sobre como disponibilizar o GRUB.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Como configurar o Linux VM para aceitar chaves SysRq
A chave sysrq é habilitada em algumas distros Linux mais novas por padrão, embora em outras possa ser configurada para aceitar valores apenas para certas funções SysRq.
Em distros mais antigos, pode ser desativado completamente.

O recurso SysRq é útil para reiniciar um VM acidentado ou pendurado diretamente do Console Serial Azure, também útil para obter acesso ao menu GRUB, alternativamente reiniciar uma VM de outra janela de portal ou sessão ssh pode soltar sua conexão atual do console, expirando assim os tempos de GRUB para os quais são usados para exibir o menu GRUB.
O VM deve ser configurado para aceitar um valor de 1 para o parâmetro do kernel, que permite todas as funções de sysrq ou 128, o que permite reinicialização/poweroff


[Ativar vídeo sysrq](https://youtu.be/0doqFRrHz_Mc)


Para configurar a VM para aceitar uma reinicialização através de comandos SysRq no portal Azure, você precisará definir um valor de 1 para o kernel parameter kernel.sysrq

Para que essa configuração persista uma reinicialização, adicione uma entrada ao arquivo **sysctl.conf**

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Para configurar o parâmetro do kernel dinamicamente

`sysctl -w kernel.sysrq=1`

Se você não tiver acesso **raiz** ou o sudo estiver quebrado, não será possível configurar sysrq a partir de um prompt shell.

Você pode habilitar sysrq neste cenário usando o portal Azure. Este método pode ser benéfico se o arquivo **sudoers.d/waagent** tiver sido quebrado ou tiver sido excluído.

Usando o recurso Operações do portal Azure - > Run Command -> RunShellScript, requer que o processo waagent seja saudável, você pode então injetar este comando para ativar o sysrq

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Como mostrado ![aqui: habilitar sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

Uma vez concluído, você pode tentar acessar **sysrq** e ver se uma reinicialização é possível.

![habilitar sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Selecione **'Reinicializar** e Enviar Comando **SysRq'**

![habilitar sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

O sistema deve registrar uma mensagem de reset como esta

![habilitar sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Configuração do Ubuntu GRUB

Por padrão, você deve ser capaz de acessar grub segurando a tecla **Esc** durante a inicialização do VM, se o menu GRUB não for apresentado você pode forçar e manter o menu GRUB na tela no Console Serial Do Azure usando uma dessas opções.

**Opção 1** - Força grub a ser exibido na tela 

Atualize o arquivo /etc/default/grub.d/50-cloudimg-settings.cfg para manter o menu GRUB na tela para o TEMPO HÁUM especificado.
Você não é obrigado a bater **Esc** como GRUB será exibido imediatamente

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Opção 2** - Permite que **esc** seja pressionado antes de inicializar

Comportamento semelhante pode ser experimentado fazendo alterações no arquivo /etc/default/grub e observar um tempo de 3 segundos para bater **Esc**


Comente estas duas linhas:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
e adicione esta linha:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

O Ubuntu 12.04 permitirá o acesso ao console serial, mas não oferece a capacidade de interagir. Um **login:** prompt não é visto


Para 12.04 para obter um **login:** prompt:
1. Crie um arquivo chamado /etc/init/ttyS0.conf contendo o seguinte texto:

    ```
    # ttyS0 - getty
    #
    # This service maintains a getty on ttyS0 from the point the system is
    # started until it is shut down again.
    start on stopped rc RUNLEVEL=[12345]
    stop on runlevel [!12345]
    
    respawn
    exec /sbin/getty -L 115200 ttyS0 vt102
    ```    

2. Peça para começar o getty     
    ```
    sudo start ttyS0
    ```
 
As configurações necessárias para configurar o console serial para versões ubuntu podem ser encontradas [aqui](https://help.ubuntu.com/community/SerialConsoleHowto)

## <a name="ubuntu-recovery-mode"></a>Modo de recuperação do Ubuntu

Opções adicionais de recuperação e limpeza estão disponíveis para o Ubuntu via GRUB, no entanto, essas configurações só são acessíveis se você configurar os parâmetros do kernel de acordo.
A falha na configuração deste parâmetro de inicialização do kernel forçaria o menu Recuperação a ser enviado para o Azure Diagnostics e não para o Azure Serial Console.
Você pode obter acesso ao Menu de Recuperação do Ubuntu seguindo estas etapas:

Interrompa o processo BOOT e acesse o menu GRUB

Selecione Opções Avançadas para Ubuntu e pressione enter

![ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

Selecione a linha de exibição *(modo de recuperação)* não pressione enter, mas pressione "e"

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

Localize a linha que carregará o kernel e substitua o último parâmetro **nomodeset** com o destino como **console=ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

Pressione **Ctrl-x** para iniciar e carregar o kernel.
Se tudo correr bem, você verá essas opções adicionais, que podem ajudar a executar outras opções de recuperação

![ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Configuração red hat GRUB

## <a name="red-hat-74-grub-configuration"></a>Configuração\.red\+ hat 7 4 GRUB
A configuração padrão /etc/default/grub nessas versões está configurada adequadamente

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_SERIAL_COMMAND="serial"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Habilite a tecla SysRq

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Red Hat\.7\.2 e 7 3 GRUB configuração
O arquivo a modificar é /etc/default/grub – uma configuração padrão se parece com este exemplo:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Alterar as seguintes linhas em /etc/default/grub

```
GRUB_TIMEOUT=1 

to

GRUB_TIMEOUT=5
```


```
GRUB_TERMINAL_OUTPUT="console"

to

GRUB_TERMINAL="serial console"
```

Adicione também esta linha:

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

/etc/default/grub deve agora ser semelhante a este exemplo:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
Complete e atualize a configuração do grub usando

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Defina o parâmetro do kernel SysRq:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

Você pode, alternativamente, configurar GRUB e SysRq usando uma única linha no shell ou através do Comando Executar. Faça backup de seus arquivos antes de executar este comando:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Configuração\.red hat 6 x GRUB
O arquivo a ser modificado é /boot/grub/grub.conf. O `timeout` valor determinará por quanto tempo o GRUB é mostrado.

```
#boot=/dev/vda
default=0
timeout=15
splashimage=(hd0,0)/grub/splash.xpm.gz
#hiddenmenu
serial --unit=0 --speed=9600
terminal serial
terminal --timeout=5 serial console
```


O último terminal de linha *–-timeout=5 console serial* aumentará ainda mais o tempo limite **do GRUB** adicionando um prompt de 5 segundos exibindo Pressionar **qualquer tecla para continuar.**

![rh6-1](./media/virtual-machines-serial-console/rh6-1.png)

O menu GRUB deve aparecer na tela para o tempo livre configurado=15 sem a necessidade de pressionar Esc. Certifique-se de clicar no Console no Navegador para tornar ativo o menu e selecionar o kernel necessário

![rh6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>Suse

## <a name="sles-12-sp1"></a>SLES 12 sp1
Ou use o bootloader yast de acordo com os [documentos](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles) oficiais

Ou adicionar/alterar para /etc/default/grub os seguintes parâmetros:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Verifique se o ttys0 é usado no GRUB_CMDLINE_LINUX ou GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Recriar o grub.cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
O Console Serial aparece e exibe mensagens de inicialização, mas não exibe um **login:** prompt

Abra uma sessão ssh no VM e atualize o arquivo **/etc/inittab** não comentando esta linha:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Em seguida, execute o comando 

`telinit q`

Para habilitar o GRUB, as seguintes alterações devem ser feitas em /boot/grub/menu.lst 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Esta configuração permitirá que a mensagem **Pressione qualquer tecla** para continuar a aparecer no console por 5 segundos 

Em seguida, ele exibirá o menu GRUB por mais 5 segundos - pressionando a seta para baixo, você interromperá o contador e selecionará um kernel que deseja inicializar ou anexar a palavra-chave **single** para um único usuário que requer senha raiz a ser definida.

Anexar o comando **init=/bin/bash** carregará o kernel, mas garante que o programa init seja substituído por uma concha de corte.

Você terá acesso a um shell sem ter que digitar uma senha. Em seguida, você pode proceder à atualização de senha para contas Linux ou fazer outras alterações de configuração.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Force o kernel a um prompt de bash
Ter acesso ao GRUB permite interromper o processo de inicialização essa interação é útil para muitos procedimentos de recuperação.
Se você não tiver senha raiz e o usuário único exigir que você tenha uma senha raiz, você pode inicializar o kernel substituindo o programa init por um prompt de bash – essa interrupção pode ser alcançada anexando init=/bin/bash à linha de inicialização do kernel

![bash1](./media/virtual-machines-serial-console/bash1.png)

Remontar seu RW do sistema de arquivos / (raiz) usando o comando

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


Agora você pode executar a alteração de senha raiz ou muitas outras alterações de configuração do Linux

![bash3](./media/virtual-machines-serial-console/bash3.png)

Reiniciar a VM com 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Modo de usuário único

Alternativamente, você pode precisar acessar a VM em um único usuário ou modo de emergência. Selecione o kernel que deseja inicializar ou interromper usando teclas de seta.
Digite o modo desejado anexando a palavra-chave **single** ou **1** à linha de inicialização do kernel. Em sistemas RHEL, você também pode anexar **rd.break**.

Para obter mais informações sobre como acessar o modo de usuário único, consulte [este doc](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [o Azure Serial Console]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)
