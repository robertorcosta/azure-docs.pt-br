---
title: Introdução ao fluxo de dados do Wrangling no Azure Data Factory
description: Um tutorial sobre como preparar dados no Azure Data Factory usando o fluxo de dados do Wrangling
author: dcstwh
ms.author: weetok
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 5ee1069ca24dc16a52d30450c0f2163130f549cb
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494878"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Preparar dados com o fluxo de dados do Wrangling

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

> [!NOTE]
> O fluxo de dados Wrangling está disponíveis no momento em visualização pública

## <a name="create-a-wrangling-data-flow"></a>Criar um fluxo de dados Wrangling

Há duas maneiras de criar um fluxo de dados Wrangling em Azure Data Factory. Uma delas é clicar no ícone de adição e selecionar o **fluxo de dados** no painel recursos de fábrica.

![Captura de tela que mostra o fluxo de dados no painel recursos de fábrica.](media/wrangling-data-flow/tutorial7.png)

O outro método está no painel atividades da tela do pipeline. Abra o adparador de **movimentação e transformação** e arraste a atividade **fluxo de dados** para a tela.

Em ambos os métodos, no painel lateral que é aberto, selecione **criar novo fluxo de dados** e escolha **Wrangling fluxo de dados**. Clique em OK.

![Captura de tela que realça a opção de fluxo de dados Wrangling.](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Criar um fluxo de dados do Wrangling

Adicione um **conjunto** de dados de origem para seu fluxo de data Wrangling. Você pode escolher um conjunto de um existente ou criar um novo. Você também pode selecionar um conjunto de um banco de uma coletor. Você pode escolher um ou mais conjuntos de data de origem, mas apenas um coletor é permitido neste momento. A escolha de um conjunto de coletas é opcional, mas pelo menos um conjunto de fonte de origem é necessário.

> [!NOTE]
> Somente o texto delimitado ADLS Gen 2 tem suporte para visualização limitada. 

![Wrangling](media/wrangling-data-flow/tutorial4.png)

Clique em **criar** para abrir o Power Query editor de mashup online.

![Captura de tela que mostra o botão criar que abre o Power Query editor de mashup online.](media/wrangling-data-flow/tutorial5.png)

Crie seu fluxo de dados do Wrangling usando a preparação de dados sem código. Para obter a lista de funções disponíveis, consulte [funções de transformação](wrangling-data-flow-functions.md).

![Captura de tela que mostra o processo de criação do fluxo de dados do Wrangling.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Executando e monitorando um fluxo de dados do Wrangling

Para executar uma depuração de pipeline de execução de um fluxo de dados Wrangling, clique em **depurar** na tela do pipeline. Depois de publicar o fluxo de dados, o **gatilho agora** executa uma execução sob demanda do último pipeline publicado. Os fluxos de dados do Wrangling podem ser agendados com todos os gatilhos de Azure Data Factory existentes.

![Captura de tela que mostra como adicionar um fluxo de dados Wrangling.](media/wrangling-data-flow/tutorial3.png)

Vá para a guia **Monitor** para visualizar a saída de uma execução de atividade de fluxo de dados Wrangling disparada.

![Captura de tela que mostra a saída de uma execução de atividade de fluxo de dados Wrangling disparada.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Próximas etapas

Saiba como [criar um fluxo de dados de mapeamento](tutorial-data-flow.md).