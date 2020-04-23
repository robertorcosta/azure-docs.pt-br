---
title: Diagnóstico e solução de ferramentas
description: Saiba como solucionar problemas com seu aplicativo no Azure App Service com a ferramenta de diagnóstico e resolver no portal Azure.
keywords: serviço de aplicativo, serviço de aplicativo do azure, diagnósticos, suporte, aplicativo web, solução de problemas, autoajuda
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: df8f68e47776f46ae2d1331f85bd76d6cfe17b80
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869938"
---
# <a name="azure-app-service-diagnostics-overview"></a>Visão geral de diagnóstico do Serviço de Aplicativo do Azure

Quando você estiver executando um aplicativo Web, você deseja estar preparado para os problemas que podem surgir, desde os erros 500 até os usuários informando que seu site está inoperante. O diagnóstico do App Service é uma experiência inteligente e interativa para ajudá-lo a solucionar problemas do seu aplicativo sem necessidade de configuração. Quando você se depara com seu aplicativo, os diagnósticos do App Service apontam o que há de errado em guiá-lo para as informações certas para solucionar e resolver o problema de forma mais fácil e rápida.

Embora essa experiência seja mais útil quando você está tendo problemas com seu aplicativo nas últimas 24 horas, todos os gráficos de diagnóstico estão sempre disponíveis para você analisar.

