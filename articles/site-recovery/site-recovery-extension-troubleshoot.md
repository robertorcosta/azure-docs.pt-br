---
title: Solucionar problemas na extensão do Azure VM para recuperação de desastres com a recuperação do site do Azure
description: Solucionaproblemas com a extensão Azure VM para recuperação de desastres com o Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: a780a42179a0bacf0e4a12ba1e75ae84943539b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190716"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Solucionando problemas de extensão do Azure VM

Este artigo fornece etapas de solução de problemas que podem ajudá-lo a resolver erros de recuperação do site do Azure relacionados ao agente e extensão vm.


## <a name="azure-site-recovery-extension-time-out"></a>Tempo de extensão de recuperação do azure site  

Mensagem de erro: "o tempo limite de execução da tarefa foi atingido ao passo que o acompanhamento da operação da extensão iniciou”<br>
Código do erro: "151076"

 O Azure Site Recovery instalou uma extensão na máquina virtual como parte de um trabalho de proteção de habilitação. Qualquer uma das seguintes condições pode impedir que a proteção seja acionada e fazer com que o trabalho falhe. Conclua as seguintes etapas de solução de problemas e, depois, repita a operação:

- [O agente é instalado na VM, mas não responde (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [O agente instalado na VM está desatualizado (para VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [A extensão de Site Recovery falha ao ser atualizada ou carregada](#the-site-recovery-extension-fails-to-update-or-load)

Mensagem de erro: "A operação anterior de extensão de recuperação de site está levando mais tempo do que o esperado."<br>
Código de erro: "150066"

- [O agente é instalado na VM, mas não responde (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [O agente instalado na VM está desatualizado (para VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [O status da extensão do Site Recovery está incorreto](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Falha na proteção porque o agente de VM está sem resposta

Mensagem de erro: "o tempo limite de execução da tarefa foi atingido ao passo que o acompanhamento da operação da extensão iniciou”.<br>
Código de erro: "151099"

Esse erro pode acontecer se o agente convidado do Azure na máquina virtual não estiver no estado pronto.

Você pode verificar o status do agente convidado do Azure no [portal Azure](https://portal.azure.com/). Vá para a máquina virtual que você está tentando proteger e verifique o status no status do agente**de propriedades de configurações** > **Properties** > **da** **VM** > . Na maioria das vezes, o status do agente está pronto após a reinicialização da máquina virtual. No entanto, se você não puder reiniciar ou ainda estiver enfrentando o problema, complete as seguintes etapas de solução de problemas:

- [O agente é instalado na VM, mas não responde (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [O agente instalado na VM está desatualizado (para VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


Mensagem de erro: "o tempo limite de execução da tarefa foi atingido ao passo que o acompanhamento da operação da extensão iniciou”.<br>
Código de erro: "151095"

Esse erro ocorre quando a versão do agente na máquina Linux está desatualizada. Complete a seguinte etapa de solução de problemas:

- [O agente instalado na VM está desatualizado (para VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>O agente é instalado na VM, mas não responde (para VMs do Windows)

#### <a name="solution"></a>Solução
O agente de VM pode ter sido corrompido ou o serviço pode ter sido interrompido. A reinstalação do agente de VM permite obter a versão mais recente. Ela também ajuda a reiniciar a comunicação com o serviço.

1. Determine se o serviço de agente convidado do Microsoft Azure está executando nos serviços de VM (services.msc). Reinicie o serviço de agente convidado do Windows Azure.    
1. Se o serviço do Agente Convidado do Windows Azure não estiver visível nos serviços, abra o Painel de Controle. Vá para **Programas e Recursos** para ver se o serviço do Agente Convidado do Windows está instalado.
1. Se o serviço de agente convidado do Microsoft Azures aparecer em **Programas e Recursos**, desinstale o serviço de agente convidado do Microsoft Azure.
1. Baixe e instale a [versão mais recente do MSI do agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisa dos direitos de administrador para concluir a instalação.
1. Verifique se o serviço de agente convidado do Windows Azure aparece nos serviços.
1. Reinicie o trabalho de proteção.

Verifique também se o [Microsoft .NET 4.5 está instalado](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) na VM. Você precisa de .NET 4.5 para que o agente VM se comunique com o serviço.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>O agente instalado na VM está desatualizado (para VMs Linux)

#### <a name="solution"></a>Solução
A maioria das falhas relacionadas ao agente ou relacionadas à extensão para VMs do Linux é causada por problemas que afetam um agente de VM desatualizado. Para solucionar esse problema, siga estas diretrizes gerais:

1. Siga as instruções para [atualizar o agente de VM do Linux ](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > Estamos *altamente recomendável* que você atualizar o agente apenas por meio de um repositório de distribuição. Não recomendamos baixar o código do agente diretamente do GitHub e atualizá-lo. Se o agente mais recente para sua distribuição não estiver disponível, entre em contato com o suporte de distribuição para obter instruções sobre como instalá-lo. Para verificar o agente mais recente, vá para a página [agente Linux do Windows Azure](https://github.com/Azure/WALinuxAgent/releases) no repositório do GitHub.

1. Verifique se o agente do Azure está em execução na VM, executando o seguinte comando: `ps -e`

   Se o processo não estiver em execução, reinicie-o usando os seguintes comandos:

   - Para o Ubuntu: `service walinuxagent start`
   - Para outras distribuições: `service waagent start`

1. [Configure o agente de reinicialização automático](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
1. Habilitar a proteção para as máquina virtuais.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>A extensão de Site Recovery falha ao ser atualizada ou carregada

O status de extensão é mostrado como "Vazio", "Não Pronto" ou "Transição".

#### <a name="solution"></a>Solução

Desinstale a extensão e reinicie a operação novamente.

Para desinstalar a extensão:

1. No [portal Azure,](https://portal.azure.com/)vá para a VM que está experimentando falha de backup.
1. Selecione **Configurações**.
1. Selecione **Extensões**.
1. Selecione **Extensão Site Recovery**.
1. Selecionar **Desinstalar**.

Para O Linux VM, se a extensão VMSnapshot não aparecer no portal Azure, [atualize o Azure Linux Agent](../virtual-machines/linux/update-agent.md). Então execute a proteção.

Quando você completa essas etapas, faz com que a extensão seja reinstalada durante a proteção.
