---
title: Gerencie o agente de mobilidade para servidores vmware/físicos com recuperação do site do Azure
description: Gerenciar o agente do Serviço de Mobilidade para recuperação de desastres de VMware VMs e servidores físicos para o Azure usando o serviço de recuperação do site do Azure.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 9be758c286e072b0fbefc5f8b20b7accc4e6741b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256959"
---
# <a name="manage-the-mobility-agent"></a>Gerenciar o agente Mobilidade 

Você configura o agente de mobilidade em seu servidor quando usa o Azure Site Recovery para recuperação de desastres de VMware VMs e servidores físicos para o Azure. O agente de mobilidade coordena as comunicações entre sua máquina protegida, servidor de configuração/servidor de processo de escala e gerencia a replicação de dados. Este artigo resume tarefas comuns para gerenciar o agente de mobilidade depois de implantado.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Atualizar o serviço de mobilidade do portal Azure

1. Antes de iniciar, certifique-se de que o servidor de configuração, os servidores de processo de expansão e quaisquer servidores de destino que não façam parte da sua implantação sejam atualizados antes de você atualizar o Serviço de Mobilidade nos computadores protegidos.
2. No portal, abra o cofre > **Itens replicados**.
3. Se o servidor de configuração estiver na versão mais recente, você verá uma notificação que mostra "Nova atualização do agente de replicação de recuperação do site está disponível. Clique para instalar."

     ![Janela Itens replicados](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Clique na notificação e, em **Atualização do agente**, selecione os computadores nos quais você deseja atualizar o serviço de mobilidade. Em seguida, clique em **OK**.

     ![Lista de VMs de itens replicados](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. O trabalho Atualizar o Serviço de Mobilidade é iniciado para cada um dos computadores selecionados.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Atualizar o serviço de mobilidade através do script powershell no servidor Windows

Antes de iniciar, certifique-se de que o servidor de configuração, os servidores de processo de expansão e quaisquer servidores de destino que não façam parte da sua implantação sejam atualizados antes de você atualizar o Serviço de Mobilidade nos computadores protegidos.

Use o script a seguir para atualizar o serviço de mobilidade em um servidor através do power shell cmdlet

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>Atualize o serviço de mobilidade manualmente em cada servidor protegido

1. Antes de iniciar, certifique-se de que o servidor de configuração, os servidores de processo de expansão e quaisquer servidores de destino que não façam parte da sua implantação sejam atualizados antes de você atualizar o Serviço de Mobilidade nos computadores protegidos.

2. [Localize o instalador do agente](vmware-physical-mobility-service-overview.md#locate-installer-files) com base no sistema operacional do servidor.

>[!IMPORTANT]
> Se você estiver replicando o Azure IaaS VM de uma região azure para outra, não use este método. Consulte [nossa orientação](azure-to-azure-autoupdate.md) para obter informações sobre todas as opções disponíveis.

3. Copie o arquivo de instalação na máquina protegida e execute-o para atualizar o agente de mobilidade.

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Conta de atualização usada para instalação push do serviço de mobilidade

Quando você implantou o Site Recovery para habilitar a instalação por push do serviço de mobilidade, especificou uma conta que o servidor de processo do Site Recovery usa para acessar os computadores e instalar o serviço quando a replicação é habilitada para o computador. Se você quiser atualizar as credenciais dessa conta, siga [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Desinstalar o serviço de mobilidade

### <a name="on-a-windows-machine"></a>Em uma máquina Windows

Desinstale pela interface do usuário ou por um prompt de comando.

- **Pela interface de usuário**: no Painel de Controle do computador, selecione **Programas**. Selecione **o Serviço de Mobilidade de Recuperação do Site do Microsoft Azure/Servidor** > alvo mestre**Desinstalar**.
- **Por um prompt de comando**: Abra uma janela de prompt de comando como administrador no computador local. Execute o comando a seguir: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Em um computador Linux
1. No computador Linux, entre como um usuário **raiz**.
2. Em um terminal, vá para /usr/local/ASR.
3. Execute o comando a seguir:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Instale o provedor VSS de recuperação de site na máquina de origem

O provedor Azure Site Recovery VSS é necessário na máquina de origem para gerar pontos de consistência do aplicativo. Se a instalação do provedor não foi bem sucedida através da instalação push, siga as diretrizes abaixo para instalá-lo manualmente.

1. Abrir a janela cmd do admin.
2. Navegue até o local de instalação do serviço de mobilidade. (Por exemplo - C:\Arquivos de programa (x86)\Microsoft Azure Site Recovery\agent)
3. Execute o script InMageVSSProvider_Uninstall.cmd . Isso desinstalará o serviço se ele já existir.
4. Execute o script InMageVSSProvider_Install.cmd para instalar manualmente o provedor VSS.

## <a name="next-steps"></a>Próximas etapas

- [Configure a recuperação de desastres para VMs VMware](vmware-azure-tutorial.md)
- [Configure a recuperação de desastre para servidores físicos](physical-azure-disaster-recovery.md)
