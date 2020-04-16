---
title: Começando com a disputa do fluxo de dados na Fábrica de Dados do Azure
description: Um tutorial sobre como preparar dados na Fábrica de Dados do Azure usando o fluxo de dados de disputa
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: a180a7a0c85b642ac09d1d027c95809c4638dee1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409002"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Preparar dados com fluxo de dados de disputa

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="create-a-wrangling-data-flow"></a>Crie um fluxo de dados de disputa

Existem duas maneiras de criar um fluxo de dados de disputa na Fábrica de Dados Do Azure. Uma maneira é clicar no ícone de mais e selecionar **Fluxo de dados** no painel de recursos de fábrica.

![Disputas](media/wrangling-data-flow/tutorial7.png)

O outro método está no painel de atividades da tela do gasoduto. Abra o acordeom **Mover e Transformar** e arraste a atividade de fluxo de **dados** para a tela.

Em ambos os métodos, no painel lateral que se abre, selecione **Criar novo fluxo de dados** e escolha O fluxo de dados de **Wrangling**. Clique em OK.

![Disputas](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Autor de um fluxo de dados de disputa

Adicione um **conjunto de dados Fonte** para o fluxo de dados de disputa. Você pode escolher um conjunto de dados existente ou criar um novo. Você também pode selecionar um conjunto de dados do sink. Você pode escolher um ou mais conjuntos de dados de origem, mas apenas um dissipador é permitido neste momento. Escolher um conjunto de dados do sink é opcional, mas pelo menos um conjunto de dados de origem é necessário.

> [!NOTE]
> Apenas o ADLS Gen 2 Delimited Text é suportado para visualização limitada. 

![Disputas](media/wrangling-data-flow/tutorial4.png)

Clique **em Criar** para abrir o editor de mashup online da Consulta de Energia.

![Disputas](media/wrangling-data-flow/tutorial5.png)

Autor do fluxo de dados de disputa usando preparação de dados sem código. Para a lista de funções disponíveis, consulte funções de [transformação](wrangling-data-flow-functions.md)/

![Disputas](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Executando e monitorando um fluxo de dados de disputa

Para executar uma execução de depuração de pipeline de um fluxo de dados de disputa, clique em **Depurar** na tela do pipeline. Uma vez que você publica seu fluxo de dados, **o Trigger agora** executa uma execução sob demanda do último pipeline publicado. A disputa de fluxos de dados pode ser agendada com todos os gatilhos existentes da Fábrica de Dados Do Azure.

![Disputas](media/wrangling-data-flow/tutorial3.png)

Vá até a guia **Monitorar** para visualizar a saída de uma execução de atividade de fluxo de dados desencadeada.

![Disputas](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Próximas etapas

Saiba como [criar um fluxo de dados de mapeamento](tutorial-data-flow.md).