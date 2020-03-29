---
title: Exibir a tendência de custo estimado mensal do laboratório no Azure DevTest Labs
description: Este artigo fornece informações sobre como rastrear o custo do seu laboratório (gráfico de tendência de custo estimado mensal) no Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2e4fe55fac5edf73e16df05bd38cc2712a94377d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721720"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Acompanhe os custos associados a um laboratório no Azure DevTest Labs
Este artigo fornece informações sobre como acompanhar o custo do seu laboratório. Ele mostra como ver o custo estimado para o mês atual do laboratório. O artigo também mostra como visualizar o custo mensal por recurso no laboratório.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Veja a tendência mensal estimada de custos de laboratório 
Nesta seção, você aprende a usar o gráfico **de tendência de custo estimado mensal** para visualizar o custo estimado do mês atual e o custo de fim de mês projetado para o mês calendário atual. Você também aprende a gerenciar os custos de laboratório definindo metas de gastos e limiares que, quando alcançados, acionam o DevTest Labs para relatar os resultados a você.

Para exibir o gráfico Tendência de custo estimado mensal, siga estas etapas: 

1. Faça login no [portal Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório.  
4. Selecione **Configuração e políticas** no menu esquerdo.  
4. Selecione **a tendência custo** na seção De rastreamento **de** custos no menu esquerdo. A captura de tela a seguir mostra um exemplo de um gráfico de custos. 
   
    ![Gráfico de custo](./media/devtest-lab-configure-cost-management/graph.png)

    O valor **Custo estimado** é o custo estimado até a data do mês do calendário atual. O **Custo projetado** é o custo estimado para todo o mês do calendário atual, calculado usando o custo do laboratório nos cinco dias anteriores.

    Observe que os valores do custo são arredondados para o próximo número inteiro. Por exemplo:  

   * 5.01 arredonda até 6 
   * 5.50 arredonda até 6
   * 5.99 arredonda até 6

     Como é indicado acima do gráfico, os custos vistos por padrão no gráfico são custos *estimados* usando as tarifas da oferta [Pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/). Você também pode definir duas metas de gastos que são exibidas nos gráficos [gerenciando as metas de custos de seu laboratório.](#managing-cost-targets-for-your-lab)

     Os seguintes custos *não* estão incluídos no cálculo de custos:

   * As assinaturas do CSP e do Dreamspark não têm suporte atualmente, pois o Azure DevTest Labs usa as [APIs de cobrança do Azure](../cost-management-billing/manage/usage-rate-card-overview.md) para calcular o custo do laboratório e essas APIs não dão suporte a assinaturas do Dreamspark ou do CSP.
   * Suas tarifas da oferta. No momento, não é possível usar as tarifas de oferta (mostradas em sua assinatura) que você negociou com a Microsoft ou parceiros Microsoft. Somente as tarifas pagas conforme o uso são usadas.
   * Seus impostos
   * Seus descontos
   * Sua moeda de cobrança. No momento, o custo do laboratório é exibido apenas na moeda USD.

### <a name="managing-cost-targets-for-your-lab"></a>Gerenciando as metas de custos para seu laboratório
O DevTest Labs lhe permite gerenciar melhor os custos em seu laboratório definindo uma meta de gasto que você pode exibir no gráfico Tendência de custo estimada por mês. O DevTest Labs também pode enviar uma notificação quando a meta ou o limite de gasto especificado é atingido. 

1. Na página **de tendência Custo,** selecione **Gerenciar destino**.

    ![Botão Gerenciar meta](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Na página **Gerenciar destino,** especifique um alvo de gastos e limites. Você também pode definir se cada limite selecionado é relatado no gráfico de tendência de custos ou por meio de uma notificação de webhook.

    ![Painel Gerenciar meta](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Selecione um período durante o qual você deseja que as metas de custo sejam controladas.
      - **Mensal**: as metas de custo são controladas por mês.
      - **Fixo**: as metas de custo são rastreadas para o intervalo de datas especificado nas datas de início e término. Normalmente, esses valores representam quanto tempo seu projeto está programado para ser executado.
   - Especifique uma **Meta de custo**. Por exemplo, quanto você planeja gastar neste laboratório no período de tempo que você definiu.
   - Selecione para habilitar ou desabilitar qualquer limite que você deseja que seja relatado – em incrementos de 25% –, até 125% de sua **Meta de custo** especificada.
      - **Notificar**: quando esse limite for atingido, você será notificado por uma URL de webhook que você especificar.
      - **Gráfico no gráfico**: Quando esse limiar é atingido, os resultados são plotados no gráfico de tendência de custo que você pode visualizar, conforme descrito no gráfico Visualizando o gráfico de tendência de custo estimado mensal.
   - Se optar por **Notificar** quando o limite for atingido, você precisará especificar uma URL de webhook. Na área de integração de custos, selecione **Clique aqui para adicionar uma integração**. Digite uma **URL do Webhook** no painel configurar notificação e, em seguida, selecione **OK**.

       ![Painel Configurar notificação](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Se especificar **Notificar**, você precisará definir uma URL de webhook.
     - Da mesma forma, se definir uma URL de webhook, você precisará definir **Notificação** como **Ativado** no painel de limite de custos.
     - Você deve criar um webhook antes de inseri-lo aqui.  

       Para saber mais sobre os webhooks, veja [Criar um webhook ou uma função da API do Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Ver custo por recurso 
O recurso de tendência de custo mensal em laboratórios permite que você veja quanto você gastou no mês do calendário atual. Também mostra a projeção dos gastos até o fim do mês, com base em seus gastos nos últimos sete dias. Para ajudá-lo a entender por que os gastos no laboratório estão atendendo aos limites no início, você pode usar o **custo por** recurso de recurso que mostra o custo mensal **por recurso** em uma tabela.

1. Faça login no [portal Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Selecione **Configuração e políticas** no menu esquerdo.
5. Selecione **Custo por recurso** na seção De rastreamento de **custos** no menu esquerdo. Você vê os custos associados a cada recurso associado a um laboratório. 

    ![Custo por recurso](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Esse recurso ajuda você a identificar facilmente os recursos que custam mais para que você possa tomar medidas para reduzir os gastos em laboratório. Por exemplo, o custo de uma VM é baseado no tamanho da VM. Quanto maior o tamanho da VM, mais é o custo. Você pode facilmente encontrar o tamanho de uma VM e do proprietário, para que você possa falar com o proprietário da VM para entender por que tal tamanho de VM é necessário e se há uma chance de diminuir o tamanho.

[A política de desligamento](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) automático ajuda você a reduzir o custo, desligando as VMs do laboratório em uma determinada hora do dia. No entanto, um usuário de laboratório pode optar por sair da política de desligamento, o que aumenta o custo de execução da VM. Você pode selecionar uma VM na tabela para ver se ela foi optada pela política de desligamento automático. Se esse for o caso, você pode falar com o proprietário da VM para descobrir por que a VM foi optada fora da apólice.
 
## <a name="next-steps"></a>Próximas etapas
Eis aqui algumas coisas para experimentar a seguir:

* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) - saiba como definir as várias políticas usadas para controlar como seu laboratório e suas VMs são usados. 
* [Criar imagem personalizada](devtest-lab-create-template.md) – durante a criação de uma VM, você especifica uma base, que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada de um arquivo VHD.
* [Configurar imagens do Marketplace](devtest-lab-configure-marketplace-images.md) – os DevTest Labs dão suporte à criação de VMs com base em imagens do Azure Marketplace. Este artigo ilustra como especificar quais imagens (caso haja alguma) do Azure Marketplace podem ser usadas durante a criação de VMs em um laboratório.
* [Criar uma VM em um laboratório](devtest-lab-add-vm.md) – ilustra como criar uma VM de uma imagem base (personalizada ou do Marketplace) e como trabalhar com artefatos na VM.

