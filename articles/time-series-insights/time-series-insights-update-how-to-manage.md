---
title: Provisionar e gerenciar um ambiente de visualização – série temporal do Azure | Microsoft Docs
description: Saiba como provisionar e gerenciar um ambiente de visualização de Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: f66925c20f6767286eb98a7f5fab180845da4c33
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014795"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Provisionar e gerenciar a Versão Prévia do Azure Time Series Insights

Este artigo descreve como criar e gerenciar um ambiente de Versão Prévia do Azure Time Series Insights usando o [portal do Azure](https://portal.azure.com/).

## <a name="overview"></a>Visão geral

Os ambientes de Versão Prévia do Azure Time Series Insights são ambientes de PAYG (pago conforme o uso).

Ao provisionar um ambiente de visualização de Azure Time Series Insights, você cria esses recursos do Azure:

* Um ambiente de Versão Prévia do Azure Time Series Insights  
* Uma conta de uso geral v1 do Armazenamento do Microsoft Azure
* Um armazenamento inesperado opcional para consultas mais rápidas e ilimitadas
  
Saiba [como planejar o ambiente](./time-series-insights-update-plan.md).

Associar cada ambiente de visualização de Azure Time Series Insights a uma origem de evento. Para obter mais informações, leia [Adicionar uma origem de Hub de Eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md) e [Adicionar uma origem de Hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md). Durante essa etapa, você fornecerá uma propriedade de ID de carimbo de data/hora e um grupo de consumidores exclusivo. Isso garante que o ambiente tenha acesso aos eventos apropriados.

> [!NOTE]
> A etapa anterior é opcional no fluxo de trabalho de provisionamento enquanto você cria o ambiente de visualização de série temporal. No entanto, você deve anexar uma origem do evento ao ambiente para que os dados possam começar a fluir nesse ambiente.

Após concluir o provisionamento, você poderá modificar as políticas de acesso e outros atributos do ambiente para atender aos requisitos de negócios.

## <a name="create-the-environment"></a>Criar o ambiente

Para criar um ambiente de visualização de Azure Time Series Insights:

1. No menu **SKU** , selecione o botão **PAYG** . Forneça um nome de ambiente e escolha o grupo de assinaturas e o grupo de recursos a serem usados. Em seguida, selecione um local com suporte para hospedar o ambiente.

   [![criar uma instância de Azure Time Series Insights.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. Insira uma ID do Time Series.

    >[!NOTE]
    > * A ID do Time Series diferencia maiúsculas de minúsculas e é imutável. (Ela não poderá ser alterada após definida.)
    > * IDs de Série Temporal podem ter de até três chaves.
    > * Para obter mais informações sobre como selecionar uma ID do Time Series, leia [Escolher uma ID do Time Series](./time-series-insights-update-how-to-id.md).

1. Crie uma conta de armazenamento do Azure, selecionando um nome de conta de armazenamento e designando uma opção de replicação. Ao fazer isso, você cria automaticamente uma conta de uso geral v1 do Armazenamento do Azure. A conta é criada na mesma região que o ambiente de visualização de Azure Time Series Insights que você selecionou anteriormente.

    [![criar uma conta de armazenamento do Azure para sua instância](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. **(Opcional)** Habilite a loja a quente para seu ambiente se você quiser consultas mais rápidas e ilimitadas sobre os dados mais recentes em seu ambiente. Você também pode criar ou excluir uma loja a quente por meio da opção de **configuração de armazenamento** no painel de navegação esquerdo, depois de criar um ambiente de visualização de time Series insights.

1. **(Opcional)** Você pode adicionar uma origem do evento agora. Como alternativa, você pode aguardar até que a instância tenha sido provisionada.

   * O Time Series Insights dá suporte ao [Hub IOT do Azure](./time-series-insights-how-to-add-an-event-source-iothub.md) e aos [hubs de eventos do Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) como opções de origem do evento. Embora seja possível adicionar apenas uma única origem do evento ao criar o ambiente, você pode adicionar outra origem do evento posteriormente. Você pode selecionar um grupo de consumidores existente ou criar um novo grupo de consumidores ao adicionar a origem do evento. É melhor criar um grupo de consumidores exclusivo para garantir que todos os eventos estejam visíveis para o ambiente de visualização de Azure Time Series Insights.

   * Escolha a propriedade de carimbo de data/hora apropriada. Por padrão, Azure Time Series Insights usa o tempo de enfileiramento de mensagens para cada origem de evento.

     > [!TIP]
     > O tempo de enfileiramento de mensagens pode não ser a configuração mais adequada para uso em cenários de eventos de lote ou em cenários de carregamento de dados históricos. Nesses casos, certifique-se de verificar sua decisão de usar ou não usar uma propriedade Timestamp.

     [guia origem do evento ![](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. Confirme se o seu ambiente foi provisionado com as configurações desejadas.

    [![revisar + criar guia](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>Gerenciar o ambiente

É possível gerenciar o ambiente de Versão Prévia do Azure Time Series Insights usando o portal do Azure. Quando você gerencia o por meio do portal do Azure, você vê algumas diferenças importantes entre um ambiente de visualização de pré-pago Azure Time Series Insights e os ambientes S1 ou S2 geralmente disponíveis:

* A folha de **Visão geral** do portal do Azure permanece inalterada no Azure Time Series Insights, exceto conforme a seguir:
  * A capacidade é removida porque não se aplica aos ambientes pagos conforme o uso.
  * A propriedade ID da série temporal é adicionada. Ela determina como os dados são particionados.
  * Conjuntos de dados de referência são removidos.
  * A URL exibida direciona você para o [Gerenciador da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md).
  * O nome da conta de armazenamento do Azure é listado.

* A folha **Configurar** do portal do Azure é removida na visualização Azure Time Series insights porque ambientes PAYG não são configuráveis. No entanto, você pode usar a **configuração de armazenamento** para configurar o armazenamento quente introduzido recentemente.

* A folha de **dados de referência** do portal do Azure é removida na visualização Azure Time Series insights porque os dados de referência não fazem parte dos ambientes pagos conforme o uso.

[![Time Series Insights ambiente de visualização no portal do Azure](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre Time Series Insights ambientes geralmente disponíveis e ambientes de visualização lendo [planejar seu ambiente](./time-series-insights-update-plan.md).

- Saiba como [Adicionar uma origem do hub de eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Configurar uma [origem do Hub IOT](./time-series-insights-how-to-add-an-event-source-iothub.md).
