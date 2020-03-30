---
title: Reproteja as VMMs do VMware para um site local com a recuperação do site do Azure
description: Saiba como reproteger as VMS vMware após failover para o Azure com a Recuperação do Site do Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 976888f57269cc9fe6107a38e30d78c73eb5c124
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257167"
---
# <a name="reprotect-from-azure-to-on-premises"></a>Proteja Novamente do Azure para o local

Depois de [failover](site-recovery-failover.md) de máquinas virtuais do VMware no local ou servidores físicos para o Azure, a primeira etapa com falha para seu site local é Proteja as VMs do Azure que foram criados durante o failover. Este artigo descreve como fazer isso. 

## <a name="before-you-begin"></a>Antes de começar

1. Siga as etapas [deste artigo](vmware-azure-prepare-failback.md) para se preparar para reproteção e failback, incluindo a configuração de um servidor de processo no Azure e um servidor de destino mestre no local e a configuração de uma VPN site-to-site, ou peering privado ExpressRoute, para failback.
2. Certifique-se de que o servidor de configuração local está sendo executado e conectado ao Azure. Durante o failover para o Azure, o site no local pode não estar acessível e o servidor de configuração pode estar indisponível ou desligado. Durante o failback, a VM deve existir no banco de dados do servidor de configuração. Caso contrário, o failback será malsucedido.
3. Exclua quaisquer instantâneos no servidor de destino principal no local. A reproteção não funcionará se houver instantâneos.  Os instantâneos na VM são automaticamente mesclados durante um trabalho de reproteção.
4. Se você estiver reprotegendo as VMs reunidas em um grupo de replicação para obter consistência de várias VM, certifique-se de que todas elas tenham o mesmo sistema operacional (Windows ou Linux) e certifique-se de que o servidor de destino mestre que você implanta tenha o mesmo tipo de sistema operacional. Todas as VMs em um grupo de replicação devem usar o mesmo servidor de destino mestre.
5. Abra [as portas necessárias](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) para failback.
6. Certifique-se de que o servidor vCenter está conectado antes da refalha. Caso contrário, desconectando discos e anexá-los de volta para a máquina virtual falha.
7. Se um servidor vCenter gerenciar as VMs para as quais você falhará, certifique-se de que você tem as permissões necessárias. Se você executar a descoberta de vCenter de Usuário somente leitura e proteger as máquinas virtuais, a proteção terá êxito e o failover funcionará. No entanto, durante a reproteção, o failover falha porque os datastores não podem ser descobertos e não estão listados durante a reproteção. Para resolver esse problema, você pode atualizar as credenciais do vCenter com uma [conta/permissões apropriadas](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)e, em seguida, tentar novamente o trabalho. 
8. Se você usou um modelo para criar suas máquinas virtuais, certifique-se de que cada VM tenha seu próprio UUID para os discos. Se o UIID VM no local se choca com o UUID do servidor de destino principal porque ambos foram criados a partir do mesmo modelo, a reproteção falhará. Implantar a partir de um modelo diferente.
9. Se você estiver falhando em um servidor vCenter alternativo, certifique-se de que o novo servidor de destino vCenter e o servidor de destino mestre sejam descobertos. Normalmente, se eles não são os datastores não são acessíveis ou não são visíveis no **Reprotect**.
10. Verifique os seguintes cenários em que você não pode falhar:
    - Se você estiver usando a edição gratuita ESXi 5.5 ou a edição livre do vSphere 6 Hypervisor. Atualize para uma versão diferente.
    - Se você tiver um servidor físico Windows Server 2008 R2 SP1.
    - VMware VMs não podem falhar de volta ao Hyper-V.
    - VMs que [foram migradas](migrate-overview.md#what-do-we-mean-by-migration).
    - Uma VM que foi transferida para outro grupo de recursos.
    - Uma réplica da Azure VM que foi excluída.
    - Uma réplica da Azure VM que não está protegida (replicando-se no local).
10. [Revise os tipos de failback que](concepts-types-of-failback.md) você pode usar - recuperação de localização original e recuperação de localização alternativa.


## <a name="enable-reprotection"></a>Habilitar nova proteção

Habilite a replicação. Você pode reproteger VMs específicos ou um plano de recuperação:

- Se você reproteger um plano de recuperação, você deve fornecer os valores para cada máquina protegida.
- Se as VMs pertencerem a um grupo de replicação para consistência multi-VM, elas só poderão ser reprotegidas usando um plano de recuperação. As VMs em um grupo de replicação devem usar o mesmo servidor de destino mestre

### <a name="before-you-start"></a>Antes de começar

- Depois que uma VM é inicializado no Azure após failover, leva algum tempo para o agente se registrar de volta no servidor de configuração (até 15 minutos). Durante esse tempo, não será capaz de proteger novamente e uma mensagem de erro indica que o agente não está instalado. Se isso acontecer, espere alguns minutos, e depois reproteja.
- Se você quiser refazer a VM do Azure para uma VM existente no local, monte os datastores VM no local com acesso à leitura/gravação no host ESXi do servidor de destino mestre.
- Se você quiser falhar de volta a um local alternativo, por exemplo, se a VM no local não existir, selecione a unidade de retenção e o armazenamento de dados configurados para o servidor de destino mestre. Quando você realiza o failback para o site local, as máquinas virtuais do VMware no plano de proteção de failback usam o mesmo repositório de dados que o servidor de destino mestre. Uma nova VM é criada no vCenter.

Habilite a reproteção da seguinte forma:

1. Selecione**itens replicados do** **cofre** > . A máquina virtual que falhou e, em seguida, selecione **proteger novamente**. Ou, os botões de comando, selecione a máquina e, em seguida, selecione **proteger novamente**.
2. Verifique o **do Azure para local** direção de proteção está selecionada.
3. Em **Master Target Server** e Process **Server**, selecione o servidor de destino mestre no local e o servidor de processo.  
4. Para **Repositório de Dados**, selecione o repositório de dados no qual você deseja recuperar os discos localmente. Essa opção é usada quando a máquina virtual no local é excluída e você precisa criar novos discos. Essa opção será ignorada se os discos já existem. Você ainda precisa especificar um valor.
5. Selecione a unidade de retenção.
6. A politica de failback é selecionada automaticamente.
7. Selecione **Okey** para iniciar a nova proteção.

    ![Proteja a caixa de diálogo](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Um trabalho começa a replicar a VM do Azure no local. Você pode acompanhar o andamento na guia **Trabalhos**.
    - Quando a reproteção é bem sucedida, a VM entra em um estado protegido.
    - A VM local é desativada durante o processo de nova proteção. Isso ajuda a garantir a consistência de dados durante a replicação.
    - Não ligue a VM no local após o término da reproteção.
   

## <a name="next-steps"></a>Próximas etapas

- Se você encontrar algum problema, revise o [artigo de solução de problemas](vmware-azure-troubleshoot-failback-reprotect.md).
- Depois que as VMs do Azure forem protegidas, você pode [executar um failback](vmware-azure-failback.md). Failback desliga a VM do Azure e inicializa a VM no local. Espere algum tempo de inatividade para o aplicativo e escolha um tempo de failback em conformidade.


