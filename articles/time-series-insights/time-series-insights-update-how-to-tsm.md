---
title: Modelagem de dados em ambientes de visualização - Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre modelagem de dados no Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/18/2020
ms.custom: seodec18
ms.openlocfilehash: 39ebbf99ad31cce20eabc20fbdc056c889235713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470744"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelagem de dados na Versão Prévia do Azure Time Series Insights

Este artigo descreve como trabalhar com o Time Series Model no Azure Time Series Insights Preview. Detalha vários cenários comuns de dados.

> [!TIP]
> * Leia sobre o [Modelo de Série de Tempo de](time-series-insights-update-tsm.md)Visualização .
> * Saiba mais sobre como navegar na Visualização de ICarros no [explorador de visualização de visualizações da série do tempo do Azure](./time-series-insights-update-explorer.md).

## <a name="instances"></a>Instâncias

O explorador Azure Time Series Insights suporta operações **Instance CREATE,** **READ,** **UPDATE**e **DELETE** dentro do navegador. 

Para começar, selecione a exibição **Modelo** do explorador Desportos da série tempo **Analyze.**

### <a name="create-a-single-instance"></a>Criar uma única instância

1. Vá para o painel seletor de modelo de série temporal e selecione **Instâncias** no menu. Todas as instâncias associadas ao ambiente de Insights da série de tempo selecionada serão exibidas.

    [![Crie uma única instância selecionando primeiramente Instâncias.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Selecione **+ Adicionar**.

    [![Adicione uma instância selecionando o botão + Adicionar.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Insira os detalhes da instância, selecione a associação de tipo e hierarquia e selecione **Criar**.

### <a name="bulk-upload-one-or-more-instances"></a>Carregar em massa uma ou mais instâncias

> [!TIP]
> Você pode salvar suas instâncias na área de trabalho no JSON. O arquivo JSON baixado pode então ser carregado através das seguintes etapas.

1. Selecione **Carregar JSON**.
1. Selecione o arquivo que contém o conteúdo das instâncias.

    [![Instâncias de upload em massa através do JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Selecione **Carregar**.

### <a name="edit-a-single-instance"></a>Editar uma única instância

1. Selecione a instância e selecione o ícone **de edição** ou **lápis**. 
1. Faça as alterações necessárias e selecione **Salvar**.

    [![Edite uma única instância.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Excluir uma instância

1. Selecione a instância e selecione o ícone **excluir** ou **lixeira**.

   [![Exclua uma instância selecionando Excluir.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Confirme a exclusão selecionando **Excluir**.

> [!NOTE]
> Uma instância deve passar com sucesso uma verificação de validação de campo para ser excluída.

## <a name="hierarchies"></a>Hierarquias

O explorador Azure Time Series Insights suporta operações Hierarchy **CREATE,** **READ,** **UPDATE**e **DELETE** dentro do navegador. 

Para começar, selecione a exibição **Modelo** do explorador Desportos da série tempo **Analyze.**

### <a name="create-a-single-hierarchy"></a>Criar uma única hierarquia

1. Vá para o painel seletor de modelo de série de tempo e selecione **Hierarquias** no menu. Todas as hierarquias associadas ao ambiente de Insights da série de tempo selecionada serão exibidas.

    [![Crie uma hierarquia através do painel.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Selecione **+ Adicionar**.

    [![Hierarquia + Adicionar botão.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Selecione **+ Adicione nível** no painel direito.

    [![Adicione um nível à hierarquia.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Digite os detalhes da hierarquia e selecione **Salvar**.

    [![Especifique detalhes da hierarquia.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Carregar em massa uma ou mais hierarquias

> [!TIP]
> Você pode salvar suas hierarquias para a sua área de trabalho no JSON. O arquivo JSON baixado pode então ser carregado através das seguintes etapas.

1. Selecione **Carregar JSON**.
1. Selecione o arquivo que contém o conteúdo de hierarquia.
1. Selecione **Carregar**.

    [![Seleções para upload em massa de hierarquias.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Editar uma única hierarquia

1. Selecione a hierarquia e selecione o ícone **de edição** ou **lápis**.
1. Faça as alterações necessárias e selecione **Salvar**.

    [![Seleções para editar uma única hierarquia.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Excluir uma hierarquia

1. Selecione a hierarquia e selecione o ícone **excluir** ou **lixeira**. 

    [![Exclua uma hierarquia selecionando o botão Excluir.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Confirme a exclusão selecionando **Excluir**.

## <a name="types"></a>Tipos

O explorador Azure Time Series Insights suporta operações Type **CREATE,** **READ,** **UPDATE** **e DELETE** dentro do navegador. 

Para começar, selecione a exibição **Modelo** do explorador Desportos da série tempo **Analyze.**

### <a name="create-a-single-type"></a>Criar um único tipo

1. Vá para o painel seletor de modelo de série temporal e selecione **Tipos** no menu. Todos os tipos associados ao ambiente de Insights da série de tempo selecionado serão exibidos.

    [![Modelo de série temporal tipo painel.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Selecione **+ Adicione** para exibir o **Adicionar um novo tipo** de moda pop-up.
1. Digite propriedades e variáveis para o seu tipo. Uma vez inserido, **selecione Salvar**. 

    [![Configuração configuração para adicionar um tipo.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Carregar em massa um ou mais tipos

> [!TIP]
> Você pode salvar seus tipos na área de trabalho no JSON. O arquivo JSON baixado pode então ser carregado através das seguintes etapas.

1. Selecione **Carregar JSON**.
1. Selecione o arquivo que contém o conteúdo de tipo.
1. Selecione **Carregar**.

    [![Opções de upload de tipos em massa.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Editar um único tipo

1. Selecione o tipo e selecione o ícone **de edição** ou **lápis**.
1. Faça as alterações necessárias e selecione **Salvar**.

    [![Edite um tipo no painel.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Excluir um tipo

1. Selecione o tipo e selecione o ícone **excluir** ou **lixeira**. .

   [![Exclua um tipo selecionando Excluir.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Confirme a exclusão selecionando **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre o Time Series Model, leia [modelagem de dados](./time-series-insights-update-tsm.md).

- Para saber mais sobre a versão prévia, leia [Visualizar dados no Gerenciador da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md).

- Para saber mais sobre as formas JSON suportadas, leia [as formas JSON suportadas](./time-series-insights-send-events.md#supported-json-shapes).
