---
title: Azure Monitor para redes (visualização)
description: Uma visão geral rápida de Azure Monitor para rede que fornece uma visão abrangente da integridade e das métricas para todos os recursos de rede implantados sem nenhuma configuração.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/24/2020
ms.openlocfilehash: 5f076f477c36f96d1807ce7071720225a6df8e03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91803799"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure Monitor para redes (visualização)
O Azure Monitor para rede fornece uma visão abrangente da [integridade](https://docs.microsoft.com/azure/service-health/resource-health-checks-resource-types) e das [métricas](../platform/metrics-supported.md) para todos os recursos de rede implantados sem nenhuma configuração.  Ele também fornece acesso a todos os recursos de monitoramento de rede, como o [Monitor de conexão](../../network-watcher/connection-monitor-preview.md), o [log de fluxo para NSGs (grupos de segurança de rede)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md), [análise de tráfego](../../network-watcher/traffic-analytics.md)e outros recursos de [diagnóstico](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) de rede.

O Azure Monitor para redes é estruturado com base nos seguintes componentes principais do monitoramento:
- [Métricas e integridade da rede](#networkhealth)
- [Conectividade](#connectivity)
- [Tráfego](#traffic)
- [Kit de ferramentas de diagnóstico](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Métricas e integridade da rede

A página **visão geral** do Azure monitor for Networks fornece uma maneira fácil de visualizar o inventário de seus recursos de rede junto com os alertas e a integridade de recursos. Ele é dividido em quatro áreas funcionais-chave: pesquisa e filtragem, Resource Health e métricas, alertas. e exibição de dependência

![Página de visão geral](media/network-insights-overview/overview.png)

### <a name="search-and-filtering"></a>Pesquisa e filtragem
A exibição de integridade e alertas de recursos pode ser personalizada usando filtros como **assinatura**, **grupo de recursos** e **tipo de recurso**. A caixa de pesquisa fornece a capacidade de Pesquisar por propriedades de recurso.

A caixa de pesquisa pode ser usada para pesquisar recursos e recursos associados. Por exemplo, um IP público é associado a um gateway de aplicativo. Pesquisar o nome DNS do IPs público identificará o IP público e o gateway de aplicativo associado.

![Azure Monitor para redes – pesquisa](media/network-insights-overview/search.png)


### <a name="resource-health-and-metric"></a>Resource Health e métrica
Cada bloco representa um tipo de recurso, com o número de instâncias implantadas em todas as assinaturas selecionadas junto com o status de integridade do recurso. No exemplo a seguir, há 45 conexões de ER e VPN implantadas, 44 estão íntegras e 1 não disponível.

![Azure Monitor para redes – Resource Health](media/network-insights-overview/resource-health.png)

Clicar nas conexões de ER e VPN não disponíveis inicia uma exibição de métrica. 

![Azure Monitor para redes – exibição de métrica](media/network-insights-overview/metric-view.png)

Você pode clicar em cada elemento no modo de exibição de grade. Clique no ícone de integridade para redirecionar para o Resource Health para essa conexão. Clique em alertas para redirecionar para a página de alertas e métricas respectivamente para essa conexão. 

### <a name="alerts"></a>Alertas
A grade de **alertas** à direita fornece uma exibição de todos os alertas gerados para os recursos selecionados em todas as assinaturas. Clique no alerta contagens para navegar até a página alertas detalhados.

### <a name="dependency-view"></a>Exibição de dependência
A exibição de **dependência** ajuda a visualizar como o recurso é configurado. Atualmente, a exibição de dependência agora tem suporte para gateway de aplicativo, WAN virtual e Load Balancer. Por exemplo, no caso do gateway de aplicativo, a exibição de dependência pode ser acessada clicando no nome de recurso do gateway de aplicativo na exibição de grade de métricas. Isso também se aplica à WAN virtual e Load Balancer.

![Azure Monitor para redes – exibição do gateway de aplicativo](media/network-insights-overview/application-gateway.png)

A exibição de **dependência** do gateway de aplicativo fornece uma visão simplificada de como os IPs de front-end são conectados aos ouvintes, às regras e ao pool de back-end. As bordas de conexão são codificadas por cores e fornecem detalhes adicionais com base na integridade do pool de back-end. A exibição também fornece uma exibição detalhada das métricas e métricas do gateway de aplicativo para todos os pools de back-end relacionados, como o conjunto de dimensionamento de máquinas virtuais e as instâncias de VM.

![Azure Monitor para redes – exibição de dependência](media/network-insights-overview/dependency-view.png)

O grafo de dependência permite a navegação fácil para as definições de configuração. Clique com o botão direito do mouse em um pool de back-end para acessar outras funcionalidades. Por exemplo, se o pool de back-end for uma VM, você poderá acessar diretamente as informações da VM e a conexão do observador de rede para identificar problemas de conectividade.

![Azure Monitor para redes – menu de exibição de dependência](media/network-insights-overview/dependency-view-menu.png)

A barra de pesquisa e filtro na exibição de dependência fornece uma maneira fácil de Pesquisar no grafo. Por exemplo, a pesquisa de *AppGWTestRule* no exemplo abaixo restringirá a exibição gráfica a todos os nós conectados por meio de *AppGWTestRule*.

![Azure Monitor para redes – exemplo de pesquisa](media/network-insights-overview/search-example.png)

Filtros diferentes fornecem ajuda para restringir a um caminho e estado específicos. Por exemplo, selecione somente não *íntegro* no menu suspenso **status de integridade** para mostrar todas as bordas em que o estado não está *íntegro*.

Clique em **exibição de métrica detalhada** para iniciar uma pasta de trabalho pré-configurada com métricas detalhadas para o gateway de aplicativo, todos os recursos de pool de back-end e IPS de front-end. 

## <a name="connectivity"></a><a name="connectivity"></a>Conectividade

A guia **conectividade** fornece uma maneira fácil de Visualizar todos os testes configurados usando o monitor de conexão e o [Monitor de conexão (versão prévia)](../../network-watcher/connection-monitor-preview.md) para o conjunto selecionado de assinaturas.

![Guia conectividade em Azure Monitor para redes](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

Os testes são agrupados por blocos de origens e destinos e exibem o status de acessibilidade de cada teste. As configurações acessíveis fornecem um acesso fácil para configurar seus critérios de acessibilidade com base nas verificações com falha (%) e RTT (MS). Depois que os valores são definidos, o status de cada teste é atualizado com base nos critérios de seleção.

![Testes de conectividade em Azure Monitor para redes](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)

Clicar em qualquer bloco de origem ou de destino iniciará uma exibição de métrica.

![Métricas de conectividade em Azure Monitor para redes](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)


Você pode clicar em cada elemento no modo de exibição de grade. Clique no ícone de **acessibilidade** para redirecionar para a página do portal do **Monitor de conexão** para exibir a topologia de salto por salto e os problemas de impacto de conectividade identificados. Clique em **alertas** para redirecionar para alertas e **verificar a porcentagem de tempo de ida e volta com falha** para redirecionar para a página métricas do monitor de conexão selecionado.

A grade de **alertas**   à direita fornece uma exibição de todos os alertas gerados para os testes de conectividade configurados em todas as assinaturas. Clique no alerta contagens para navegar até a página alertas detalhados.

## <a name="traffic"></a><a name="traffic"></a>Tráfego
A guia tráfego fornece acesso a todos os NSGs configurados para [logs de fluxo NSG](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) e [análise de tráfego](../../network-watcher/traffic-analytics.md) para o conjunto selecionado de assinaturas e agrupadas por locais. A funcionalidade de pesquisa fornecida nesta guia permite identificar o NSGs configurado para o endereço IP pesquisado. Você pode pesquisar por qualquer endereço IP em seu ambiente e a exibição regional lado a lado exibirá todos os NSGs junto com o status de configuração de logs de fluxo NSG e análise de tráfego.

![Exibição de tráfego em Azure Monitor para redes](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)

Clicar em qualquer bloco de região inicia um modo de exibição de grade que fornece fácil de exibir e configurar logs e Análise de Tráfego de fluxo de NSG.  

![Exibição de região de tráfego em Azure Monitor para redes](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)

Você pode clicar em cada elemento no modo de exibição de grade. Clique em status de configuração para editar o log de fluxo NSG e a configuração de Análise de Tráfego. Clique em alertas para redirecionar para os alertas de tráfego configurados para o NSG selecionado. Da mesma forma, você pode navegar até a exibição de Análise de Tráfego clicando no espaço de trabalho.  

A grade de **alertas**   à direita fornece uma exibição de todos os análise de tráfego alertas baseados em espaço de trabalho em todas as assinaturas. Clique no alerta contagens para navegar até a página alertas detalhados.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Kit de ferramentas de diagnóstico
O Diagnostic Toolkit fornece acesso a todos os recursos de diagnóstico disponíveis para a solução de problemas da rede. Nesse menu suspenso, você pode acessar recursos como captura de [pacote](../../network-watcher/network-watcher-packet-capture-overview.md), solução de problemas de [VPN](../../network-watcher/network-watcher-troubleshoot-overview.md), [solução de problemas de conexão](../../network-watcher/network-watcher-connectivity-overview.md), [próximo salto](../../network-watcher/network-watcher-next-hop-overview.md) e [verificação de fluxo de IP](../../network-watcher/network-watcher-ip-flow-verify-overview.md).

![Guia kit de ferramentas de diagnóstico](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="troubleshooting"></a>Solução de problemas 

Para obter diretrizes gerais de solução de problemas, consulte o [artigo de solução de problemas](troubleshoot-workbooks.md)de informações baseadas na pasta de trabalho dedicada.

Esta seção o ajudará com o diagnóstico e a solução de problemas de alguns dos problemas comuns que você pode encontrar ao usar o Azure Monitor para redes. Use a lista abaixo para localizar as informações relevantes para o seu problema específico.

### <a name="resolving-performance-issues-or-failures"></a>Resolução de problemas ou falhas de desempenho

Para ajudar a solucionar problemas relacionados à rede que você identifica com Azure Monitor para redes, consulte a documentação de solução de problemas do recurso que está funcionando incorretamente. Os links de solução de problemas para serviços de alta utilização estão listados abaixo.
* Rede virtual (VNET)
* Gateway de Aplicativo
* Gateway de VPN
* ExpressRoute 
* Load Balancer 

### <a name="why-dont-i-see-the-resources-from-all-the-subscriptions-i-have-selected"></a>Por que não vejo os recursos de todas as assinaturas que selecionei

O insights de rede só pode mostrar recursos de 5 assinaturas de cada vez. 

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-network-insights-how-do-i-do-so"></a>Quero fazer alterações ou adicionar visualizações adicionais ao insights de rede, como fazer isso

Para fazer alterações, selecione o "Modo de edição" para modificar a pasta de trabalho e, em seguida, você pode salvar seu trabalho como uma nova pasta de trabalho vinculada a uma assinatura e um grupo de recursos designados.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Qual é o refinamento de tempo depois de fixarmos qualquer parte das pastas de trabalho

Utilizamos o intervalo de agregação "Automático" e, portanto, depende do intervalo de tempo selecionado.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Qual é o intervalo de tempo quando qualquer parte da pasta de trabalho é fixada

O intervalo de tempo dependerá das configurações do painel.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-network-insights"></a>E se eu quiser ver outros dados ou fazer minhas próprias visualizações? Como fazer alterações nas informações de rede

Você pode editar a pasta de trabalho que vê em qualquer painel lateral e exibição de métrica detalhada, por meio do uso do modo de edição e, em seguida, salvar seu trabalho como uma nova pasta de trabalho que terá todas as suas novas alterações.


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o monitoramento de rede em [o que é o observador de rede do Azure?](../../network-watcher/network-watcher-monitoring-overview.md).
- Conheça os cenários aos quais as pastas de trabalho foram projetadas para dar suporte, como criar relatórios, personalizar relatórios existentes e muito mais, examinando [Criar relatórios interativos com pastas de trabalho do Azure Monitor](../platform/workbooks-overview.md).
