---
title: Exibindo vários recursos no Metrics Explorer
description: Saiba como Visualizar vários recursos em Azure Monitor Metrics Explorer
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 724809dbce3ca1b5a36f4da0ba5c03d0f78897f5
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577600"
---
# <a name="viewing-multiple-resources-in-metrics-explorer"></a>Exibindo vários recursos no Metrics Explorer

O seletor de escopo de recurso permite exibir métricas em vários recursos que estão dentro da mesma assinatura e região. Veja abaixo as instruções sobre como exibir vários recursos no Metrics Explorer Azure Monitor. 

## <a name="selecting-a-resource"></a>Selecionando um recurso 

Selecione **Métricas** no menu do **Azure Monitor** ou na seção **Monitoramento** do menu de um recurso. Clique no botão "selecionar um escopo" para abrir o seletor de escopo de recurso, que permitirá selecionar os recursos para os quais você deseja ver as métricas. Isso já deve estar preenchido se você abrir o Metrics Explorer no menu de um recurso. 

![Captura de tela do seletor de escopo do recurso realçado em vermelho](./media/metrics-charts/019.png)

## <a name="selecting-multiple-resources"></a>Selecionando vários recursos 

Alguns tipos de recursos habilitaram a capacidade de consultar métricas em vários recursos, desde que estejam dentro da mesma assinatura e local. Esses tipos de recursos podem ser encontrados na parte superior da lista suspensa "tipos de recurso". 

![Captura de tela que mostra uma lista suspensa de recursos compatíveis com vários recursos ](./media/metrics-charts/020.png)

> [!WARNING] 
> Você deve ter a permissão de leitor de monitoramento no nível da assinatura para visualizar as métricas em vários recursos, grupos de recursos ou uma assinatura. Para fazer isso, siga as instruções neste [documento](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Para visualizar as métricas em vários recursos, comece selecionando vários recursos no seletor de escopo de recurso. 

![Captura de tela que mostra como selecionar vários recursos](./media/metrics-charts/021.png)

> [!NOTE]
> Você só pode selecionar vários recursos dentro do mesmo tipo de recurso, localização e assinatura. Os recursos fora desses critérios não serão selecionáveis. 

Quando terminar de selecionar, clique no botão "aplicar" para salvar sua seleção. 

## <a name="selecting-a-resource-group-or-subscription"></a>Selecionando um grupo de recursos ou uma assinatura 

> [!WARNING]
> Você deve ter a permissão de leitor de monitoramento no nível da assinatura para visualizar as métricas em vários recursos, grupos de recursos ou uma assinatura. 

Para tipos compatíveis com vários recursos, você também pode consultar métricas em uma assinatura ou em vários grupos de recursos. Comece selecionando uma assinatura ou um ou mais grupos de recursos: 

![Captura de tela que mostra como consultar em vários grupos de recursos ](./media/metrics-charts/022.png)

Em seguida, você precisará selecionar um tipo de recurso e um local antes de continuar aplicando seu novo escopo. 

![Captura de tela que mostra os grupos de recursos selecionados ](./media/metrics-charts/023.png)

Você também pode expandir os escopos selecionados para verificar quais recursos serão aplicados.

![Captura de tela que mostra os recursos selecionados dentro dos grupos ](./media/metrics-charts/024.png)

Depois de concluir a seleção dos escopos, clique em "aplicar" para salvar suas seleções. 

## <a name="splitting-and-filtering-by-resource-group-or-resources"></a>Divisão e filtragem por grupo de recursos ou recursos

Depois de plotar seus recursos, você pode usar a ferramenta de divisão e filtragem para obter mais informações sobre seus dados. 

A divisão permite visualizar como os diferentes segmentos da métrica se comparam entre si. Por exemplo, quando você estiver plotando uma métrica para vários recursos, poderá usar a ferramenta "aplicar divisão" para dividir por ID de recurso ou grupo de recursos. Isso permitirá que você compare facilmente uma única métrica entre vários recursos ou grupos de recursos.  

Por exemplo, abaixo está um gráfico da porcentagem de CPU em 9VMs. Dividindo por ID de recurso, você pode ver facilmente como a porcentagem de CPU difere por VM. 

![Captura de tela que mostra como você pode usar a divisão para ver a porcentagem de CPU por VM](./media/metrics-charts/026.png)

Além da divisão, você pode usar o recurso de filtragem para exibir apenas os grupos de recursos que deseja ver.  Por exemplo, se você quiser exibir a porcentagem de CPU para VMs para um determinado grupo de recursos, poderá usar a ferramenta "Adicionar filtro" para filtrar por grupo de recursos. Neste exemplo, filtramos por TailspinToys, que remove as métricas associadas aos recursos em TailspinToysDemo. 

![Captura de tela que mostra como você pode filtrar por grupo de recursos](./media/metrics-charts/027.png)

## <a name="pinning-your-multi-resource-charts"></a>Fixando seus gráficos de vários recursos 

> [!WARNING] 
> Você deve ter a permissão de leitor de monitoramento no nível da assinatura para visualizar as métricas em vários recursos, grupos de recursos ou uma assinatura. Para fazer isso, siga as instruções neste [documento](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). 

Para fixar o gráfico de vários recursos, siga as instruções [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts#create-alert-rules). 

## <a name="next-steps"></a>Próximas etapas

* [Solução de problemas do Metrics Explorer](metrics-troubleshoot.md)
* [Veja uma lista das métricas disponíveis para os serviços do Azure](metrics-supported.md)
* [Veja exemplos de gráficos configurados](metric-chart-samples.md)

