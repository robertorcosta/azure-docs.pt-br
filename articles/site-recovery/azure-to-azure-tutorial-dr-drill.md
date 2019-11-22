---
title: Executar uma simulação de recuperação de desastre de uma VM do Azure com o Azure Site Recovery
description: Saiba como executar uma simulação de recuperação de desastre para uma região secundária para VMs do Azure usando o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 817a220e36ac250b1d5a5aa90d0bddbfb155cc26
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091327"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Executar uma simulação de recuperação de desastre para uma região secundária para VMs do Azure 

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) por manter seus aplicativos de negócios em execução e disponíveis durante interrupções planejadas e não planejadas. O Site Recovery gerencia e orquestra a recuperação de desastre de máquinas locais e de VMs (máquinas virtuais) do Azure, incluindo replicação, failover e recuperação.

Este tutorial mostra a você como realizar uma simulação de recuperação de desastre para uma VM do Azure, de uma região para outra, com um failover de teste. Uma simulação valida sua estratégia de replicação sem perda de dados ou tempo de inatividade e não afeta seu ambiente de produção. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verificar os pré-requisitos
> * Executar um failover de teste para uma VM

> [!NOTE]
> Este tutorial ajuda você a executar uma simulação de recuperação e desastre com o mínimo de etapas. Caso queira saber mais sobre os vários aspectos associados à execução de uma simulação de recuperação de desastre, incluindo considerações de rede, automação ou solução de problemas, consulte os documentos em “Instruções” para VMs do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de executar um failover de teste, é recomendável verificar as propriedades da VM para certificar-se de que tudo está conforme o esperado.  Acesse as propriedades da VM em **Itens replicados**. A folha **Conceitos básicos** mostra as informações sobre as configurações e o status dos computadores.
- **É recomendável utilizar uma rede de VM do Azure separada para o failover de teste** e não a rede padrão que foi configurada quando você habilitou a replicação.
- Dependendo de suas configurações de rede de origem para cada NIC, você pode especificar **a sub-rede, o endereço IP, o IP público, o Grupo de Segurança de Rede ou o Load Balancer Interno** para anexar a cada NIC nas configurações de failover de teste em Computação e Rede antes de executar a simulação de DR.


## <a name="run-a-test-failover"></a>Execute um teste de failover

1. Em **Configurações** > **Itens Replicados**, clique no ícone da VM **+ Failover de Teste**.

2. Em **Failover de Teste**, selecione um ponto de recuperação para usar no failover:

    - **Mais recente**: processa todos os dados no Site Recovery e fornece o menor RTO (Objetivo de tempo de recuperação).
    - **Mais recente processado**: efetua o failover da VM para o último ponto de recuperação processado pelo Site Recovery. A carimbo de data/hora é mostrado. Com essa opção, nenhum tempo é gasto em processamento de dados, portanto, ela fornece um RTO (Objetivo do Tempo de Recuperação) baixo
   - **Consistente com o aplicativo mais recente**: Essa opção falha em todas as VMs para o ponto de recuperação consistente com o aplicativo mais recente. A carimbo de data/hora é mostrado.
   - **Personalizado**: efetua o failover para um determinado ponto de recuperação. A configuração personalizada só está disponível quando você efetua o failover de uma única VM, não para failovers com um plano de recuperação.

3. Selecione a rede virtual de destino do Azure para qual as VMs do Azure na região secundária serão conectadas, após o failover ocorrer.

    > [!NOTE]
    > A lista suspensa para selecionar a rede virtual do Azure não estará visível se as configurações de failover de teste estiverem pré-configuradas para o item replicado.

4. Para iniciar o failover, clique em **Ok**. Para acompanhar o progresso, clique na VM para abrir suas propriedades. Ou você pode clicar no trabalho **Failover de Teste** no nome do cofre > **Configurações** > **Trabalhos** > **Trabalhos do Site Recovery**.
5. Após a conclusão do failover, a réplica da VM do Azure aparece no portal do Azure > **Máquinas Virtuais**. Verifique se a VM está em execução, tem o tamanho apropriado e está conectada à rede apropriada.
6. Para excluir as VMs que foram criadas durante o failover de teste, clique em **Failover de teste de limpeza** no item replicado ou no plano de recuperação. Em **Observações**, registre e salve todas as observações associadas ao failover de teste.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Executar um failover de produção](azure-to-azure-tutorial-failover-failback.md)
