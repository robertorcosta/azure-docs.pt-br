---
title: Executar uma simulação de recuperação de desastre de uma VM do Azure com o Azure Site Recovery
description: Saiba como executar uma simulação de recuperação de desastre para uma região secundária para VMs do Azure usando o serviço Azure Site Recovery.
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166186"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Executar uma simulação de recuperação de desastre para uma região secundária para VMs do Azure

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) por manter seus aplicativos de negócios em execução e disponíveis durante interrupções planejadas e não planejadas. O Site Recovery gerencia e orquestra a recuperação de desastre de máquinas locais e de VMs (máquinas virtuais) do Azure, incluindo replicação, failover e recuperação.

Este tutorial mostra a você como realizar uma simulação de recuperação de desastre para uma VM do Azure, de uma região para outra, com um failover de teste. Uma simulação valida sua estratégia de replicação sem perda de dados ou tempo de inatividade e não afeta seu ambiente de produção. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verificar os pré-requisitos
> * Executar um failover de teste para uma VM

> [!NOTE]
> Este tutorial ajuda você a executar uma simulação de recuperação de desastre com etapas mínimas. Para saber mais sobre as várias funções relacionadas à realização de uma simulação de recuperação de desastre, confira a documentação de [replicação](azure-to-azure-how-to-enable-replication.md), [rede](azure-to-azure-about-networking.md), [automação](azure-to-azure-powershell.md) ou [solução de problemas](azure-to-azure-troubleshoot-errors.md) das VMs do Azure.

## <a name="prerequisites"></a>Prerequisites

Verifique os seguintes itens antes de iniciar este tutorial:

- Antes de executar um failover de teste, recomendamos que você verifique as propriedades da VM para garantir que ela esteja configurada para a recuperação de desastre. Acesse **Operações** > **Recuperação de Desastre** > **Propriedades** da VM para ver as propriedades de replicação e failover.
- **É recomendável utilizar uma rede de VM do Azure separada para o failover de teste** e não a rede padrão que foi configurada quando você habilitou a replicação.
- Dependendo das configurações de rede de origem para cada NIC, você pode especificar **Sub-rede**, **Endereço IP privado**, **IP público**, **Grupo de segurança de rede** ou **Balanceador de carga** para anexação a cada NIC nas configurações de failover de teste em **Computação e Rede** antes de executar a simulação de recuperação de desastre.

## <a name="run-a-test-failover"></a>Execute um teste de failover

Este exemplo mostra como usar um cofre dos Serviços de Recuperação para fazer um failover de teste de uma VM.

1. Selecione um cofre e acesse **Itens protegidos** > **Itens replicados** e selecione uma VM.
1. Em **Failover de Teste**, selecione um ponto de uso para o failover:
   - **Mais recente**: processa todos os dados no Site Recovery e fornece o menor RTO (Objetivo de tempo de recuperação).
   - **Mais recente processado**: efetua o failover da VM para o último ponto de recuperação processado pelo Site Recovery. A carimbo de data/hora é mostrado. Com esta opção, não há tempo gasto no processamento de dados e, portanto, ele fornece um baixo RTO.
   - **Consistente com o aplicativo mais recente**: Essa opção falha em todas as VMs para o ponto de recuperação consistente com o aplicativo mais recente. A carimbo de data/hora é mostrado.
   - **Personalizado**: efetua o failover para um determinado ponto de recuperação. A configuração personalizada só está disponível quando você efetua o failover de uma única VM, não para failovers com um plano de recuperação.
1. Selecione a rede virtual de destino do Azure à qual as VMs do Azure na região secundária serão conectadas, após o failover.

   > [!NOTE]
   > Se as configurações de failover de teste estiverem pré-configuradas para o item replicado, o menu suspenso para seleção de uma rede virtual do Azure não estará visível.

1. Para iniciar o failover, selecione **OK**. Para acompanhar o progresso do cofre, acesse **Monitoramento** > **Trabalhos do Site Recovery** e selecione o trabalho **Failover de Teste**.
1. Após a conclusão do failover, a VM de réplica do Azure será exibida nas **Máquinas Virtuais** do portal do Azure. Verifique se a VM está em execução, tem o tamanho apropriado e está conectada à rede apropriada.
1. Para excluir as VMs que foram criadas durante o failover de teste, selecione **Failover de teste de limpeza** no item replicado ou no plano de recuperação. Em **Observações**, registre e salve todas as observações associadas ao failover de teste.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Executar um failover de produção](azure-to-azure-tutorial-failover-failback.md)
