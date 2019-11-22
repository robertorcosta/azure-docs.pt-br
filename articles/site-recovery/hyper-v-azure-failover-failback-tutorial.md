---
title: Configurar failover e failback para VMs do Hyper-V no Azure Site Recovery
description: Saiba como fazer failover e failback de VMs do Hyper-V durante a recuperação de desastre no Azure usando o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: a8c197c2f0875bb31d091fb5839730ee1568b471
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082641"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Executar failover e failback em VMs do Hyper-V replicadas no Azure

Este tutorial descreve como executar failover de uma VM do Hyper-V no Azure. Depois de executar failover, execute failback para o site local quando ele estiver disponível. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verificar as propriedades da VM Hyper-V para saber se elas estão em conformidade com os requisitos do Azure
> * Executar um failover para o Azure
> * Failback do Azure para o local
> * Faça replicação inversa de VMs locais para iniciar a replicação para o Azure novamente

Este tutorial é o quinto tutorial de uma série. Presume-se que você já tenha concluído as tarefas nos tutoriais anteriores.    

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar Hyper-V local](tutorial-prepare-on-premises-hyper-v.md)
3. Configurar a recuperação de desastres para as [VMs do Hyper-V](tutorial-hyper-v-to-azure.md) ou para [VMs do Hyper-V gerenciadas em nuvens do System Center VMM](tutorial-hyper-v-vmm-to-azure.md)
4. [Realizar uma simulação de recuperação de desastre](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Preparar-se para failover e failback

Certifique-se de que não haja instantâneos na VM e que a VM local esteja desativada durante o failback. Isso ajuda a garantir a consistência dos dados durante a replicação. Não ative a VM local durante o failback. 

O failover e o failback têm três estágios:

1. **Failover para o Azure**: Fazer failover de VMs do Hyper-V do site local para Azure.
2. **Failback para local**: Fazer failover de VMs do Azure para o site local, quando disponível. Ele inicia a sincronização de dados do Azure para o local e, após a conclusão, ele abre as VMs no local.  
3. **Replicação inversa de VMs locais**: Após o failback para o local, replique inversamente as VMs locais para começar a replicá-las no Azure.

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Antes do failover, verifique as propriedades da VM e verifique se a VM atende aos [requisitos do Azure](hyper-v-azure-support-matrix.md#replicated-vms).

Em **Itens Protegidos**, clique em **Itens Replicados** > VM.

1. No painel **Item Replicado**, há um resumo das informações da VM, o status de integridade e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para exibir mais detalhes.

1. Em **Computação e Rede**, você pode modificar o nome do Azure, o grupo de recursos, o tamanho do destino, o [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) e as configurações de disco gerenciado.

1. Você pode exibir ou modificar as configurações de rede, incluindo a rede/sub-rede na qual a VM do Azure estará localizada após o failover e o endereço IP que será atribuído a ela.

1. Em **Discos**, é possível visualizar as informações sobre o sistema operacional e os discos de dados na VM.

## <a name="failover-to-azure"></a>Failover para o Azure

1. Em **Configurações** > **Itens replicados** clique em VM > **Failover**.
2. Em **Failover**, selecione o **Último** ponto de recuperação. 
3. Selecione **Desligar o computador antes do início do failover**. O Site Recovery tenta desligar as VMs de origem antes de acionar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
4. Depois de verificar o failover, clique em **Confirmar**. Exclui todos os pontos de recuperação disponíveis.

> [!WARNING]
> **Não cancele um failover em andamento**: Se você cancelar um failover em andamento, o failover será interrompido, mas a VM não será replicada novamente.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Fazer failback de VM do Azure para local e replicação inversa de VM local

A operação de failback é basicamente um failover do Azure para o site local e, na replicação inversa, ele inicia novamente a replicação de VMs do site local para o Azure.

1. Em **Configurações** > **Itens Replicados**, clique na VM > **Failover Planejado**.
2. Em **Confirmar Failover Planejado**, verifique a direção do failover (do Azure) e selecione os locais de origem e de destino.
3. Selecione **Sincronizar os dados antes do failover (sincronizar apenas alterações delta)** . Essa opção minimiza o tempo de inatividade da VM porque sincroniza sem desligar a VM.
4. Inicie o failover. Você pode acompanhar o progresso do failover na guia **Trabalhos** .
5. Depois que a sincronização de dados inicial for concluída e você estiver pronto para desligar as VMs no Azure, clique em **Trabalhos** > nome do trabalho de failover planejado > **Concluir Failover**. Ele desliga a VM do Azure, transfere as alterações mais recentes para o local e inicia a VM local.
6. Faça logon na VM local para verificar se ela está disponível conforme o esperado.
7. A VM local agora está em um estado de **Confirmação Pendente**. Clique em **Confirmar**. Ele exclui as VMs do Azure e seus discos e prepara a VM local para a replicação inversa.
Para iniciar a replicação da VM local para o Azure, habilite a **Replicação Inversa**. Ele dispara a replicação de alterações delta ocorridas desde que a VM do Azure foi desativada.  
