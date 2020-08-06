---
title: Monitorando a WAN virtual do Azure usando o Azure Monitor insights
description: Saiba mais sobre como monitorar a WAN virtual usando o Azure Monitor insights
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: cherylmc
ms.openlocfilehash: e3316b4a2255652972a0b9deef813f894f993589
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836064"
---
# <a name="azure-monitor-insights-for-virtual-wan-preview"></a>Azure Monitor insights para WAN virtual (versão prévia)

[Azure monitor insights](../azure-monitor/insights/network-insights-overview.md) para WAN virtual oferece aos usuários e operadores a capacidade de exibir o estado e o status da WAN virtual, apresentada por meio de um mapa topológica autodescoberto. O estado e o status do recurso são sobrepostos no mapa para fornecer uma exibição de instantâneo da integridade geral da WAN virtual. A navegação de recursos é habilitada no mapa por meio de acesso de um clique às páginas de configuração de recurso do portal de WAN virtual.

As métricas de nível de recurso da WAN virtual são coletadas e apresentadas por meio de uma pasta de trabalho de métricas de WAN virtual predefinida que mostra as métricas em um WAN virtual, Hub, gateway e níveis de conexão. Este artigo explica as etapas para usar Azure Monitor informações para a WAN virtual para exibir sua topologia e métricas de WAN virtual em um único local.

> [!NOTE]
> A opção de menu insights no portal de WAN virtual está em processo de distribuição. Embora o menu insights para a WAN virtual esteja sendo distribuído, a topologia e a pasta de trabalho de métricas da WAN virtual podem ser acessadas diretamente usando o Azure montor para redes. Confira [Azure monitor insights](../azure-monitor/insights/network-insights-overview.md) para obter mais informações. 
>

## <a name="before-you-begin"></a>Antes de começar

As etapas neste artigo pressupõem que você já implantou uma WAN virtual com um ou mais hubs. Para criar uma nova WAN virtual e um novo hub, siga as etapas nos artigos a seguir:

* [Criação de uma WAN virtual](virtual-wan-site-to-site-portal.md#openvwan)
* [Criação de um hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>Exibir topologia VWAN

Na **Wan virtual de portal do Azure >**, no menu do **Monitor** à esquerda, selecione **insights (versão prévia)**. Isso abre o **modo de exibição de informações**, que exibe o mapa de dependência de WAN virtual e a mini-pasta de métricas de alto nível.

**Figura 1: menu do monitor-insights**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="figuras" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

No modo de exibição de **informações** , você pode exibir os recursos de WAN virtuais autodescobertos, como hubs, gateways, firewalls, conexões e VNets de spoke, NVAs de terceiros e branches em uma WAN virtual de ponta a ponta, como mostra a **Figura 2**.

O **estado do recurso** e o **status** são codificados por cor e sobrepostos nos ícones de recurso no mapa. As métricas de alto nível da WAN virtual, como capacidades de Hub e utilização de gateway, são exibidas à direita por meio de uma mini pasta de trabalho.

**Figura 2: exibição do insights**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="figuras" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Exibição de dependência

A exibição de **dependência** para WAN virtual ajuda a visualizar a exibição interconectada de todos os recursos de WAN virtual amplamente organizados em uma arquitetura de Hub e spoke.

**Figura 3: exibição de dependência VWAN**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="Mapa de dependências" lightbox="./media/azure-monitor-insights/dependency-map.png":::

O mapa da exibição de dependência exibe os seguintes recursos como um grafo conectado:

* Hubs de WAN virtual em várias regiões do Azure.
* Spoke VNets que estão diretamente conectados ao Hub.
* Sites de Branch VPN e ExpressRoute e usuários P2S que estão conectados a cada Hub por meio de suas respectivas conexões de ExpressRoute, S2S e P2S e gateways de rede virtual.
* Firewalls do Azure (incluindo proxies de firewall de terceiros) implantados em um Hub (Hub protegido).
* NVA de terceiros (dispositivos de rede virtual) que são implantados em um VNets de spoke.

O mapa de dependência também exibe VNets conectadas indiretamente (VNet que são emparelhadas com um VNets de spoke de WAN virtual).

O mapa de dependências permite uma fácil navegação para as definições de configuração de cada recurso. Por exemplo, você pode passar o mouse sobre o recurso de Hub para exibir a configuração básica de recursos, como região do Hub e prefixo do Hub. Clique com o botão direito do mouse para acessar a página de portal do Azure do recurso de Hub.

**Figura 4: navegar para informações específicas do recurso**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="informações do recurso":::

A barra de pesquisa e filtro na exibição de dependência fornece uma maneira fácil de Pesquisar no grafo. Filtros diferentes fornecem ajuda para restringir a pesquisa a um caminho e estado específicos.

**Figura 5: pesquisa e filtragem**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="barra de pesquisa e filtro" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Métricas detalhadas

Você pode selecionar **Exibir métricas detalhadas** para acessar a página de **métricas** detalhadas. A página métricas é um painel pré-configurado com guias separadas, fornecendo informações úteis sobre a capacidade, o desempenho e a utilização de recursos de WAN virtual no nível de WAN virtual, nível de Hub e conexões individuais.

**Figura 6: painel de métricas detalhado**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="métricas detalhadas" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as métricas em Azure Monitor, consulte [métricas em Azure monitor](../azure-monitor/platform/data-platform-metrics.md).
* Para obter uma descrição completa de todas as métricas de WAN virtual, consulte [métricas e logs de WAN virtual](logs-metrics.md).
