---
title: Realizar uma análise da causa raiz em um alerta – Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a conduzir uma análise da causa raiz em um alerta usando o Azure Time Series Insights.
author: Philmea
ms.author: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: c3cb940583677d813873c07fbfa679fdcc1dff59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77565461"
---
# <a name="tutorial-conduct-a-root-cause-analysis-on-an-alert"></a>Tutorial: realizar uma análise da causa raiz em um alerta

Neste tutorial, você aprenderá a usar o acelerador de solução de Monitoramento Remoto para diagnosticar a causa raiz de um alerta. Você verá que um alerta foi disparado no painel da solução de Monitoramento Remoto. Em seguida, use o explorador do Azure Time Series Insights para investigar a causa raiz.

O tutorial usa dois dispositivos de caminhão de entrega simulados que enviam a telemetria de localização, altitude, velocidade e temperatura da carga. Os caminhões são gerenciados por uma organização chamada Contoso e são conectados ao acelerador de solução de Monitoramento Remoto. Como operador da Contoso, você precisa entender por que um dos seus caminhões (caminhão de entrega 02) registrou um alerta de baixa temperatura.

Neste tutorial, você:

>[!div class="checklist"]
> * Filtrar os dispositivos no painel
> * Exibir telemetria em tempo real
> * Explorar os dados no explorador do Time Series Insights
> * Realizar uma análise da causa raiz
> * Criar uma regra com base em seus aprendizados

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Escolha os dispositivos a serem exibidos

Use filtros para selecionar quais dispositivos conectados deverão ser exibidos na página **Painel**. Para exibir apenas os dispositivos **Caminhão**, escolha o filtro **Caminhões** interno na lista suspensa de filtros:

[![Filtrar caminhões no painel](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-expanded.png#lightbox)

Quando você aplica um filtro, somente os dispositivos que correspondem às condições do filtro são exibidos no mapa e no painel de telemetria no **Painel**. Você pode ver que há dois caminhões conectados ao acelerador de soluções, incluindo o **caminhão-02**.

## <a name="view-real-time-telemetry"></a>Exibir telemetria em tempo real

O acelerador de solução plota a telemetria em tempo real no gráfico na página **Painel**. Por padrão, o gráfico está mostrando telemetria de altitude, que varia ao longo do tempo:

[![Gráfico de telemetria de altitude do caminhão](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-expanded.png#lightbox)

Para exibir a telemetria de temperatura dos caminhões, clique em **Temperatura** no **Painel de telemetria**. Você verá como a temperatura dos dois caminhões variou ao longo dos últimos 15 minutos. Você também verá que um alerta de baixa temperatura foi disparado para o caminhão de entrega 02 no painel de alertas.

[![Painel do RM com alerta de baixa temperatura](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-expanded.png#lightbox)

## <a name="explore-the-data"></a>Explorar os dados

Para identificar a causa do alarme de temperatura baixa, abra os dados telemétricos do caminhão de entrega no explorador do Time Series Insights. Clique em qualquer um dos links **Explorar no Time Series Insights** no painel:

[![Painel do RM com links TSI realçados](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-expanded.png#lightbox)

Quando o explorador for iniciado, você verá todos os seus dispositivos listados:

[![Exibição inicial do Explorador do TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-expanded.png#lightbox)

Filtre os dispositivos digitando **caminhão de entrega** na caixa de filtro e selecione **temperatura** como a **Medida** no painel à esquerda:

[![Temperatura do caminhão do Explorador do TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-expanded.png#lightbox)

Você vê o mesmo modo de exibição do painel de Monitoramento remoto. Além disso, agora é possível ampliar mais próximo ao período no qual o alerta foi disparado:

[![Zoom do Explorador do TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-expanded.png#lightbox)

Você também pode adicionar em outros fluxos de telemetria proveniente dos caminhões. Clique no botão **Adicionar** no canto superior esquerdo. Um novo painel é exibido:

[![Explorador do TSI com o novo painel](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-expanded.png#lightbox)

No novo painel, altere o nome do novo rótulo para **Dispositivos** para que ele corresponda ao anterior. Selecione **altitude** como a **Medida** e **iothub-connection-device-id** como o valor **Dividir por** para adicionar a telemetria de altitude à sua exibição:

[![Explorador do TSI com temperatura e altitude](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-expanded.png#lightbox)

## <a name="diagnose-the-alert"></a>Diagnosticar o alerta

Quando você examinar os fluxos na exibição atual, poderá ver que os perfis de altitude dos dois caminhões são diferentes. Além disso, a queda de temperatura no **caminhão de entrega 02** acontece quando o caminhão atinge uma alta altitude. Você está surpreso com a descoberta, porque os caminhões foram programados para usar a mesma rota.

Para confirmar a suspeita de que os caminhões fizeram caminhos diferentes, adicione outro painel ao painel lateral usando o botão **Adicionar**. No novo painel, altere o nome do novo rótulo para **Dispositivos** para que ele corresponda ao anterior. Selecione **longitude** como a **Medida** e **iothub-connection-device-id** como o valor **Dividir por** para adicionar a telemetria de longitude à sua exibição. Você pode ver que os caminhões fizeram percursos diferentes observando a diferença entre os fluxos de **longitude**:

[![Explorador do TSI com temperatura, altitude e longitude](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-expanded.png#lightbox)

## <a name="create-a-new-rule"></a>Criar uma nova regra

Embora as rotas de caminhão normalmente sejam otimizadas com antecedência, você percebe que padrões de tráfego, clima e outros eventos imprevisíveis podem causar atrasos e os motoristas precisam tomar decisões de última hora com base no melhor julgamento. No entanto, como a temperatura de seus ativos dentro do veículo é crítica, você deve criar uma regra adicional em sua solução de Monitoramento remoto. Essa regra é para garantir que você receberá um aviso caso a altitude média em um intervalo de 1 minuto fique acima de 350 pés:

[![Guia de regras de Monitoramento Remoto para definir regra de altitude](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-expanded.png#lightbox)

Para aprender a criar e editar regras, confira o tutorial anterior sobre [Detecção de problemas de dispositivo](iot-accelerators-remote-monitoring-automate.md).

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrou como usar o explorador do Time Series Insights com o acelerador de solução de Monitoramento Remoto para diagnosticar a causa raiz de um alerta. Para saber como usar o acelerador de solução para identificar e corrigir problemas com seus dispositivos conectados, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Usar alertas do dispositivo para identificar e corrigir problemas em dispositivos conectados à solução de monitoramento](iot-accelerators-remote-monitoring-maintain.md)
