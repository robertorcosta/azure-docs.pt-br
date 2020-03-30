---
title: Configurar a recuperação de desastres após a migração para o Azure com a recuperação do site do Azure
description: Este artigo descreve como preparar computadores para configurar a recuperação de desastre entre regiões do Azure após a migração para o Azure usando o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 874c282ff878126297dc46ca0e7a4c19910e40a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74159117"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Configurar a recuperação de desastres para VMs do Azure após a migração para o Azure 


Siga este artigo se você [migrou máquinas locais para As VMs do Azure](tutorial-migrate-on-premises-to-azure.md) usando o serviço [de recuperação de sites](site-recovery-overview.md) e agora deseja configurar as VMs para recuperação de desastres para uma região secundária do Azure. O artigo descreve como garantir que o agente Azure VM seja instalado em VMs migradas e como remover o serviço de mobilidade de recuperação do site que não é mais necessário após a migração.



## <a name="verify-migration"></a>Verificar migração

Antes de configurar a recuperação de desastres, certifique-se de que a migração foi concluída conforme o esperado. Para concluir uma migração com êxito, após o failover, você deve selecionar a opção **Concluir a migração** para cada computador que você deseja migrar. 

## <a name="verify-the-azure-vm-agent"></a>Verifique o agente Azure VM

Cada Azure VM deve ter o [agente Azure VM](../virtual-machines/extensions/agent-windows.md) instalado. Para replicar As VMs do Azure, a Recuperação do Site instala uma extensão no agente.

- Se a máquina estiver executando a versão 9.7.0.0 ou posterior do serviço de mobilidade de recuperação do site, o agente Azure VM será instalado automaticamente pelo serviço Mobility em VMs Windows. Nas versões anteriores do serviço Mobility, você instalará o agente manualmente.
- Para VMs Linux, você deve instalar o agente Azure VM manualmente. Você só precisa instalar o agente da VM do Azure se o serviço de Mobilidade instalado no computador migrado for versão 9.6 ou anterior.


### <a name="install-the-agent-on-windows-vms"></a>Instale o agente em VMs do Windows

Se você estiver executando uma versão do serviço de mobilidade de recuperação de site antes do 9.7.0.0, ou tiver alguma outra necessidade de instalar o agente manualmente, faça o seguinte:  

1. Certifique-se de ter permissões de admin na VM.
2. Baixe o [instalador do Agente VM](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Execute o arquivo do instalador.

#### <a name="validate-the-installation"></a>validar a instalação
Para verificar se o agente está instalado:

1. Na VM do Azure, na pasta C:\WindowsAzure\Packages, você deve ver o arquivo WaAppAgent.exe.
2. Clique com o botão direito do mouse no arquivo e em **Propriedades**, selecione a guia **Detalhes**.
3. Verifique se o campo **Versão do Produto** mostra 2.6.1198.718 ou mais recente.

[Saiba mais](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) sobre a instalação do agente para Windows.

### <a name="install-the-agent-on-linux-vms"></a>Instale o agente em VMs Linux

Instale manualmente o agente [Azure Linux VM:](../virtual-machines/extensions/agent-linux.md)

1. Certifique-se de ter permissões de admin na máquina.
2. Recomendamos fortemente que você instale o agente Linux VM usando um RPM ou um pacote DEB do repositório de pacotes da sua distribuição. Todos os [provedores de distribuição aprovados](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integram o pacote do agente Linux do Azure em suas imagens e repositórios.
    - Estamos altamente recomendável que você atualizar o agente apenas por meio de um repositório de distribuição.
    - Não recomendamos instalar o agente Linux VM diretamente do GitHub e atualizá-lo.
    -  Se o agente mais recente para a sua distribuição não está disponível, entre em contato com o suporte da distribuição para obter instruções de como instalá-lo. 

#### <a name="validate-the-installation"></a>validar a instalação 

1. Execute este comando: **ps -e** para garantir que o agente Azure esteja sendo executado na VM Linux.
2. Se o processo não estiver em execução, reinicie-o usando os seguintes comandos:
    - Para o Ubuntu: **início do walinuxagent de serviço**
    - Para outras distribuições: **início do waagent de serviço**


## <a name="uninstall-the-mobility-service"></a>Desinstalar o serviço de Mobilidade

1. Desinstale manualmente o serviço de mobilidade da VM Azure, usando um dos seguintes métodos. 
    - Para Windows, no Painel de controle > **Adicionar ou remover programas**, desinstale **Serviço de Mobilidade do Microsoft Azure Site Recovery/Servidor de destino mestre**. Em um prompt de comandos com privilégios elevados, execute:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Para o Linux, entre como o usuário raiz. Em um terminal, vá para **/user/local/ASR** e execute o seguinte comando:
        ```
        ./uninstall.sh -Y
        ```
2. Reinicie a máquina virtual antes de configurar a replicação.

## <a name="next-steps"></a>Próximas etapas

[Revise a solução de problemas](site-recovery-extension-troubleshoot.md) para a extensão de recuperação de site no agente Azure VM.
[Replicar rapidamente](azure-to-azure-quickstart.md) uma VM do Azure para uma região secundária.
