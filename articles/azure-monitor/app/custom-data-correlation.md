---
title: Azure Application Insights | Microsoft Docs
description: Correlacionar dados do Application Insights para outros conjuntos de dados, como enriquecimento de dados ou tabelas de pesquisa, fontes de dados não-application Insights e dados personalizados.
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 121e4699bd6a72f6865d3a6ffdef58c1b3806047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082754"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Correlacionando os dados do Application Insights com fontes de dados personalizadas

O Application Insights coleta vários tipos de dados diferentes: exceções, rastreamentos, exibições de página e outros. Embora isso seja muitas vezes suficiente para investigar o desempenho, a confiabilidade e o uso do seu aplicativo, há casos em que é útil correlacionar os dados armazenados no Application Insights com outros conjuntos de dados completamente personalizados.

Algumas situações em que é recomendável ter dados personalizados incluem:

- Tabelas de pesquisa ou enriquecimento de dados: por exemplo, complementar um nome do servidor com o proprietário do servidor e o local do laboratório no qual ele pode ser encontrado 
- Correlação com fontes de dados não pertencentes ao Application Insights: por exemplo, correlacionar os dados sobre uma compra em uma webstore com as informações do serviço de atendimento de compra para determinar a precisão das estimativas do tempo de entrega 
- Dados totalmente personalizados: muitos de nossos clientes adoram a linguagem de consulta e o desempenho da plataforma de logs do Azure Monitor que dá suporte ao Application Insights e desejam usá-la para consultar dados não relacionados ao Application Insights. Por exemplo, para rastrear o desempenho do painel solar como parte de uma instalação de casa inteligente, conforme descrito [aqui](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Como correlacionar dados personalizados com os dados do Application Insights 

Como o Application Insights tem suporte da avançada plataforma de logs do Azure Monitor, podemos usar toda a potência do Azure Monitor para ingerir os dados. Em seguida, escreveremos consultas usando o operador "join" que correlacionará esses dados personalizados com os dados disponíveis para nós nos registros do Monitor do Azure. 

## <a name="ingesting-data"></a>Ingerindo dados

Nesta seção, veremos como inserir seus dados nos logs do Azure Monitor.

Se você ainda não tiver um, disponibilize um novo espaço de trabalho do Log Analytics seguindo [essas instruções](../learn/quick-collect-azurevm.md) e incluindo a etapa "criar um espaço de trabalho".

Para começar a enviar dados de log para o Azure Monitor. Existem várias opções:

- Para um mecanismo síncrono, você pode ligar diretamente para a [API de coletor de dados](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) ou usar nosso conector Logic App – basta procurar "Azure Log Analytics" e escolher a opção "Enviar dados":

  ![Captura de tela de escolha e ação](./media/custom-data-correlation/01-logic-app-connector.png)  

- Para uma opção assíncrona, use a API do Coletor de Dados para criar um pipeline de processamento. Veja [este artigo](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) para mais detalhes.

## <a name="correlating-data"></a>Correlacionando dados

O Application Insights se baseia na plataforma de logs do Azure Monitor. Portanto, podemos usar [junções entre recursos](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) para correlacionar os dados ingeridos no Azure Monitor com nossos dados do Application Insights.

Por exemplo, podemos ingerir nosso inventário de laboratório e localizações em uma tabela chamada "LabLocations_CL" em um espaço de trabalho do Log Analytics chamado "myLA". Se, em seguida, quiséssemos rever nossas solicitações rastreadas no aplicativo Application Insights chamado "myAI" e correlacionar os nomes das máquinas que serviam as solicitações para os locais dessas máquinas armazenadas na tabela personalizada mencionada anteriormente, podemos executar a seguinte consulta de tanto o contexto do Application Insights quanto do Monitor do Azure:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Próximas etapas

- Veja a referência da [API do Coletor de Dados](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api).
- Para obter mais informações sobre [junções entre recursos](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).
