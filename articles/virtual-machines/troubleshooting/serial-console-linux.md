---
title: Console serial azure para Linux | Microsoft Docs
description: Console serial bidirecional para máquinas virtuais do Azure e conjuntos de escala de máquinas virtuais.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: b1f7708c9bd213e201ba4eb8837a191dca68ca9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167012"
---
# <a name="azure-serial-console-for-linux"></a>Console serial do Azure para Linux

O Console Serial no portal Azure fornece acesso a um console baseado em texto para máquinas virtuais Linux (VMs) e instâncias de conjunto de escala de máquinas virtuais. Esta conexão serial conecta-se à porta serial ttys0 da instância de conjunto de escala de VM ou máquina virtual, fornecendo acesso a ela independente do estado de rede ou sistema operacional. O console serial só pode ser acessado usando o portal Azure e é permitido apenas para aqueles usuários que têm uma função de acesso de Contribuinte ou superior ao conjunto de escala sem vm ou máquina virtual.

O Console Serial funciona da mesma maneira para VMs e instâncias de conjunto de escala de máquinavirtual. Neste documento, todas as menções às VMs incluirão implicitamente instâncias de conjunto de escala de máquina virtual, a menos que seja indicado de outra forma.

Para obter a documentação do console serial para Windows, consulte [Console serial para Windows](../windows/serial-console.md).

> [!NOTE]
> O Console Serial está geralmente disponível em regiões globais do Azure e em visualização pública no Governo Azure. Ainda não está disponível na nuvem do Azure China.


## <a name="prerequisites"></a>Pré-requisitos

- A instância de conjunto de escala de vm ou de máquina virtual deve usar o modelo de implantação de gerenciamento de recursos. Implantações clássicas não são suportadas.