O diagnóstico do Serviço de Aplicativo funciona não para apenas o aplicativo no Windows, mas também aplicativos em [Linux/contêineres](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [Ambiente do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/environment/intro) e [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).

## <a name="open-app-service-diagnostics"></a>Abra o diagnóstico do Serviço de Aplicativo

Para acessar os diagnósticos do App Service, navegue até o aplicativo web app service ou o app service environment no [portal Azure](https://portal.azure.com). No painel de navegação esquerdo, clique em **Diagnosticar e resolver problemas**.

Para funções do Azure, navegue até o aplicativo de função e, na navegação superior, clique nos **recursos da plataforma**e selecione Diagnosticar e resolver **problemas** da seção de gerenciamento de **recursos.**

Na página inicial de diagnósticos do App Service, você pode escolher a categoria que melhor descreve o problema com seu aplicativo usando as palavras-chave em cada azulejo da página inicial. Além disso, esta página é onde você pode encontrar **ferramentas de diagnóstico** para aplicativos windows. Consulte [ferramentas de diagnóstico (apenas para o aplicativo Windows)](#diagnostic-tools-only-for-windows-app).

![Home page](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

> [!NOTE]
> Se o aplicativo estiver desligado ou com desempenho lento, você pode [coletar um traçado de perfil](https://azure.github.io/AppService/2018/06/06/App-Service-Diagnostics-Profiling-an-ASP.NET-Web-App-on-Azure-App-Service.html) para identificar a causa raiz do problema. O perfil é leve e é projetado para cenários de produção.
>

## <a name="interactive-interface"></a>Interface interativa

Depois de selecionar uma categoria de página inicial que melhor se alinhe com o problema do seu aplicativo, a interface interativa do App Service diagnostics, Genie, pode guiá-lo através do diagnóstico e resolução de problemas com o seu aplicativo. Você pode usar os atalhos de azulejo fornecidos pelo Genie para visualizar o relatório de diagnóstico completo da categoria problema que você está interessado. Os atalhos de azulejo fornecem uma maneira direta de acessar suas métricas de diagnóstico.

![Atalhos de bloco](./media/app-service-diagnostics/tile-shortcuts-2.png)

Depois de clicar nessas telhas, você pode ver uma lista de tópicos relacionados ao problema descrito no azulejo. Esses tópicos fornecem trechos de informações notáveis do relatório completo. Você pode clicar em qualquer um desses tópicos para investigar mais sobre os problemas. Além disso, você pode clicar em **Exibir relatório completo** para explorar todos os tópicos em uma única página.

![Tópicos](./media/app-service-diagnostics/application-logs-insights-3.png)

![Ver relatório completo](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Relatório de diagnóstico

Depois de optar por investigar o assunto mais adiante clicando em um tópico, você pode ver mais detalhes sobre o tópico muitas vezes complementado com gráficos e marcações. O relatório de diagnóstico pode ser uma ferramenta poderosa para identificar o problema com o seu aplicativo.

![Relatório de diagnóstico](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Verificação de integridade

Se você não sabe o que há de errado com seu aplicativo ou não sabe por onde começar a solucionar seus problemas, o check-up de saúde é um bom lugar para começar. O check-up de saúde analisa seus aplicativos para dar uma visão geral rápida e interativa que aponta o que é saudável e o que está errado, dizendo onde procurar investigar o problema. Sua interface inteligente e interativa fornece orientações durante o processo de solução de problemas. O check-up de saúde é integrado com a experiência genie para aplicativos Windows e relatório de diagnóstico para aplicativos Linux.

### <a name="health-checkup-graphs"></a>Gráficos de check-up de saúde

Há quatro gráficos diferentes no exame de saúde.

- **solicitações e erros:** Um gráfico que mostra o número de solicitações feitas nas últimas 24 horas, juntamente com erros do servidor HTTP.
- **desempenho do aplicativo:** Um gráfico que mostra o tempo de resposta nas últimas 24 horas para vários grupos percentis.
- **Uso da CPU:** Um gráfico que mostra a porcentagem total de uso da CPU por instância nas últimas 24 horas.  
- **uso da memória:** Um gráfico que mostra a porcentagem total de uso da memória física por instância nas últimas 24 horas.

![Verificação de integridade](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Investigue problemas de código de aplicativo (apenas para o aplicativo Windows)

Como muitos problemas de aplicativo estão relacionados a problemas no código do aplicativo, o diagnóstico do Serviço de Aplicativo integra-se ao [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) para realçar exceções e problemas de dependência para correlacionar com o tempo de inatividade selecionado. Os Insights do aplicativo devem ser ativados separadamente.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Para exibir as exceções e dependências do Application Insights, selecione o **aplicativo web para baixo** ou atalhos lentos do aplicativo **web.**

### <a name="troubleshooting-steps-only-for-windows-app"></a>Etapas de solução de problemas (apenas para o aplicativo Windows)

Se um problema for detectado com uma categoria de problema específica nas últimas 24 horas, você poderá visualizar o relatório de diagnóstico completo, e os diagnósticos do App Service podem solicitar que você visualize mais conselhos de solução de problemas e próximos passos para uma experiência mais guiada.

![Insights de aplicativos e solução de problemas e próximos passos](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Ferramentas de diagnóstico (apenas para o aplicativo Windows)

As ferramentas de diagnóstico incluem ferramentas de diagnóstico mais avançadas que ajudam você a investigar problemas de código de aplicativos, lentidão, strings de conexão e muito mais. e ferramentas proativas que ajudam a mitigar problemas com o uso da CPU, solicitações e memória.

### <a name="proactive-cpu-monitoring"></a>Monitoramento proativo da CPU

O monitoramento proativo da CPU fornece uma maneira fácil e proativa de agir quando seu aplicativo ou filho processa seu aplicativo está consumindo altos recursos de CPU. Você pode definir suas próprias regras de limite de CPU para mitigar temporariamente um problema de CPU alto até que a causa real para o problema inesperado seja encontrada. Para obter mais informações, consulte [Mitigar seus problemas de CPU antes que eles aconteçam](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html).

![Monitoramento proativo da CPU](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>Auto-cura e auto-cura proativa

A cura automática é uma ação de mitigação que você pode tomar quando seu aplicativo está tendo um comportamento inesperado. Você pode definir suas próprias regras com base na contagem de solicitações, solicitação lenta, limite de memória e código de status HTTP para desencadear ações de mitigação. Use a ferramenta para mitigar temporariamente um comportamento inesperado até encontrar a causa raiz. Para obter mais informações, consulte [Anunciando a nova experiência de cura automática em diagnósticos de serviços de aplicativos.](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html)

![Auto-cura](./media/app-service-diagnostics/auto-healing-10.png)

Como o monitoramento proativo da CPU, a cura automática proativa é uma solução de turn-key para mitigar comportamentos inesperados do seu aplicativo. A cura automática proativa reinicia seu aplicativo quando o App Service determina que seu aplicativo está em um estado irrecuperável. Para obter mais informações, consulte [Introdução proativo auto heal](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html).

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>Análise de navegador e alteração (apenas para o aplicativo Windows)

Em uma grande equipe com integração contínua e onde seu aplicativo tem muitas dependências, pode ser difícil identificar a mudança específica que causa um comportamento insalubre. O Navegador ajuda a obter visibilidade na topologia do seu aplicativo, renderizando automaticamente um mapa de dependência do seu aplicativo e todos os recursos na mesma assinatura. O Navegador permite que você visualize uma lista consolidada de alterações feitas pelo seu aplicativo e suas dependências e reduza uma mudança que causa comportamento insalubre. Ele pode ser acessado através do navegador da página inicial **Navigator** e precisa ser ativado antes de usá-lo pela primeira vez. Para obter mais informações, consulte [Obter visibilidade nas dependências do seu aplicativo com o Navigator](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html).

![Página padrão do navegador](./media/app-service-diagnostics/navigator-default-page-11.png)

![Vista difusa](./media/app-service-diagnostics/diff-view-12.png)

A análise de alterações para alterações de aplicativos pode ser acessada através de atalhos de ladrilhos, **alterações de aplicativos** e falhas de **aplicativos** em disponibilidade **e desempenho** para que você possa usá-lo simultaneamente com outras métricas. Antes de usar o recurso, você deve primeiro atilá-lo. Para obter mais informações, consulte [Anunciando a nova experiência de análise de mudança sustais no App Service Diagnostics](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).

Publique suas perguntas ou comentários no [UserVoice](https://feedback.azure.com/forums/169385-web-apps) adicionando "[Diag]" no título.
