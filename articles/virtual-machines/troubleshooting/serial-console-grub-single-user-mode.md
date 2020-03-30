---
title: Console serial azure para GRUB e modo de usuário único | Microsoft Docs
description: Este artigo descreve como usar o Serial Console para GRUB em máquinas virtuais Do Zure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: alsin
ms.openlocfilehash: 06cb3fe5d551ddfc95fcbd37cd9620adebd825c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70883936"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Use o Serial Console para acessar grub e modo de usuário único
GRand Unified Bootloader (GRUB) é provavelmente a primeira coisa que você vê quando inicializa uma máquina virtual (VM). Como é exibido antes do sistema operacional começar, o GRUB não é acessível via SSH. No GRUB, você pode modificar sua configuração de inicialização para inicializar no modo de usuário único, entre outras coisas.

O modo de um único usuário é um ambiente mínimo com funcionalidade mínima. Ele pode ser útil para investigar problemas de inicialização, problemas no sistema de arquivos ou problemas de rede. Menos serviços podem ser executados em segundo plano e, dependendo do nível de execução, um sistema de arquivos pode nem mesmo ser montado automaticamente.

O modo de usuário único também é útil em situações em que sua VM pode ser configurada para aceitar apenas chaves SSH para login. Neste caso, você pode ser capaz de usar o modo de usuário único para criar uma conta com autenticação de senha. 

> [!NOTE]
> O serviço Serial Console permite que apenas usuários com nível *de contribuinte* ou permissões superiores acessem o console serial de uma VM.

Para entrar no modo de usuário único, digite GRUB quando sua VM estiver inicializando e modifique a configuração de inicialização no GRUB. Consulte instruções detalhadas para inserir grub na próxima seção. Em geral, se a VM foi configurada para exibir grub, você pode usar o botão de reinicialização dentro do console serial da VM para reiniciar a VM e exibir GRUB.

