---
title: Como adicionar conjuntos de dados de referência ao seu ambiente-Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como adicionar um conjunto de dados de referência para ampliar dados no seu ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/05/2020
ms.custom: seodec18~~~~
ms.openlocfilehash: 010f7b908ceb9c8d07208db1312d8af8d4e27051
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838511"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Criar um conjunto de dados de referência para o seu ambiente Time Series Insights usando o Portal do Azure

Este artigo descreve como adicionar um conjunto de dados de referência ao seu ambiente Azure Time Series Insights. É útil adicionar dados de referência em sua fonte de dados para aumentar os valores.

Um conjunto de dados de referência é uma coleção de itens que aumentam os eventos da fonte de evento. O mecanismo de entrada da Análise de Séries Temporais une cada evento da fonte de evento à linha de dados correspondentes em seu conjunto de dados de referência. Esse evento aumentado é disponibilizado para consulta. Essa junção baseia-se na(s) coluna(s) de chave primária definidas no conjunto de dados de referência.

Os dados de referência não estão unidos retroativamente. Assim, somente os dados de entrada atuais e futuros são correspondidos e associados à data de referência definida, depois que ele tiver sido configurado e carregado.

## <a name="video"></a>Vídeo

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Saiba mais sobre o modelo de dados de referência do time Series Insight.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Adicionar um conjunto de dados de referência

1. Entre no [portal do Azure](https://portal.azure.com).

1. Localize seu ambiente de Azure Time Series Insights existente. Selecione **Todos os recursos** no menu à esquerda do portal do Azure. Selecione o seu ambiente de Análise de Séries Temporais.

1. Selecione a página **Visão geral**. Expanda a seção **Essentials** próxima à parte superior da página para localizar a **URL do time Series insights Explorer** e abrir o link.  

   [![Seção expandir noções básicas](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   Exiba o Gerenciador de seu ambiente de Time Series Insights.

1. Expanda o seletor de ambiente no Time Series Insights Explorer. Escolha o ambiente ativo. Selecione o ícone de dados de referência no canto superior direito na página do Gerenciador.

   [![Adicione dados de referência](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. Selecione o botão **+ Adicionar um conjunto de dados** para começar a adicionar um novo conjunto de dados.

   [![Adicione o conjunto de dados](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. Na página **Novo conjunto de dados de referência**, escolha o formato dos dados:

   - Escolha **CSV** para dados delimitados por vírgula. A primeira linha é tratada como uma linha de cabeçalho.
   - Escolha a **matriz JSON** para dados formatados do JSON (JavaScript Object Notation).

   [![Escolha a fonte de dados.](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. Forneça os dados, usando um dos dois métodos:

   - Cole os dados no editor de texto. Em seguida, selecione o botão **Analisar dados de referência**.
   - Selecione o botão **Escolher arquivo** para adicionar dados de um arquivo de texto local.

   Por exemplo, colar dados CSV: [ ![dados CSV colados](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   Por exemplo, Cole dados da matriz JSON: [ ![colar dados JSON](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   Se houver um erro ao analisar os valores de dados, o erro aparecerá em vermelho na parte inferior da página, como `CSV parsing error, no rows extracted`.

1. Depois que os dados são analisados com êxito, uma grade de dados é mostrada exibindo as colunas e linhas que representam os dados. Examine a grade de dados para garantir a exatidão.

   [![Examinar dados de referência](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. Examine cada coluna para entender o tipo de dados assumido e altere o tipo de dados, se necessário.  Selecione o símbolo de tipo de dados no título da **#** coluna: para duplo (dados numéricos), **T | F** para booliano ou **ABC** para cadeia de caracteres.

   [![Escolha os tipos de dados nos cabeçalhos de coluna.](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. Renomeie os cabeçalhos de coluna, se necessário. O nome da coluna de chave é necessário para associar à propriedade correspondente na fonte de evento.

   > [!IMPORTANT]
   > Certifique-se de que os nomes de coluna de chave dos dados de referência correspondem exatamente ao nome do evento para seus dados de entrada, incluindo diferenciar maiúsculas de minúsculas. Os nomes de coluna não chave são usados para ampliar os dados de entrada com os valores de dados de referência correspondentes.

1. Digite um valor no campo **Filtrar as linhas... ** para examinar linhas específicas, conforme necessário. O filtro é útil para análise de dados, mas não é aplicado ao carregar os dados.

1. Nomeie o conjunto de dados, preenchendo o campo **Nome do conjunto de dados** acima da grade de dados.

    [![Nomeie o conjunto de dados.](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. Forneça a coluna de **chave primária** no conjunto de dados, selecionando na lista suspensa acima da grade de dados.

    [![Selecione a(s) coluna(s) de chave.](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(Opcional)** Selecione o **+** botão para adicionar uma coluna de chave secundária, como uma chave primária composta. Se você precisar desfazer a seleção, escolha o valor vazio da lista suspensa para remover a chave secundária.

1. Para carregar os dados, selecione o botão **Carregar linhas**.

    [![Carregar linhas e confirmar dados.](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    A página confirma o carregamento concluído e exibe a mensagem **Conjunto de dados carregado com êxito**.

    > [!WARNING]
    > Colunas ou Propriedades compartilhadas entre conjuntos de dados de referência exibirão um erro de carregamento de **nome de propriedade duplicado** . O erro não impedirá o upload bem-sucedido dos conjuntos de dados de referência. Ele pode ser removido por meio da combinação de linhas que compartilham o nome da propriedade duplicada.

1. Selecione **Adicionar uma linha**, **importar linhas em massa**ou **Adicionar uma coluna** para adicionar mais valores de dados de referência, conforme necessário.

    [![Adicione uma linha, importe linhas em massa ou adicione uma coluna.](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > Qualquer linha que compartilha uma chave exclusiva com outra linha terá suas colunas substituídas pela última linha adicionada que compartilha essa chave exclusiva.

   > [!NOTE]
   > As linhas adicionadas **não** precisam ser *retangulares* . elas podem ter menos colunas, maiores ou variáveis diferentes das outras entradas no conjunto de dados de referência.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar dados de referência](time-series-insights-manage-reference-data-csharp.md) programaticamente.

* Para obter a referência completa da API, leia documento [API de dados de referência](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api) .
