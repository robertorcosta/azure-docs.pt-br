---
title: Fazer failback de VMs VMware/servidores físicos do Azure com Azure Site Recovery
description: Saiba como fazer o failback para o site local após o failover para o Azure durante a recuperação de desastre de VMs e servidores físicos VMware para o Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 2ec2a4a91f4de0761f631bec393bb90c3feb82b9
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084050"
---
# <a name="fail-back-vmware-vms-and-physical-servers-from-azure-to-an-on-premises-site"></a>Executar failback de VMs e servidores físicos VMware do Azure para um site local

Este artigo descreve como realizar o failback de máquinas virtuais das Máquinas Virtuais do Azure para um ambiente VMware local. Siga as instruções neste artigo para realizar o failback de suas máquinas virtuais VMware ou de servidores físicos Windows/Linux após a realização do failover do site local para o Azure usando o tutorial [Failover no Azure Site Recovery](site-recovery-failover.md).

## <a name="prerequisites"></a>pré-requisitos
- Certifique-se de que você tenha lido os detalhes sobre os [tipos diferentes de failback](concepts-types-of-failback.md) e as perdas correspondentes.

> [!WARNING]
> Não é possível realizar o failback após [concluir a migração](migrate-overview.md#what-do-we-mean-by-migration), mover a máquina virtual para outro grupo de recursos ou excluir a máquina virtual do Azure. Se você desabilitar a proteção da máquina virtual, não é possível realizar o failback.

> [!WARNING]
> Um servidor físico com Windows Server 2008 R2 SP1, se protegido e com failover no Azure, não pode passar por failback.

> [!NOTE]
> Se você fizer failover em máquinas virtuais VMware, não poderá fazer failback para um host Hyper-V.


- Antes de continuar, conclua as etapas de Nova Proteção para que as máquinas virtuais estejam em estado replicado e você possa iniciar um failover em um local. Para saber mais, veja [Como proteger mais do Azure para locais](vmware-azure-reprotect.md).

- Verifique se o vCenter está em um estado conectado antes de fazer um failback. Caso contrário, desconectando discos e anexá-los de volta para a máquina virtual falha.

- Durante o failover para o Azure, o site local pode não estar acessível e o servidor de configuração pode não estar disponível ou estar desligado. Durante a nova proteção e o failback, o servidor de configuração local deve estar em execução e em um estado conectado OK. 

- Durante o failback, a máquina virtual deve existir no banco de dados do servidor de configuração ou o failback não obterá êxito. Faça o backup agendado regular de seu servidor. Em caso de um desastre, você precisa restaurar o servidor com o endereço IP original para que o failback funcione.

- O servidor de destino mestre não deve ter instantâneos antes de acionar o failback ou a nova proteção.

## <a name="overview-of-failback"></a>Visão geral do failback
Após a realização do failover para o Azure, será possível executar o failback para seu site local executando as seguintes etapas:

1. [Proteja novamente](vmware-azure-reprotect.md) as máquinas virtuais no Azure para que iniciem a replicação para máquinas virtuais VMware seu site local. Como parte desse processo, você também precisa:

    * Configurar um servidor de destino mestre local Use um destino mestre do Windows para máquinas virtuais Windows e um [destino mestre Linux](vmware-azure-install-linux-master-target.md) para máquinas virtuais Linux.
    * Configure um [servidor de processo](vmware-azure-set-up-process-server-azure.md).
    * Inicie [Proteger novamente](vmware-azure-reprotect.md) para desligar a máquina virtual no local e sincronizar os dados da máquina virtual do Azure com os discos no local.

2. Depois que as máquinas virtuais do Azure são replicadas no site local, você inicia um failover do Azure para o site local.

3. Após realizar o failback dos dados, você protege novamente as máquinas virtuais locais para as quais realizou o failback para que comecem a ser replicadas no Azure.

Para obter uma visão geral, assista a este vídeo sobre como fazer failover para um site local:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Etapas para failback

> [!IMPORTANT]
> Antes de iniciar o failback, certifique-se de que você terminou a nova proteção das máquinas virtuais. As máquinas virtuais devem estar em um estado protegido e sua integridade deve ser **OK**. Para proteger novamente as máquinas virtuais, leia [Como proteger novamente](vmware-azure-reprotect.md).

1. Na página itens duplicados, selecione a máquina virtual. Clique com botão direito para selecionar **Failover não planejado**.
2. Em **Confirmar Failover**, verifique a direção do failover (do Azure). Em seguida, selecione o ponto de recuperação (o mais recente, ou um que seja consistente com o aplicativo mais recente) que você deseja usar para o failover. O ponto consistente do aplicativo está atrás do último ponto no tempo e causa a perda de alguns dados.
3. Durante o failover, o Site Recovery desliga as máquinas virtuais no Azure. Depois de verificar se o failback foi concluído conforme o esperado, você pode verificar se as máquinas virtuais do Azure foram desligadas.
4. A **confirmação** é necessária para remover a máquina virtual com failover do Azure. Clique com o botão direito no item protegido e, em seguida, selecione **Confirmar**. Um trabalho remove as máquinas virtuais de failover no Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Para o ponto de recuperação pode não consigo novamente as máquinas virtuais?

Durante o failback, você tem duas opções para failback o plano de recuperação/máquina virtual.

- Se você selecionar o ponto no tempo processado mais recentemente, todas as máquinas virtuais são submetidas a failover para o ponto no tempo mais recente disponível no momento. Caso haja um grupo de replicação no plano de recuperação, cada máquina virtual do grupo de replicação é submetida a failover para seu ponto no tempo independente mais recente.

  Você não pode efetuar novamente uma máquina virtual até que tenha pelo menos um ponto de recuperação. Não é possível failback um plano de recuperação até que todas as suas máquinas virtuais tenham pelo menos um ponto de recuperação.

  > [!NOTE]
  > Um ponto de recuperação mais recente é um ponto de recuperação consistente com falha.

- Se você selecionar o ponto de recuperação consistente de aplicativo, um único failback de máquina virtual será recuperado para o ponto de recuperação consistente de aplicativo disponível mais recente. No caso de um plano de recuperação com um grupo de replicação, cada grupo de replicação é recuperado para o ponto de recuperação disponível comum.
Os pontos de recuperação consistentes de aplicativo podem ser anteriores no tempo, e pode haver perda de dados.

## <a name="what-happens-to-vmware-tools-post-failback"></a>O que acontece com o failback de postagem de ferramentas VMware?

No caso de uma máquina virtual do Windows, o Site Recovery desabilita as ferramentas VMware durante o failover. Durante o failback da máquina virtual do Windows, as ferramentas do VMware são habilitadas novamente. 


## <a name="reprotect-from-on-premises-to-azure"></a>Proteger novamente do local para o Azure
Depois que o failback terminar e você tiver iniciado a confirmação, as máquinas virtuais recuperadas no Azure serão excluídas. Agora, a máquina virtual está novamente no site local, mas ela não estará protegida. Para iniciar a replicação no Azure novamente, faça o seguinte:

1. Em **Vault** > **Configuração** > **Itens replicados**, selecione as máquinas virtuais na quais foi executado o failback e selecione **Proteger novamente**.
2. Insira o valor do servidor de processo que precisa ser usado para enviar dados de volta ao Azure.
3. Selecione **OK** para iniciar o trabalho de proteger novamente.

> [!NOTE]
> Depois que uma máquina virtual local é inicializada, levará algum tempo para que o agente seja registrado no servidor de configuração (no máximo 15 minutos). Durante esse tempo, proteger novamente falhará e retornará uma mensagem de erro informando que o agente não está instalado. Aguarde alguns minutos e tente proteger novamente.

## <a name="next-steps"></a>Próximas etapas

Depois que o trabalho de proteger novamente for concluído, a máquina virtual é replicada novamente para o Azure e você poderá fazer um [failover](site-recovery-failover.md) a fim de mover as máquinas virtuais do Azure.


