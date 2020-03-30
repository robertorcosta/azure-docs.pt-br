---
title: Autoria visual
description: Saiba como usar a criação visual no Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 12/19/2019
ms.openlocfilehash: 09d4055ba98da2dd87efc9421402f2827a87ba16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440928"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Criação visual no Azure Data Factory

A experiência (UX) da interface do usuário do Azure Data Factory permite criar e implantar visualmente recursos para seu data factory sem ter que gravar nenhum código. Você pode arrastar atividades para uma tela de pipeline, realizar execuções de teste, depurar iterativamente e implantar e monitorar as execuções do pipeline.

Atualmente, o UX do Azure Data Factory só é suportado no Microsoft Edge e no Google Chrome.

## <a name="authoring-canvas"></a>Tela de autoria

Para abrir a **tela de autoria,** clique no ícone do lápis. 

![Tela de Autoria](media/author-visually/authoring-canvas.png)

Aqui, você será o autor dos pipelines, atividades, conjuntos de dados, serviços vinculados, fluxos de dados, gatilhos e tempos de execução de integração que compõem sua fábrica. Para começar a construir um pipeline usando a tela de autoria, consulte [Copiar dados usando a atividade de cópia](tutorial-copy-data-portal.md). 

A experiência padrão de autoria visual está trabalhando diretamente com o serviço Data Factory. A integração do Azure Repos Git ou do GitHub também é suportada para permitir o controle de origem e a colaboração para o trabalho em seus pipelines de fábrica de dados. Para saber mais sobre as diferenças entre essas experiências autorais, consulte [Controle de Origem na Fábrica de Dados do Azure](source-control.md).

## <a name="expressions-and-functions"></a>Expressões e funções

Expressões e funções podem ser usadas em vez de valores estáticos para especificar muitas propriedades na Fábrica de Dados Do Azure.

Para especificar uma expressão para um valor de propriedade, **selecione Adicionar conteúdo dinâmico** ou clique em **Alt + P** enquanto foca no campo.

![Adicionar conteúdo dinâmico](media/author-visually/dynamic-content-1.png)

Isso abre o **Data Factory Expression Builder** onde você pode construir expressões a partir de variáveis de sistema suportadas, saída de atividade, funções e variáveis ou parâmetros especificados pelo usuário. 

![Construtor de expressões](media/author-visually/dynamic-content-2.png)

Para obter informações sobre a linguagem de expressão, consulte [Expressões e funções na Fábrica de Dados Do Azure](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Fornecer comentários

Selecione **Feedback** para comentar sobre os recursos ou notificar a Microsoft sobre problemas com a ferramenta:

![Comentários](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o monitoramento e o gerenciamento de pipelines, c [Monitorar e gerenciar os pipelines programaticamente](monitor-programmatically.md).