![O botão de reinicialização do console serial linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Acesso geral ao GRUB
Para acessar o GRUB, reinicie sua VM enquanto o painel serial console estiver aberto. Algumas distribuições exigem a entrada do teclado para mostrar grub, e outras mostram automaticamente grub por alguns segundos para permitir que a entrada do teclado do usuário cancele o tempo.

Para poder acessar o modo de usuário único, você deseja garantir que o GRUB esteja ativado em sua VM. Dependendo da sua distribuição, alguns trabalhos de configuração podem ser necessários para garantir que o GRUB esteja habilitado. Para obter informações específicas de distribuição, consulte a próxima seção e nosso suporte para Linux na página [DoZure.](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Reiniciar a VM para acessar o GRUB no Console Serial
Você pode reiniciar sua VM no Console Serial pairando sobre o botão **Reiniciar** e, em seguida, selecionando **Reiniciar VM**. Uma notificação sobre a reinicialização é exibida na parte inferior do painel.

Você também pode reiniciar sua VM executando um comando SysRq "b" se [o SysRq](./serial-console-nmi-sysrq.md) estiver habilitado. Para saber o que esperar do GRUB ao reiniciar, consulte as instruções específicas de distribuição nas próximas seções.

![Reinicialização do Linux Serial Console](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Acesso geral ao modo de um usuário
Você pode precisar de acesso manual ao modo de usuário único quando não tiver configurado uma conta com autenticação de senha. Modifique a configuração GRUB para entrar manualmente no modo de um usuário único. Depois de fazer isso, consulte a seção "Usar o modo de usuário único para redefinir ou adicionar uma senha" para obter instruções adicionais.

Se a VM não puder inicializar, as distribuições geralmente o deixam automaticamente no modo de usuário único ou no modo de emergência. Outras distribuições, no entanto, exigem configuração adicional, como a configuração de uma senha raiz, antes que eles possam deixá-lo no modo de uso único ou de emergência automaticamente.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Use o modo de usuário único para redefinir ou adicionar uma senha
Depois de estar no modo de usuário único, adicione um novo usuário com privilégios de sudo fazendo o seguinte:
1. Execute `useradd <username>` para adicionar um usuário.
1. Execute `sudo usermod -a -G sudo <username>` para conceder os novos privilégios de raiz do usuário.
1. Use `passwd <username>` para definir a senha do novo usuário. Em seguida, você pode fazer login como o novo usuário.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Acesso para Red Hat Enterprise Linux (RHEL)
Se o RHEL não puder inicializar normalmente, ele o joga automaticamente no modo de um único usuário. No entanto, se você não tiver configurado acesso raiz para o modo de usuário único, você não tem uma senha raiz e não pode fazer login. Há uma solução (consulte a seção "Digite manualmente o modo de um usuário único no RHEL"), mas sugerimos que você configure o acesso raiz inicialmente.

### <a name="grub-access-in-rhel"></a>Acesso ao GRUB no RHEL
O RHEL vem com o GRUB habilitado pronto para uso. Para entrar no GRUB, reinicie sua VM executando `sudo reboot`e pressione qualquer tecla. O painel GRUB deve ser exibido. Se não for, certifique-se de que as seguintes linhas estejam presentes no seu arquivo GRUB ():`/etc/default/grub`

**Para RHEL 8**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**Para RHEL 7**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> O Red Hat também fornece documentação para inicialização no Modo resgate, modo de emergência ou modo de depuração, e para redefinir a senha raiz. Para obter instruções, consulte [Edição do menu Terminal durante a inicialização](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Configure o acesso raiz para o modo de um usuário único no RHEL
O usuário raiz é desativado por padrão. O modo de um usuário único no RHEL requer que o usuário raiz seja ativado. Se você precisar ativar o modo de usuário único, use as seguintes instruções:

1. Faça login no sistema Red Hat via SSH.
1. Mude para raiz.
1. Habilite a senha para o usuário raiz fazendo o seguinte:
    * Executar `passwd root` (definir uma senha raiz forte).
1. Certifique-se de que o usuário raiz pode fazer login somente via ttyS0 fazendo o seguinte:  
    a. Executar `edit /etc/ssh/sshd_config`e garantir que o PermitRootLogIn esteja definido como `no`.  
    b. Execute `edit /etc/securetty file` para permitir o login apenas via ttyS0.

Agora, se o sistema for inicializado no modo de usuário único, você pode fazer login com a senha raiz.

Alternativamente, para RHEL 7.4+ ou 6.9+, para ativar o modo de usuário único nas solicitações GRUB, consulte [Inicialização no modo de usuário único](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single).

### <a name="manually-enter-single-user-mode-in-rhel"></a>Digite manualmente o modo de um usuário em RHEL
Se você configurar o GRUB e o acesso raiz usando as instruções anteriores, você pode entrar no modo de usuário único fazendo o seguinte:

1. Para entrar no GRUB, pressione Esc ao reiniciar a VM.
1. No GRUB, pressione E para editar o sistema operacional que você deseja inicializar. O Sistema Operacional é geralmente listado na primeira linha.
1. Encontre a linha do kernel. No Azure, ele começa com *o linux16*.
1. Pressione Ctrl+E para ir até o final da linha.
1. No final da linha, adicione *systemd.unit=rescue.target*.
    
    Esta ação inicializa você no modo de usuário único. Se você quiser usar o modo de emergência, adicione *systemd.unit=emergency.target* ao final da linha (em vez de *systemd.unit=rescue.target*).

1. Pressione Ctrl+X para sair e reiniciar com as configurações aplicadas.

   Você será solicitado para a senha do administrador antes de entrar no modo de usuário único. Esta senha é a que você criou nas instruções anteriores.

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Digite o modo de usuário único sem conta raiz ativada no RHEL
Se você não habilitou o usuário raiz seguindo as instruções anteriores, você ainda pode redefinir sua senha raiz fazendo o seguinte:

> [!NOTE]
> Se você estiver usando o SELinux, ao redefinir a senha raiz, certifique-se de seguir as etapas adicionais descritas na [documentação](https://aka.ms/rhel7grubterminal)do Red Hat .

1. Para entrar no GRUB, pressione Esc ao reiniciar a VM.

1. No GRUB, pressione E para editar o sistema operacional que você deseja inicializar. O Sistema Operacional é geralmente listado na primeira linha.
1. Encontre a linha do kernel. No Azure, ele começa com *o linux16*.
1. No final da linha, adicione *rd.break* ao final da linha. Deixe um espaço entre a linha do kernel e *rd.break*.

    Esta ação interrompe o processo de `initramfs` inicialização antes que o controle seja passado para `systemd`, conforme descrito na [documentação do Red Hat](https://aka.ms/rhel7rootpassword).
1. Pressione Ctrl+X para sair e reiniciar com as configurações aplicadas.

   Depois de reiniciado, você é lançado no modo de emergência com um sistema de arquivos somente leitura. 
   
1. No shell, `mount -o remount,rw /sysroot` digite para remontar o sistema de arquivos raiz com permissões de leitura/gravação.
1. Depois de inicializar no modo `chroot /sysroot` de usuário `sysroot` único, entre para mudar para a cadeia.
1. Agora você está na raiz. Você pode redefinir sua `passwd` senha raiz digitando e, em seguida, usar as instruções anteriores para entrar no modo de usuário único. 
1. Depois de terminar, `reboot -f` entre para reiniciar.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> Passar pelas instruções anteriores coloca você na camada de emergência para `fstab`que você também possa executar tarefas como edição . No entanto, normalmente sugerimos que você redefinisse sua senha raiz e a usasse para entrar no modo de usuário único.

## <a name="access-for-centos"></a>Acesso para o CentOS
Assim como o Red Hat Enterprise Linux, o modo de usuário único no CentOS requer que o GRUB e o usuário raiz sejam ativados.

### <a name="grub-access-in-centos"></a>Acesso ao GRUB no CentOS
O CentOS vem com o GRUB habilitado pronto para uso. Para entrar no GRUB, reinicie sua VM inserindo `sudo reboot`e pressione qualquer tecla. Esta ação exibe o painel GRUB.

### <a name="single-user-mode-in-centos"></a>Modo de usuário único no CentOS
Para ativar o modo de usuário único no CentOS, siga as instruções anteriores para RHEL.

## <a name="access-for-ubuntu"></a>Acesso para Ubuntu
As imagens do Ubuntu não exigem uma senha raiz. Se o sistema for inicializado no modo de usuário único, você pode usá-lo sem credenciais adicionais.

### <a name="grub-access-in-ubuntu"></a>Acesso ao GRUB no Ubuntu
Para acessar grub, pressione e segure Esc enquanto a VM estiver inicializando.

Por padrão, as imagens do Ubuntu podem não exibir automaticamente o painel GRUB. Você pode alterar a configuração fazendo o seguinte:
1. Em um editor de texto, abra o arquivo */etc/default/grub.d/50-cloudimg.cfg.*

1. Mude `GRUB_TIMEOUT` o valor para um valor não-zero.
1. Em um editor de texto, abra */etc/default/grub*.
1. Comente `GRUB_HIDDEN_TIMEOUT=1` a linha.
1. Certifique-se de `GRUB_TIMEOUT_STYLE=menu` que há uma linha.
1. Execute `sudo update-grub`.

### <a name="single-user-mode-in-ubuntu"></a>Modo de usuário único no Ubuntu
Se o Ubuntu não puder inicializar normalmente, ele o colocará automaticamente no modo de um único usuário. Para inserir o modo de um usuário manualmente, faça o seguinte:

1. No GRUB, pressione E para editar sua entrada de inicialização (a entrada do Ubuntu).
1. Procure a linha que começa com *linux*, e depois *procure*ro .
1. Adicione *um único* após *ro,* garantindo que haja um espaço antes e depois *do single*.
1. Pressione Ctrl+X para reiniciar com essas configurações e entrar no modo de usuário único.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>Use GRUB para invocar bash no Ubuntu
Depois de experimentar as instruções anteriores, pode haver uma situação (como uma senha raiz esquecida) onde você ainda não consegue acessar o modo de usuário único em sua VM Ubuntu. Você também pode dizer `/bin/bash` ao kernel para executar como init, em vez de o sistema init. Esta ação lhe dá uma concha de corte e permite a manutenção do sistema. Use as instruções a seguir:

1. No GRUB, pressione E para editar sua entrada de inicialização (a entrada do Ubuntu).

1. Procure a linha que começa com *linux*, e depois *procure*ro .
1. Substitua *ro* por *rw init=/bin/bash*.

    Esta ação monta seu sistema de arquivos `/bin/bash` como leitura-gravação e usa como o processo init.
1. Pressione Ctrl+X para reiniciar com estas configurações.

## <a name="access-for-coreos"></a>Acesso para CoreOS
O modo de um usuário único no CoreOS requer que o GRUB seja ativado.

### <a name="grub-access-in-coreos"></a>Acesso ao GRUB no CoreOS
Para acessar o GRUB, pressione qualquer tecla enquanto sua VM estiver inicializando.

### <a name="single-user-mode-in-coreos"></a>Modo de usuário único no CoreOS
Se o CoreOS não puder inicializar normalmente, ele o coloca automaticamente no modo de um usuário único. Para inserir o modo de um usuário manualmente, faça o seguinte:

1. Em GRUB, pressione E para editar sua entrada de inicialização.

1. Procure a linha que começa com *linux$*. Deve haver duas instâncias da linha, cada uma encapsulada em um diferente *se... outra* cláusula.
1. Apêndice *coreos.autologin=ttyS0* até o final de cada linha *linux$.*
1. Pressione Ctrl+X para reiniciar com essas configurações e entrar no modo de usuário único.

## <a name="access-for-suse-sles"></a>Acesso para SUSE SLES
Imagens mais novas do SLES 12 SP3+ permitem acesso através do console serial se o sistema inicializa em modo de emergência.

### <a name="grub-access-in-suse-sles"></a>Acesso ao GRUB no SUSE SLES
O acesso GRUB no SLES requer uma configuração de bootloader via YaST. Para criar a configuração, faça o seguinte:

1. Use o SSH para fazer login no Seu `sudo yast bootloader`VM SLES e, em seguida, execute . Pressione Tab, pressione Enter e use as teclas de seta para navegar pelo menu.

1. Vá para **Parâmetros do Kernel**e selecione a caixa de seleção **Usar console serial.**
1. Adicione `serial --unit=0 --speed=9600 --parity=no` aos argumentos **do Console.**
1. Pressione F10 para salvar suas configurações e sair.
1. Para entrar no GRUB, reinicie sua VM e pressione qualquer tecla durante a seqüência de inicialização para manter o painel GRUB exibido.

    O tempo de intervalo padrão para GRUB é **1s**. Você pode modificar essa `GRUB_TIMEOUT` configuração alterando a variável no arquivo */etc/default/grub.*

![Inicializando a configuração do bootloader](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Modo de usuário único no SUSE SLES
Se sles não pode inicializar normalmente, você é automaticamente jogado no shell de emergência. Para entrar manualmente no shell de emergência, faça o seguinte:

1. Em GRUB, pressione E para editar sua entrada de inicialização (a entrada SLES).

1. Procure a linha do kernel que começa com *o linux*.
1. Anexar *systemd.unit=emergency.target* até o final da linha do kernel.
1. Pressione Ctrl+X para reiniciar com estas configurações e entrar na camada de emergência.

   > [!NOTE]
   > Esta ação coloca você no shell de emergência com um sistema de arquivos somente leitura. Para editar quaisquer arquivos, remonte o sistema de arquivos com permissões de leitura e gravação. Para isso, entre `mount -o remount,rw /` na concha.

## <a name="access-for-oracle-linux"></a>Acesso para o Oracle Linux
Assim como o Red Hat Enterprise Linux, o modo de usuário único no Oracle Linux requer grub e o usuário raiz para ser ativado.

### <a name="grub-access-in-oracle-linux"></a>Acesso ao GRUB no Oracle Linux
O Oracle Linux vem com o GRUB habilitado pronto para uso. Para entrar no GRUB, reinicie seu VM executando `sudo reboot`e pressione Esc. Esta ação exibe o painel GRUB. Se o painel GRUB não for exibido, certifique-se de que o `GRUB_TERMINAL` valor da linha contém console *serial* (isto é, `GRUB_TERMINAL="serial console"`). Reconstruir grub `grub2-mkconfig -o /boot/grub/grub.cfg`com .

### <a name="single-user-mode-in-oracle-linux"></a>Modo de usuário único no Oracle Linux
Para ativar o modo de usuário único no Oracle Linux, siga as instruções anteriores para RHEL.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Serial Console, consulte:
* [Documentação do Linux Serial Console](serial-console-linux.md)
* [Use o Serial Console para habilitar o GRUB em várias distribuições](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [Use o Console Serial para chamadas NMI e SysRq](serial-console-nmi-sysrq.md)
* [Console serial para VMs windows](serial-console-windows.md)
* [Diagnóstico de inicialização](boot-diagnostics.md)
