---
title: Noções básicas sobre métricas do Azure Spring Cloud
description: Saiba como examinar métricas no Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d9c6f14b7ecfc2929dc48c11e0df1fe80303c8b1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038495"
---
# <a name="metrics-for-azure-spring-cloud"></a>Métricas do Azure Spring Cloud

O Azure Monitor Metrics Explorer é um componente do portal do Microsoft Azure que permite plotar gráficos, correlacionar tendências visualmente e investigar picos e quedas nas métricas. Use o Metrics Explorer para investigar a integridade e o uso dos recursos. No Azure Spring Cloud, oferecemos duas opções para exibir métricas: gráficos na página **Visão Geral do Aplicativo** e a página Métricas do nível de serviço.

## <a name="application-overview-page"></a>Página Visão Geral do Aplicativo

A página **Visão Geral do Aplicativo** de cada aplicativo apresenta um gráfico de métricas que permite que você realize uma rápida verificação de status do seu aplicativo.  Acesse a página de serviço do Azure Spring Cloud e selecione o **Painel do Aplicativo**; em seguida, selecione um aplicativo na lista.  

Fornecemos cinco gráficos com métricas atualizadas a cada minuto para o seguinte:

* **Erros de servidor HTTP**: contagem de erros para solicitações HTTP para seu aplicativo.
* **Entrada de dados**: bytes recebidos pelo seu aplicativo.
* **Saída de dados**: bytes enviados para seu aplicativo.
* **Solicitações**: solicitações recebidas pelo seu aplicativo.
* **Tempo médio de resposta**: tempo médio de resposta do seu aplicativo.

Você pode selecionar um intervalo de tempo para o gráfico entre 1 hora e até 7 dias.

## <a name="service-level-metric-queries"></a>Consultas de métricas de nível de serviço

O Azure Spring Cloud permite que você monitore uma variedade de métricas de aplicativo. Leia o [guia de introdução](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) ao Azure Monitor Metrics para saber mais sobre este serviço.

Para examinar os dados de métricas, você selecionará sua métrica, sua **Agregação** e seu intervalo de tempo.  Esses conceitos são explicados abaixo.

### <a name="aggregation"></a>Agregação 

O Azure sonda e atualiza métricas a cada minuto. O Azure oferece três maneiras de agregar dados para um período escolhido:

* **Total**: somar todas as métricas como a saída de destino.
* **Average**: usar o valor médio no período como a saída de destino.
* **Max/Min**: usar o valor Máx./Mín. no período como o destino de saída.

### <a name="time-range"></a>Intervalo de tempo

Selecione um intervalo de tempo padrão ou defina o seu próprio.

### <a name="modifying-the-granularity-of-your-metric-query"></a>Modificar a granularidade de sua consulta de métrica

Por padrão, o Azure agrega métricas para todos os aplicativos do serviço do Azure Spring Cloud. Para examinar métricas no nível do aplicativo ou da instância, use a função de filtro.  
Selecione **Adicionar filtro**, defina a propriedade como **Aplicativo** e selecione o aplicativo de destino que você deseja monitorar. Opcionalmente, use a opção **Aplicar divisão** para desenhar linhas separadas para cada aplicativo no gráfico.

>[!TIP]
> Você pode criar seus próprios gráficos na página de métricas e fixá-los em seu **Painel**. Comece nomeando seu gráfico.  Em seguida, selecione **Fixar no painel no canto superior direito**. Agora você pode verificar seu aplicativo no **Painel** do Portal.