---
title: Entenda o uso de aplicativos móveis e o comportamento do usuário com o Visual Studio App Center e os serviços do Azure
description: Conheça os serviços como o App Center que ajudam você a tomar decisões de negócios inteligentes, entendendo como os usuários usam seu aplicativo móvel.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 4dc3cea4497c55dda0d8da2ca29201615dadff19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241074"
---
# <a name="analyze-and-understand-mobile-application-use"></a>Analisar e entender o uso de aplicativos móveis
Quão bem você entende como seus usuários usam seus aplicativos? Quantos usuários ativos seu aplicativo tem e como o uso está mudando ao longo do tempo? Quais características eles estão usando e quais são mais usados? Onde esses usuários estão baseados? Quantos usuários estão usando a versão mais recente do aplicativo? Todas essas perguntas são importantes para entender para transformar seu aplicativo em um negócio de sucesso. Para responder a esses tipos de perguntas de análise de uso, você precisa coletar dados de uso de seus aplicativos.

Quanto mais você olha para os dados, mais você pode encontrar maneiras de melhorar seu aplicativo e manter seus usuários felizes. É importante usar dados para encontrar insights acionáveis e manter os usuários satisfeitos.

## <a name="importance-of-analytics"></a>Importância da análise
- Entenda seus usuários, como eles interagem com seu aplicativo e o que os traz de volta para ajustar seu aplicativo e proporcionar ótimas experiências para crescer o seu negócio.
- Acompanhe suas métricas de uso para tomar decisões informadas sobre como comercializar sua aplicação e melhor atender seus clientes.
- Meça o desempenho da aplicação.
- Saiba quais partes do valor e desempenho da unidade de aplicativos.
- Obtenha insights baseados em dados sobre questões que dizem respeito à rotatividade e retenção.

Use os seguintes serviços para permitir a análise de aplicativos móveis.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[O App Center Analytics](/appcenter/analytics/) permite que você cresça sua audiência focando no que é importante. Ele oferece relatórios profundos e insights sobre sessões de usuários, dispositivos de topo, versões do Sistema Operacional e análises comportamentais. Crie facilmente eventos personalizados para rastrear qualquer coisa com extensaanálise de aplicativos.

   **Principais características**
   - Acompanhe padrões de uso, adoção do usuário e outras métricas de engajamento gratuitamente.
   - Identifique tendências, comportamento do usuário e engajamento por meio de eventos personalizados.
   - Obtenha métricas fora da caixa e insights detalhados sobre o uso do aplicativo (diário, semanal, mensal), sessões, propriedades do dispositivo e demografia do usuário em um único painel.
   - Exporte continuamente todos os dados do App Center Analytics para o Azure para retenção ilimitada. O App Center Analytics suporta a exportação para o armazenamento Azure Blob e o Azure Application Insights.
   - Integre-se ao Azure Application Insights para obter insights ainda mais profundos, como retenção, análise de funil e coortes.
   - Use a integração SDK de uma linha para começar em poucos minutos.
   - Ganhe suporte à plataforma para iOS, Android, macOS, tvOS, Xamarin, React Native, Unity e Cordova.

   **Referências**
   - [Inscrever-se com o App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Comece com o App Center Analytics](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
O Azure Monitor inclui [o Application Insights](/azure/azure-monitor/app/app-insights-overview), que fornece ferramentas para coletar e analisar a telemetria para maximizar o desempenho e monitorar seu aplicativo móvel. Você pode aproveitar os insights do aplicativo usando o App Center Analytics para configurar a exportação para insights de aplicativos. O Application Insights pode consultar, segmentar, filtrar e analisar a telemetria de eventos personalizada de seus aplicativos, além das ferramentas de análise que o App Center fornece.

**Principais características**
   - Consulte a telemetria de evento personalizado.
   - Filtrar a telemetria de eventos com poderosos recursos de segmentação.
   - Analise os padrões de conversão, retenção e navegação em sua aplicação. Você pode usar:
     - Funis para analisar e monitorar taxas de conversão.
     - Retenção para analisar o quão bem seu aplicativo retém os usuários ao longo do tempo.
     - Livros de trabalho para combinar visualizações e texto em um relatório compartilhável.
     - Coortes para nomear e salvar grupos específicos de usuários ou eventos para que eles possam ser facilmente referenciados a partir de outras ferramentas de análise.

**Referências**
- [Portal Azure](https://portal.azure.com/)
- [Analise seu aplicativo móvel com app center e insights de aplicativos](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Use o App Center Analytics com insights de aplicativos](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[O Azure PlayFab](https://playfab.com/) oferece uma plataforma de back-end completa com serviços de jogos, análises em tempo real e LiveOps que você precisa para criar jogos conectados à nuvem de classe mundial. Esses serviços reduzem as barreiras para o lançamento para desenvolvedores de jogos. Eles oferecem soluções de desenvolvimento econômicas de estúdios grandes e pequenos que são dimensionadas com seus jogos. Os serviços podem ajudar os estúdios a engajar, reter e monetizar jogadores. Com o PlayFab, os desenvolvedores podem usar a nuvem inteligente para construir e operar jogos, analisar dados de jogos e melhorar as experiências gerais de jogo.

**Principais características**
   - Monitore os painéis em tempo real.
   - Avalie o desempenho do seu jogo através de métricas superiores.
   - Revise resumos do desempenho diário e mensal do seu jogo através de relatórios gerados automaticamente. Você pode ver os relatórios no Game Manager e tê-los baixados ou entregues em sua caixa de entrada diariamente.
   - Use testes A/B para executar experimentos e determinar a configuração ideal para uma determinada variável.
   - Use segmentação para jogadores para definir agrupamentos automatizados de jogadores.
    
**Referências**
- [Portal PlayFab](https://developer.playfab.com/en-US/sign-up)
- [Analytics](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Inícios rápidos](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
