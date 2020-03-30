---
title: Provisão e gerenciar um ambiente de visualização - Azure Time Series | Microsoft Docs
description: Aprenda a fornecer e gerenciar um ambiente de visualização de insights da série do tempo do Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087221"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Provisionar e gerenciar a Versão Prévia do Azure Time Series Insights

Este artigo descreve como criar e gerenciar um ambiente de Versão Prévia do Azure Time Series Insights usando o [portal do Azure](https://portal.azure.com/).

## <a name="overview"></a>Visão geral

Os ambientes de visualização da série de tempo do Azure são ambientes *pay-as-you-go* (PAYG).

Quando você disponibiliza um ambiente de visualização de visualizações da série de tempo do Azure, você cria esses recursos do Azure:

* Um ambiente de Versão Prévia do Azure Time Series Insights  
* Uma conta de uso geral v1 do Armazenamento do Microsoft Azure
* Uma loja quente opcional para consultas mais rápidas e ilimitadas

> [!TIP]
> * Aprenda [a planejar seu ambiente.](./time-series-insights-update-plan.md)
> * Leia sobre como [adicionar uma fonte de hub de eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md) ou como adicionar uma fonte de hub [IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

Você saberá como:

1. **(Opcional)** Associe cada ambiente de visualização de insights da série do tempo do Azure com uma fonte de evento. Você também fornecerá uma propriedade de Identificação de Carimbo de Tempo e um grupo de consumidores exclusivo para garantir que o ambiente tenha acesso aos eventos apropriados.

   > [!NOTE]
   > A etapa anterior é opcional ao provisionar um ambiente. Se você pular essa etapa, você deve anexar uma fonte de evento ao ambiente mais tarde para que os dados possam ser acessados no ambiente.

1. Após concluir o provisionamento, você poderá modificar as políticas de acesso e outros atributos do ambiente para atender aos requisitos de negócios.

## <a name="create-the-environment"></a>Criar o ambiente

Para criar um ambiente de visualização de visualizações da série do tempo do Azure:

1. Selecione **PAYG** como **o Tier**. Forneça um nome de ambiente e escolha o grupo de assinaturas e o grupo de recursos a usar. Em seguida, selecione um local suportado para hospedar o ambiente.

   [![Criar uma instância do Azure Time Series Insights.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Insira uma ID do Time Series.

    > [!NOTE]
    > * O ID da série temporal é *sensível a maiúsculas* e *imutáveis.* (Ela não poderá ser alterada após definida.)
    > * As IDs da série temporal podem ser de até *três* teclas.
    > * Leia mais sobre [como escolher uma ID de série de tempo](time-series-insights-update-how-to-id.md)

1. Crie uma conta do Azure Storage selecionando um nome de conta de armazenamento e designando uma escolha de replicação. Ao fazer isso, você cria automaticamente uma conta de uso geral v1 do Armazenamento do Azure. A conta é criada na mesma região do ambiente Azure Time Series Insights Preview que você selecionou anteriormente.

    [![Configuração de armazenamento a frio](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(Opcional)** Habilite o armazenamento quente para o seu ambiente se você quiser consultas mais rápidas e ilimitadas sobre os dados mais recentes em seu ambiente. Você também pode criar ou excluir uma loja quente através da opção **Configuração de armazenamento** no painel de navegação à esquerda, depois de criar um ambiente de visualização de insights da série do tempo.

    [![Configuração de armazenamento quente](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(Opcional)** Você pode adicionar uma fonte de evento agora. Você também pode esperar até que a instância tenha sido provisionada.

   * O Time Series Insights suporta [o Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) e [o Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) como opções de origem de eventos. Embora você possa adicionar apenas uma única fonte de evento ao criar o ambiente, você pode adicionar outra fonte de evento mais tarde. 
   
     Você pode selecionar um grupo de consumidores existente ou criar um novo grupo de consumidores ao adicionar a fonte do evento. É melhor criar um grupo de consumidores exclusivo para garantir que todos os eventos sejam visíveis para o ambiente de visualização de insights da série do tempo do Azure.

   * Escolha a propriedade de carimbo de tempo apropriada. Por padrão, o Azure Time Series Insights usa o tempo enfileirado por mensagem para cada fonte de evento.

     > [!TIP]
     > O tempo enfileirado por mensagem pode não ser a melhor configuração configurada para usar em cenários de eventos em lote ou cenários históricos de upload de dados. Nesses casos, certifique-se de verificar sua decisão de usar ou não uma propriedade timestamp.

     [![Guia de configuração da origem do evento](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Confirme se seu ambiente foi provisionado e configurado da maneira que você deseja.

    [![Guia Revisar + Criar](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Gerenciar o ambiente

É possível gerenciar o ambiente de Versão Prévia do Azure Time Series Insights usando o portal do Azure. Existem algumas diferenças importantes entre um ambiente de visualização de séries de tempo do PAYG Azure e os ambientes S1 ou S2 geralmente disponíveis para ter em mente quando você gerencia seu ambiente através do portal Azure:

* A lâmina de **visão geral do** portal Azure tem as seguintes alterações:

  * A capacidade é removida porque não se aplica a ambientes PAYG.
  * A **propriedade Time series ID** é adicionada. Ela determina como os dados são particionados.
  * Conjuntos de dados de referência são removidos.
  * A URL exibida direciona você para o [Gerenciador da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md).
  * O nome da sua conta de Armazenamento do Azure é listado.

* A lâmina **Configure** do portal Azure é removida na visualização de insights da série de tempo do Azure porque os ambientes PAYG não são configuráveis. No entanto, você pode usar a **configuração de armazenamento** para configurar o recém-introduzido warm store.

* A lâmina de dados de **referência** do portal Azure é removida na visualização de insights da série de tempo do Azure porque os dados de referência não fazem parte dos ambientes PAYG.

[![Ambiente de Versão Prévia do Azure Time Series Insights no portal do Azure](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o Time Series Insights geralmente ambientes disponíveis e ambientes de visualização lendo [Plan seu ambiente](./time-series-insights-update-plan.md).

- Aprenda a [adicionar uma fonte de hub de eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Configure uma [fonte de hub ioT](./time-series-insights-how-to-add-an-event-source-iothub.md).
