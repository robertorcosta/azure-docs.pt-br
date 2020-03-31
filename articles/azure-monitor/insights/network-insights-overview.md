---
title: Monitor de Redes Do Azure (Visualização)
description: Uma visão geral rápida do Azure Monitor for Network, que fornece uma visão abrangente da saúde e das métricas para todos os recursos de rede implantados sem qualquer configuração.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 0f5b2fbd13cb9658e255fde727e115df748aaed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654861"
---
# <a name="azure-monitor-for-networks-preview"></a>Monitor de Redes Do Azure (Visualização)
O Azure Monitor for Network fornece uma visão abrangente da saúde e das métricas para todos os recursos de rede implantados sem qualquer configuração. O recurso avançado de pesquisa ajuda a identificar dependências de recursos, permitindo cenários como identificar recursos que hospedam seu site simplesmente procurando o nome do site hospedado.

A página Visão **Geral** do Monitor para Redes do Azure fornece uma maneira fácil de visualizar o inventário de seus recursos de rede, juntamente com a saúde de recursos e alertas. É dividido em quatro áreas funcionais principais:

- Pesquisa e filtragem
- Saúde e Métricas de Recursos
- Alertas 
- Exibição de dependência

![Página de visão geral](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>Pesquisa e filtragem
A visualização de recursos e alertas de saúde pode ser personalizada usando filtros como **Assinatura,** **Grupo de Recursos** e Tipo de **Recurso.** A caixa de pesquisa fornece o recurso para pesquisar através de propriedades de recursos.

A caixa de pesquisa pode ser usada para procurar recursos e recursos associados. Por exemplo, um IP público está associado a um Gateway de aplicativo. A busca pelo nome DNS de IPs Públicos identificará tanto o IP público quanto o Gateway de aplicativo associado.

![Search](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Saúde e Métrica de Recursos
Cada ladrilho representa um tipo de recurso, com o número de instâncias implantadas em todas as assinaturas selecionadas juntamente com o status de saúde dos recursos. No exemplo abaixo, há 105 conexões ER e VPN implantadas, 103 são saudáveis e 2 indisponíveis.

![Integridade de recursos](media/network-insights-overview/resource-health.png)

Clicando nas duas conexões ER e VPN indisponíveis, inicia uma visão métrica. 

![Visualização métrica](media/network-insights-overview/metric-view.png)

Você pode clicar em cada elemento na exibição da grade. Clique no ícone Saúde para redirecionar para a saúde dos recursos para essa conexão. Clique em Alertas para redirecionar para a página de alertas e métricas, respectivamente, para essa conexão. 

## <a name="alerts"></a>Alertas
A grade **Alertas** à direita fornece uma visão de todos os alertas gerados para os recursos selecionados em todas as assinaturas. Clique na contagem de alertas para navegar na página de alertas detalhados.

## <a name="dependency-view"></a>Exibição de dependência
A **exibição Dependência** ajuda a visualizar como o recurso está configurado. Atualmente, a exibição de dependência só é suportada para o Gateway de aplicativos. A exibição de dependência pode ser acessada clicando no nome do recurso Application Gateway a partir da exibição da grade de métricas.

![Exibição do Gateway de aplicativos](media/network-insights-overview/application-gateway.png)

A **exibição dependência** do Application Gateway fornece uma visão simplificada de como os IPs front-end estão conectados aos ouvintes, regras e pool de back-end. As bordas de conexão são codificadas por cores e fornecem detalhes adicionais com base na saúde da piscina de backend. A exibição também fornece uma visão detalhada das métricas e métricas do Application Gateway para todos os pools de backend relacionados, como as instâncias VMSS e VM.

![Exibição de dependência](media/network-insights-overview/dependency-view.png)

O gráfico de dependência permite uma navegação fácil para configurações de configuração. Clique com o botão direito do mouse em um pool de backend para acessar outras funcionalidades. Por exemplo, se o pool de backend for uma VM, você poderá acessar diretamente o VM Insights e a solução de problemas de conexão do Observador de Rede para identificar problemas de conectividade.

![Menu de exibição de dependência](media/network-insights-overview/dependency-view-menu.png)

A barra de pesquisa e filtro na exibição de dependência fornece uma maneira fácil de pesquisar através do gráfico. Por exemplo, a pesquisa por *AppGWTestRule* no exemplo abaixo reduzirá a exibição gráfica a todos os nós conectados via *AppGWTestRule*. 

![Exemplo de pesquisa](media/network-insights-overview/search-example.png)

Diferentes filtros fornecem ajuda para reduzir a um caminho e estado específicos. Por exemplo, selecione apenas *O Insalubre* do Estado de **Saúde** para mostrar todas as bordas onde o estado é *insalubre*.

Clique em **Visão Métrica Detalhada** para lançar uma carteira de trabalho pré-configurada com métricas detalhadas para o gateway do aplicativo, todos os recursos de pool backend e IPs front-end. 

## <a name="next-steps"></a>Próximas etapas 

- Saiba mais sobre o monitoramento de rede no [Azure Network Watcher?](/azure/network-watcher/network-watcher-monitoring-overview)
