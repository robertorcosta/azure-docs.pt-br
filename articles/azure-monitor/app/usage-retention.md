---
title: Analisar a retenção de usuários de aplicativos da Web com o Azure Application Insights
description: Quantos usuários retornam ao seu aplicativo?
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 05/03/2017
ms.reviewer: mbullwin
ms.openlocfilehash: e6d9be6bc6d7f90153e2fb58aa404e281568dbdd
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892403"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Análise de retenção de usuários para aplicativos Web com o Application Insights

O recurso de retenção no [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) ajuda a analisar quantos usuários retornam ao seu aplicativo e com que frequência eles executam determinadas tarefas ou atingem metas. Por exemplo, caso gerencie um site de jogos, você pode comparar o número de usuários que retornam ao site após perder um jogo com o número de usuários que retornam após vencer. Esse conhecimento pode ajudar a melhorar a experiência do usuário e sua estratégia de negócios.

## <a name="get-started"></a>Introdução

Caso ainda não veja dados na ferramenta de retenção do portal do Application Insights, [saiba como começar a usar as ferramentas de uso](usage-overview.md).

## <a name="the-retention-tool"></a>A ferramenta de retenção

![Ferramenta de retenção](./media/usage-retention/retention.png)

1. A barra de ferramentas permite aos usuários criar novos relatórios de retenção, abrir os relatórios de retenção existentes, salvar o relatório de retenção atual ou salvar como, reverter as alterações feitas nos relatórios salvos, atualizar dados no relatório, compartilhar relatórios por email ou link direto e acessar a página de documentação. 
2. Por padrão, a retenção mostra todos os usuários que fizeram algo e então voltaram e fizeram outra coisa em um período. Você pode selecionar diferentes combinações de eventos para estreitar o foco em atividades de usuário específico.
3. Adicione um ou mais filtros para as propriedades. Por exemplo, você pode se concentrar em usuários de um determinado país ou região. Clique em **Atualizar** depois de configurar os filtros. 
4. O gráfico de retenção geral mostra um resumo de retenção de usuário entre o período selecionado. 
5. A grade mostra o número de usuários retidos de acordo com o construtor de consultas no nº 2. Cada linha representa um coorte de usuários que realizaram qualquer evento no período mostrado. Cada célula na linha mostra quantas pessoas desse coorte retornaram pelo menos uma vez em um período posterior. Alguns usuários podem retornar em mais de um período. 
6. Os cartões de informações mostram os cinco principais eventos de início e os cinco eventos principais retornados para dar aos usuários uma ideia melhor sobre seus relatórios de retenção. 

![Foco de mouse de retenção](./media/usage-retention/hover.png)

Os usuários podem focalize células sobre a ferramenta de retenção para acessar as dicas de ferramenta e o botão de análise explicando o que significa que a célula. Botão análise leva o usuário para a ferramenta de análise com uma consulta preenchida previamente para gerar os usuários da célula. 

## <a name="use-business-events-to-track-retention"></a>Usar eventos de negócios para monitorar a retenção

Para obter a análise de retenção mais útil, meça eventos que representam atividades de negócios significativas. 

Por exemplo, muitos usuários podem abrir uma página em seu aplicativo sem jogar o jogo que ela exibe. Monitorar apenas as exibições de página, portanto, forneceria uma estimativa imprecisa de quantas pessoas retornam para jogar o jogo após tê-lo jogado anteriormente. Para obter uma visão clara dos jogadores que retornam, seu aplicativo deve enviar um evento personalizado quando um usuário de fato jogar.  

É uma boa prática codificar eventos personalizados que representam ações de negócios essenciais e usá-los para sua análise de retenção. Para capturar o resultado do jogo, você precisa escrever uma linha de código para enviar um evento personalizado para o Application Insights. Se você a escrever no código da página da Web ou no Node.JS, ela fica assim:

```JavaScript
    appinsights.trackEvent("won game");
```

Ou no código de servidor do ASP.NET:

```csharp
   telemetry.TrackEvent("won game");
```

[Saiba mais sobre como escrever eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Próximas etapas
- Para habilitar as experiências de uso, comece enviando [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [exibições de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se você já envia eventos personalizados ou exibições de página, explore as ferramentas de uso para saber como os usuários utilizam o seu serviço.
    - [Usuários, Sessões, Eventos](usage-segmentation.md)
    - [Funis](usage-funnels.md)
    - [Fluxos de Usuário](usage-flows.md)
    - [Pastas de trabalho](../../azure-monitor/app/usage-workbooks.md)
    - [Adicionar contexto de usuário](usage-send-user-context.md)


