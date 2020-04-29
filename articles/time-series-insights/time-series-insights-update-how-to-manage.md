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
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1ec0d9c7ecf16c60c32abdf08b358268f460edb0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77087221"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Provisionar e gerenciar a Versão Prévia do Azure Time Series Insights

Este artigo descreve como criar e gerenciar um ambiente de Versão Prévia do Azure Time Series Insights usando o [portal do Azure](https://portal.azure.com/).

## <a name="overview"></a>Visão geral

Azure Time Series Insights ambientes de visualização são ambientes PAYG (pré- *pago* ).

Ao provisionar um ambiente de visualização de Azure Time Series Insights, você cria esses recursos do Azure:

* Um ambiente de Versão Prévia do Azure Time Series Insights  
* Uma conta de uso geral v1 do Armazenamento do Microsoft Azure
* Um armazenamento inesperado opcional para consultas mais rápidas e ilimitadas

> [!TIP]
> * Saiba [como planejar seu ambiente](./time-series-insights-update-plan.md).
> * Leia sobre como [Adicionar uma origem do hub de eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md) ou como [Adicionar uma origem do Hub IOT](./time-series-insights-how-to-add-an-event-source-iothub.md).

Você saberá como:

1. **(Opcional)** Associar cada ambiente de visualização de Azure Time Series Insights a uma origem de evento. Você também fornecerá uma propriedade de ID de carimbo de data/hora e um grupo de consumidores exclusivo para garantir que o ambiente tenha acesso aos eventos apropriados.

   > [!NOTE]
   > A etapa anterior é opcional ao provisionar um ambiente. Se você ignorar esta etapa, deverá anexar uma origem do evento ao ambiente posteriormente para que os dados possam ser acessados no ambiente.

1. Após concluir o provisionamento, você poderá modificar as políticas de acesso e outros atributos do ambiente para atender aos requisitos de negócios.

## <a name="create-the-environment"></a>Criar o ambiente

Para criar um ambiente de visualização de Azure Time Series Insights:

1. Selecione **PAYG** como a **camada**. Forneça um nome de ambiente e escolha o grupo de assinaturas e o grupo de recursos a serem usados. Em seguida, selecione um local com suporte para hospedar o ambiente.

   [![Criar uma instância do Azure Time Series Insights.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Insira uma ID do Time Series.

    > [!NOTE]
    > * A ID da série temporal diferencia *maiúsculas de minúsculas* e *imutável*. (Ela não poderá ser alterada após definida.)
    > * As IDs de série temporal podem ter até *três* chaves.
    > * Leia mais sobre [como escolher uma ID de série temporal](time-series-insights-update-how-to-id.md)

1. Crie uma conta de armazenamento do Azure selecionando um nome de conta de armazenamento e designando uma opção de replicação. Ao fazer isso, você cria automaticamente uma conta de uso geral v1 do Armazenamento do Azure. A conta é criada na mesma região que o ambiente de visualização de Azure Time Series Insights que você selecionou anteriormente.

    [![Configuração de armazenamento frio](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(Opcional)** Habilite a loja a quente para seu ambiente se você quiser consultas mais rápidas e ilimitadas sobre os dados mais recentes em seu ambiente. Você também pode criar ou excluir uma loja a quente por meio da opção de **configuração de armazenamento** no painel de navegação esquerdo, depois de criar um ambiente de visualização de time Series insights.

    [![Configuração de armazenamento quente](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(Opcional)** Você pode adicionar uma origem do evento agora. Você também pode aguardar até que a instância tenha sido provisionada.

   * O Time Series Insights dá suporte ao [Hub IOT do Azure](./time-series-insights-how-to-add-an-event-source-iothub.md) e aos [hubs de eventos do Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) como opções de origem do evento. Embora seja possível adicionar apenas uma única origem do evento ao criar o ambiente, você pode adicionar outra origem do evento posteriormente. 
   
     Você pode selecionar um grupo de consumidores existente ou criar um novo grupo de consumidores ao adicionar a origem do evento. É melhor criar um grupo de consumidores exclusivo para garantir que todos os eventos estejam visíveis para o ambiente de visualização de Azure Time Series Insights.

   * Escolha a propriedade de carimbo de data/hora apropriada. Por padrão, Azure Time Series Insights usa o tempo de enfileiramento de mensagens para cada origem de evento.

     > [!TIP]
     > O tempo de enfileiramento de mensagens pode não ser a configuração mais adequada para uso em cenários de eventos de lote ou em cenários de carregamento de dados históricos. Nesses casos, certifique-se de verificar sua decisão de usar ou não usar uma propriedade Timestamp.

     [![Guia Configuração de origem do evento](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Confirme se o seu ambiente foi provisionado e configurado da maneira desejada.

    [![Revisar + criar guia](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Gerenciar o ambiente

É possível gerenciar o ambiente de Versão Prévia do Azure Time Series Insights usando o portal do Azure. Há algumas diferenças importantes entre um ambiente de visualização do PAYG Azure Time Series Insights e os ambientes S1 ou S2 geralmente disponíveis para se lembrar ao gerenciar seu ambiente por meio do portal do Azure:

* A folha **visão geral** da visualização de portal do Azure tem as seguintes alterações:

  * A capacidade é removida porque não se aplica a ambientes PAYG.
  * A propriedade **ID da série temporal** é adicionada. Ela determina como os dados são particionados.
  * Conjuntos de dados de referência são removidos.
  * A URL exibida direciona você para o [Gerenciador da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md).
  * O nome da sua conta de Armazenamento do Azure é listado.

* A folha **Configurar** do portal do Azure é removida na visualização Azure Time Series insights porque ambientes PAYG não são configuráveis. No entanto, você pode usar a **configuração de armazenamento** para configurar o armazenamento quente introduzido recentemente.

* A folha de **dados de referência** do portal do Azure é removida na visualização Azure Time Series insights porque os dados de referência não fazem parte dos ambientes PAYG.

[![Ambiente de Versão Prévia do Azure Time Series Insights no portal do Azure](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre Time Series Insights ambientes geralmente disponíveis e ambientes de visualização lendo [planejar seu ambiente](./time-series-insights-update-plan.md).

- Saiba como [Adicionar uma origem do hub de eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Configurar uma [origem do Hub IOT](./time-series-insights-how-to-add-an-event-source-iothub.md).
