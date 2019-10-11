---
title: Modelagem de dados na Versão Prévia do Azure Time Series Insights | Microsoft Docs
description: Entenda a modelagem de dados na Versão Prévia do Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: seodec18
ms.openlocfilehash: 64f892f344780d4af58c70935b9b7b68bad9550d
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273751"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelagem de dados na Versão Prévia do Azure Time Series Insights

Este documento descreve como trabalhar com Modelos de Série Temporal seguindo a Versão Prévia do Azure Time Series Insights. Detalha vários cenários comuns de dados.

Para saber mais sobre como usar a atualização, leia [Gerenciador da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md).

## <a name="types"></a>Tipos

### <a name="create-a-single-type"></a>Criar um único tipo

1. Vá para o painel de seletor de Modelos de Série Temporal e selecione **Tipos** no menu. Recolha o painel para se concentrar nos tipos de Modelos de Série Temporal.

    [![Create um único tipo](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Selecione **+ adicionar**.
1. Insira todos os detalhes referentes a tipos e selecione **Criar**. Essa ação cria tipos no ambiente.

    [![Add um tipo](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Carregar em massa um ou mais tipos

1. Selecione **Carregar JSON**.
1. Selecione o arquivo que contém o conteúdo de tipo.
1. Escolha **Carregar**.

    [JSON de @no__t 1Upload](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Editar um único tipo

1. Selecione o tipo e selecione **Editar**. 
1. Faça as alterações necessárias e selecione **Salvar**.

    [![Edit um tipo](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Excluir um tipo

1. Selecione o tipo e selecione **Excluir**.
1. Se nenhuma instância estiver associada aos tipos, eles serão excluídos.

    [![Exclua um tipo](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hierarquias

### <a name="create-a-single-hierarchy"></a>Criar uma única hierarquia

1. Vá para o painel de seletor de Modelos de Série Temporal e selecione **Hierarquias** no menu. Recolha o painel para se concentrar nas hierarquias de Modelos de Série Temporal.

    [hierarquias ![Select](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Selecione **+ adicionar**.

    [![Add uma hierarquia](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Selecione **+ Adicionar nível** no painel direito.

    [![Add um nível](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Insira os detalhes de hierarquia e selecione **Criar**.

    [![Create um nível](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Carregar em massa uma ou mais hierarquias

1. Selecione **Carregar JSON**.
1. Selecione o arquivo que contém o conteúdo de hierarquia.
1. Escolha **Carregar**.

    [hierarquias de upload ![Bulk](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Editar uma única hierarquia

1. Selecione a hierarquia e selecione **Editar**.
1. Faça as alterações necessárias e selecione **Salvar**.

    [![Edit uma única hierarquia](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Excluir uma hierarquia

1. Selecione a hierarquia e selecione **Excluir**. 
1. Se nenhuma instância estiver associada à hierarquia, ela será excluída.

    [![Exclua uma hierarquia](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Instâncias

### <a name="create-a-single-instance"></a>Criar uma única instância

1. Vá para o painel de seletor de Modelos de Série Temporal e selecione **Instâncias** no menu. Recolha o painel para se concentrar nas instâncias de Modelos de Série Temporal.

    [![Create uma única instância](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Selecione **Adicionar**.

    [![Add uma instância](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Insira os detalhes da instância, selecione a associação de tipo e hierarquia e selecione **Criar**.

### <a name="bulk-upload-one-or-more-instances"></a>Carregar em massa uma ou mais instâncias

1. Selecione **Carregar JSON**.
1. Selecione o arquivo que contém o conteúdo das instâncias.

    [![Bulk carregar uma ou mais instâncias](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Escolha **Carregar**.

### <a name="edit-a-single-instance"></a>Editar uma única instância

1. Selecione a instância e selecione **Editar**. 
1. Faça as alterações necessárias e selecione **Salvar**.

    [![Edit uma única instância](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre Modelos de Série Temporal, leia [Modelagem de dados](./time-series-insights-update-tsm.md).

- Para saber mais sobre a versão prévia, leia [Visualizar dados no Gerenciador da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md).

- Para saber mais sobre formas JSON com suporte, leia [Formas JSON com suporte](./time-series-insights-send-events.md#supported-json-shapes).
