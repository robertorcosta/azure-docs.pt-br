---
title: Efetuar o failback de VMs do Azure para uma região primária com o serviço do Azure Site Recovery.
description: Descreve como efetuar o failback de VMs do Azure para uma região primária com o serviço do Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c27b7bf29e5f124fdcfb886b658fd8e9d4cc48fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74091354"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Failback de uma VM do Azure entre regiões do Azure

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de recuperação de desastre ao gerenciar e orquestrar a replicação, o failover e o failback de computadores locais e de VMs (máquinas virtuais) do Azure.

Este tutorial descreve como fazer failback de uma única VM do Azure. Depois de fazer failover, você deverá fazer failback para a região primária quando ela estiver disponível. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> 
> * Fazer failback da VM na região secundária.
> * Proteja novamente a VM primária de volta para a região secundária.
> 
> [!NOTE]
> 
> Este tutorial ajuda você a fazer failover de algumas VMs para uma região de destino de volta para a região de origem com as personalizações mínimas. Para obter mais instruções detalhadas, examine os [guias de instruções sobre VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/).

## <a name="before-you-start"></a>Antes de começar

* Certifique-se de que o status da VM seja **Failover confirmado**.
* Verifique se a região primária está disponível e, em seguida, você poderá criar e acessar os novos recursos nela.
* Certifique-se de que a nova proteção esteja habilitada.

## <a name="fail-back-to-the-primary-region"></a>Failback para a região primária

Depois que as VMs forem protegidas novamente, você poderá fazer failback para a região primária, conforme necessário.

1. No cofre, selecione **Itens replicados**, depois selecione a VM que foi protegida novamente.

    ![Failback para primária](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. Em **Itens replicados**, selecione a VM e, em seguida, selecione **Failover**.
3. Em **Failover**, selecione um ponto de recuperação para executar o failover:
    - **Mais recente (padrão)** : processa todos os dados no serviço Site Recovery e fornece o menor RPO (Objetivo de ponto de recuperação).
    - **Mais recente processado**: Reverte a VM para o último ponto de recuperação que foi processado pelo Site Recovery.
    - **Personalizado**: Faz failover para um determinado ponto de recuperação. Essa opção é útil para fazer um failover de teste.
4. Selecione **Desligar o computador antes do início do failover** se quiser que o Site Recovery tente realizar um desligamento das VMs da região de DR antes de disparar o failover. O failover continuará, mesmo se o desligamento falhar. 
5. Acompanhe o progresso do failover na página **Trabalhos**.
6. Após a conclusão do failover, valide a VM fazendo logon nela. É possível alterar o ponto de recuperação conforme necessário.
7. Depois de verificar o failover, selecione **Fazer commit do failover**. Confirmar exclui todos os pontos de recuperação disponíveis. A opção Alterar ponto de recuperação não está mais disponível.
8. A VM deve aparecer como o fazer failover realizado e failback realizado.

    ![VM nas regiões primária e secundária](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Para computadores que executam a extensão do Site Recovery versão 9.28.x.x em diante, o [Pacote cumulativo de atualizações 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) do Site Recovery limpa computadores na região de recuperação de desastre secundária, após o failback ser concluído e as VMs serem protegidas novamente. Não é necessário excluir manualmente as VMs e NICs na região secundária. Se você desabilitar completamente a replicação após o failback, o Site Recovery limpará os discos na região de recuperação de desastre, além das VMs e NICs.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) sobre o fluxo de nova proteção.