- Sua conta que usa console serial deve ter a [função de Contribuinte de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para a VM e a conta de armazenamento de diagnóstico de [inicialização](boot-diagnostics.md)

- A instância de definição de escala de VM ou máquina virtual deve ter um usuário baseado em senha. Você pode criar um com a função [reset password](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) da extensão de acesso da VM. Selecione **Redefinir senha** na seção **Suporte + solução de problemas**.

- A instância de conjunto de escala de VM ou máquina virtual deve ter [diagnósticos de inicialização ativados.](boot-diagnostics.md)

    ![Configurações de diagnóstico de inicialização](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Para configurações específicas das distribuições do Linux, consulte [Disponibilidade de distribuição Linux do console serial](#serial-console-linux-distribution-availability).

- A instância do conjunto de escala da vm `ttys0`ou da máquina virtual deve ser configurada para a saída serial em . Este é o padrão para imagens do Azure, mas você vai querer verificar isso duas vezes em imagens personalizadas. Detalhes [abaixo](#custom-linux-images).


> [!NOTE]
> O console serial requer um usuário local com uma senha configurada. VMs ou conjuntos de escala de máquina virtual configurados apenas com uma chave pública SSH não poderão fazer login no console serial. Para criar um usuário local com uma senha, use a [Extensão do VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), disponível no portal selecionando **Redefinir senha** no portal do Azure e crie um usuário local com uma senha.
> Você também pode redefinir a senha do administrador em sua conta [usando o GRUB para inicializar no modo de usuário único](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Disponibilidade de distribuição Serial Console Linux
Para o console serial funcionar corretamente, o sistema operacional convidado deve ser configurado para ler e gravar mensagens do console na porta serial. A maioria [das distribuições Azure Linux endossadas](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) tem o console serial configurado por padrão. A seleção do **console serial** na seção **Support + troubleshooting** do portal do Azure fornece acesso ao console serial.

> [!NOTE]
> Se você não estiver vendo nada no console serial, verifique se o diagnóstico de inicialização está habilitado na VM. Bater **Enter** muitas vezes corrigirá problemas onde nada está aparecendo no console serial.

Distribuição      | Acesso ao console serial
:-----------|:---------------------
Red Hat Enterprise Linux    | Acesso ao console serial habilitado por padrão.
CentOS      | Acesso ao console serial habilitado por padrão.
Debian      | Acesso ao console serial habilitado por padrão.
Ubuntu      | Acesso ao console serial habilitado por padrão.
CoreOS      | Acesso ao console serial habilitado por padrão.
SUSE        | Imagens mais recentes do SLES disponíveis no Azure têm acesso ao console serial ativado por padrão. Se você estiver usando versões mais antigas (10 ou anteriores) do SLES no Azure, consulte o [artigo do KB](https://www.novell.com/support/kb/doc.php?id=3456486) para ativar o console serial.
Oracle Linux        | Acesso ao console serial habilitado por padrão.

### <a name="custom-linux-images"></a>Imagens personalizadas do Linux
Para ativar o console serial para sua imagem customizada da VM Linux, ative o acesso ao console no arquivo */ etc / inittab* para executar um terminal em`ttyS0`. Por exemplo: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Você também pode precisar gerar um getty em ttyS0. Isso pode ser `systemctl start serial-getty@ttyS0.service`feito com .

Você também vai querer adicionar ttys0 como o destino para a saída em série. Para obter mais informações sobre a configuração de uma imagem personalizada para trabalhar com o console serial, consulte os requisitos gerais do sistema em [Criar e carregar um VHD Linux no Azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Se você está construindo um kernel personalizado, considere habilitar esses flags do kernel: `CONFIG_SERIAL_8250=y` e `CONFIG_MAGIC_SYSRQ_SERIAL=y`. O arquivo de configuração normalmente está localizado na */boot/* caminho.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Cenários comuns para acessar o Console Serial

Cenário          | Ações no Console Serial
:------------------|:-----------------------------------------
Arquivo *FSTAB* quebrado | Pressione a tecla **Enter** para continuar e use um editor de texto para corrigir o arquivo *FSTAB*. Você pode precisar estar no modo de usuário único para fazer isso. Para obter mais informações, consulte a seção de console serial de [Como corrigir problemas](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) de fstab e [Use console serial para acessar grub e modo de usuário único](serial-console-grub-single-user-mode.md).
Regras de firewall incorretas |  Se você tiver configurado iptables para bloquear a conectividade SSH, você pode usar o console serial para interagir com sua VM sem precisar de SSH. Mais detalhes podem ser encontrados na [página iptables man](https://linux.die.net/man/8/iptables).<br>Da mesma forma, se o firewalld estiver bloqueando o acesso ao SSH, você pode acessar a VM através do console serial e reconfigurar o firewall. Mais detalhes podem ser encontrados na [documentação com firewall.](https://firewalld.org/documentation/)
Corrupção de sistema de arquivos/verificação | Por favor, veja a seção de console serial do [Azure Linux VM não pode começar por causa de erros no sistema de arquivos](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) para obter mais detalhes sobre a solução de problemas de sistemas de arquivos corrompidos usando console serial.
Problemas de configuração do SSH | Acesse o console serial e altere as configurações. O console serial pode ser usado independentemente da configuração SSH de uma VM, pois não requer que a VM tenha conectividade de rede para funcionar. Um guia de solução de problemas está disponível em [conexões SSH de solução de problemas a uma VM Azure Linux que falha, falha ou é recusada](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). Mais detalhes estão disponíveis em [etapas detalhadas de solução de problemas ssh para problemas que se conectam a uma VM Linux no Azure](./detailed-troubleshoot-ssh-connection.md)
Interagir com o carregador de inicialização | Reinicie a VM na folha do console serial para acessar o GRUB na VM do Linux. Para obter mais detalhes e informações específicas da distro, consulte [Usar console serial para acessar grub e modo de usuário único](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Desativar o Console Serial

Por padrão, todas as assinaturas têm acesso ao console serial ativado. Você pode desativar o console serial no nível de assinatura ou no nível de configuração da escala vm/máquina virtual. Para obter instruções detalhadas, visite [Ativar e desativar o Console Serial Azure](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Segurança de console serial

### <a name="access-security"></a>Segurança de acesso
O acesso ao console serial é limitado aos usuários que têm uma função de acesso de [Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ou superior à máquina virtual. Se o locatário do Azure Active Directory exigir autenticação de vários fatores (MFA), o acesso ao console serial também precisará do MFA, pois o acesso ao console serial é feito através do [Portal do Azure](https://portal.azure.com).

### <a name="channel-security"></a>Segurança de canal
Todos os dados enviados são criptografados na rede.

### <a name="audit-logs"></a>Logs de auditoria
Todo o acesso ao console serial está conectado a logs de [diagnósticos de inicialização](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) da máquina virtual. O acesso a esses logs pertence e é controlado pelo administrador da máquina virtual do Azure.

> [!CAUTION]
> Nenhuma senha de acesso para o console seja registrada. No entanto, se comandos executados dentro do console contiverem ou gerarem senhas, segredos, nomes de usuário ou qualquer outra forma de informações de identificação pessoal (PII), eles serão gravados nos logs de diagnóstico de inicialização VM. Eles serão gravados juntamente com todos os outro texto visível, como parte da implementação de scrollback do console serial função. Esses logs são circulares e somente indivíduos com permissões de leitura para a conta de armazenamento de diagnósticos têm acesso a eles. Se você estiver inserindo quaisquer comandos dataor que contenham segredos ou PII, recomendamos o uso de SSH a menos que o console serial seja absolutamente necessário.

### <a name="concurrent-usage"></a>Uso simultâneo
Se um usuário estiver conectado ao console serial e outro usuário solicitar com êxito acesso à mesma máquina virtual, o primeiro usuário será desconectado e o segundo usuário será conectado à mesma sessão.

> [!CAUTION]
> Isso significa que um usuário desconectado não será logado. A capacidade de impor um logout após a desconexão (usando SIGHUP ou mecanismo semelhante) ainda está no roteiro. Para o Windows, há um tempo limite automático ativado no Console Administrativo Especial (SAC); no entanto, para o Linux, você pode definir a configuração de tempo limite do terminal. Para fazer isso, adicione `export TMOUT=600` no arquivo * .bash_profile * ou * .profile * para o usuário que você usa para fazer login no console. Essa configuração expirará a sessão após 10 minutos.

## <a name="accessibility"></a>Acessibilidade
A acessibilidade é um foco fundamental para o Console Serial Azure. Para esse fim, garantimos que o console serial seja totalmente acessível.

### <a name="keyboard-navigation"></a>Navegação de teclado
Use a tecla ** Tab ** no seu teclado para navegar na interface do console serial do portal do Azure. Sua localização será destacada na tela. Para deixar o foco da janela do console serial, pressione **Ctrl**+**F6** no teclado.

### <a name="use-serial-console-with-a-screen-reader"></a>Use o Console Serial com um leitor de tela
O console serial possui suporte ao leitor de tela embutido. Navegar ao redor com um leitor de tela ativado permitirá que o texto alternativo do botão atualmente selecionado seja lido em voz alta pelo leitor de tela.

## <a name="known-issues"></a>Problemas conhecidos
Estamos cientes de alguns problemas com o console serial e o sistema operacional da VM. Aqui está uma lista desses problemas e etapas para mitigação de VMs Linux. Esses problemas e atenuações se aplicam tanto às VMs quanto às instâncias de conjunto de escala de máquinas virtuais. Se estes não corresponderem ao erro que você está vendo, veja os erros comuns de serviço de console serial em [erros comuns do console serial](./serial-console-errors.md).

Problema                           |   Atenuação
:---------------------------------|:--------------------------------------------|
Pressionando **Enter** depois que o banner de conexão não faz com que um prompt de login seja exibido. | O GRUB pode não estar configurado corretamente. Execute os seguintes `grub2-mkconfig -o /etc/grub2-efi.cfg` comandos: e/ou `grub2-mkconfig -o /etc/grub2.cfg`. Para mais informações, consulte [Hitting enter não faz nada](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Esse problema pode ocorrer se você estiver executando uma vm personalizada, um aparelho endurecido ou uma configuração GRUB que faz com que o Linux não se conecte à porta serial.
O texto do console serial ocupa apenas uma parte do tamanho da tela (geralmente depois de usar um editor de texto). | Os consoles seriais não dão suporte à negociação do tamanho da janela ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), o que significa que nenhum sinal SIGWINCH será enviado para atualizar o tamanho da tela e a VM não saberá o tamanho do seu terminal. Instale o xterm ou um utilitário semelhante para fornecer o comando `resize` e, em seguida, execute `resize`.
Colar longas cadeias de caracteres não funciona. | O console serial limita o comprimento de cadeias de caracteres colados em terminal a 2048 caracteres para impedir a sobrecarga da largura de banda de porta serial.
Entrada errática do teclado em imagens BYOS SLES. A entrada do teclado é apenas esporadicamente reconhecida. | Este é um problema com o pacote plymouth. O Plymouth não deve ser executado no Azure, pois você não precisa de uma tela de respingo e o Plymouth interfere na capacidade da plataforma de usar o Serial Console. Remova plymouth `sudo zypper remove plymouth` com e, em seguida, reinicie. Alternativamente, modifique a linha de kernel `plymouth.enable=0` da sua configuração GRUB, anexando até o final da linha. Você pode fazer isso [editando a entrada de inicialização na hora da inicialização,](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)ou editando a linha GRUB_CMDLINE_LINUX em `/etc/default/grub`, reconstruindo grub com e, em `grub2-mkconfig -o /boot/grub2/grub.cfg`seguida, reinicializar.


## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P. Como posso enviar feedback?**

a. Fornecer comentários com a criação de um problema do GitHub em https://aka.ms/serialconsolefeedback. Como alternativa (menos preferencial), você pode enviar comentários por meio azserialhelp@microsoft.com ou na categoria da máquina virtual https://feedback.azure.com.

**P. O console serial suporta cópia/cola?**

a. Sim. Use **ctrl**+**shift**+**C** e **Ctrl**+**Shift**+**V** para copiar e colar no terminal.

**P. Posso usar console serial em vez de uma conexão SSH?**

a. Embora esse uso possa parecer tecnicamente possível, o console serial deve ser usado principalmente como uma ferramenta de solução de problemas em situações em que a conectividade via SSH não é possível. Recomendamos não usar o console serial como substituto de SSH pelos seguintes motivos:

- O console serial não possui muita largura de banda como o SSH. Como é uma conexão somente de texto, mais interações com GUI são difíceis.
- O acesso ao console serial é atualmente possível apenas usando um nome de usuário e senha. Como as chaves SSH são muito mais seguras do que combinações de nome de usuário / senha, do ponto de vista da segurança de login, recomendamos o SSH no console serial.

**P. Quem pode ativar ou desativar console serial para minha assinatura?**

a. Para ativar ou desativar o console serial em um nível de assinatura, você deve ter permissões de gravação para a assinatura. As funções que têm permissão de gravação incluem funções de administrador ou proprietário. Funções personalizadas também podem ter permissões de gravação.

**P. Quem pode acessar o console serial para o conjunto de escalas da máquina VM/virtual?**

a. Você deve ter a função de Contribuinte de Máquina Virtual ou superior para uma escala de VM ou máquina virtual definida para acessar o console serial.

**P. Meu console serial não está exibindo nada, o que eu faço?**

a. Sua imagem provavelmente não está configurada corretamente para acesso ao console serial. Para obter informações sobre como configurar sua imagem para ativar o console serial, consulte [Disponibilidade de distribuição Linux do console serial](#serial-console-linux-distribution-availability).

**P. O console serial está disponível para conjuntos de escala de máquinavirtual?**

a. Sim, é! Ver [console serial para conjuntos de escala de máquinavirtual](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**P. Se eu configurar a minha escala de VM ou máquina virtual definida usando apenas autenticação de chave SSH, ainda posso usar o console serial para se conectar à minha instância de conjunto de escala de vm/máquina virtual?**

a. Sim. Como o console serial não requer chaves SSH, você só precisa configurar uma combinação de nome de usuário / senha. Você pode fazer isso selecionando **Redefinir senha** no portal do Azure e usando essas credenciais para entrar no console serial.

## <a name="next-steps"></a>Próximas etapas
* Use o console serial para [acessar o GRUB e o modo de usuário único](serial-console-grub-single-user-mode.md).
* Usar o console serial para [chamadas NMI e SysRq](serial-console-nmi-sysrq.md).
* Aprenda a usar o console serial para [ativar o GRUB em várias distros](serial-console-grub-proactive-configuration.md)
* O console serial também está disponível para [VMs windows](../windows/serial-console.md).
* Saiba mais sobre [diagnósticos de inicialização](boot-diagnostics.md).

