---
title: Dispositivos de monitoramento na solução de Monitoramento Remoto - Azure | Microsoft Docs
description: Neste tutorial você aprenderá a monitorar seus dispositivos de IoT usando o Acelerador de solução de Monitoramento Remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 400a71b11fde210b889d938041e88c5ebe73c1dc
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890866"
---
# <a name="tutorial-monitor-your-iot-devices"></a>Tutorial: Monitorar seus dispositivos de IoT

Neste tutorial, você usará o acelerador da solução de Monitoramento Remoto para monitorar seus dispositivos de IoT conectados. Você pode usar o painel da solução para exibir a telemetria, informações do dispositivo, alertas e KPIs.

O tutorial usa dois dispositivos de caminhão simulados que enviam a telemetria de localização, velocidade e temperatura da carga. Os caminhões são gerenciados por uma organização chamada Contoso e são conectados ao acelerador de solução de Monitoramento Remoto. Como um operador da Contoso, você precisará monitorar a localização e o comportamento de um dos seus caminhões (caminhão-02) em campo.

Neste tutorial, você irá:

>[!div class="checklist"]
> * Filtrar os dispositivos no painel
> * Exibir telemetria em tempo real
> * Exibir detalhes do dispositivo
> * Exibir alarmes de seus dispositivos
> * Exibir os KPIs do sistema

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Escolha os dispositivos a serem exibidos

Use filtros para selecionar quais dispositivos conectados deverão ser exibidos na página **Painel**. Para exibir apenas os dispositivos **Caminhão**, escolha o filtro **Caminhões** interno na lista suspensa de filtros:

[![Filtrar caminhões no painel](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

Quando você aplica um filtro, somente os dispositivos que correspondem às condições do filtro são exibidos no mapa e no painel de telemetria. Você pode ver que há dois caminhões conectados ao acelerador de soluções, incluindo o caminhão-02:

[![Somente os caminhões são exibidos no mapa](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

Para criar, editar e excluir filtros, clique em **Gerenciar grupos de dispositivos**.

## <a name="view-real-time-telemetry"></a>Exibir telemetria em tempo real

O acelerador de solução plota a telemetria em tempo real no gráfico na página **Painel**. O topo do gráfico de telemetria mostra os tipos de telemetria disponíveis para os dispositivos, incluindo o caminhão-02, selecionados pelo filtro atual. Por padrão, o gráfico está mostrando a latitude dos caminhões e o caminhão-02 parece estar parado:

[![Tipos de telemetria de caminhões](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

Para exibir a telemetria de temperatura para os caminhões, clique em **Temperatura**. Você verá como a temperatura do caminhão-02 variou ao longo última hora:

[![Gráfico de telemetria de temperatura de caminhões](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="view-the-map"></a>Exibir o mapa

O mapa exibe informações sobre os caminhões simuladas selecionados pelo filtro atual. É possível aplicar zoom e aplicar panorâmica no mapa para exibir os locais com mais ou menos detalhes. A cor de um ícone de dispositivo no mapa indica se existem **Alertas** (azul-escuro) ou **Avisos** (vermelho) ativos para o dispositivo. Um resumo do número de **Alertas** e **Avisos** é exibido à esquerda do mapa.

Para exibir os detalhes para o caminhão-02, aplique panorâmica e aplique zoom no mapa para localizá-lo e, em seguida, selecione o caminhão no mapa. Em seguida, clique no rótulo do dispositivo para abrir o painel **Detalhes do dispositivo**. Os detalhes do dispositivo incluem:

* Valores de telemetria recentes
* Métodos com suporte no dispositivo
* Propriedades do dispositivo

[![Exibir detalhes do dispositivo no painel](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>Exibir alertas

O painel **Alertas** exibe informações detalhadas sobre os alertas mais recentes dos seus dispositivos. Os alertas do caminhão-02 indicam uma temperatura de carga maior do que o normal:

[![Exibir alertas do dispositivo no painel](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

Você pode usar um filtro para ajustar o período de alertas recentes. Por padrão, o painel exibe os alertas da última hora.

## <a name="view-the-system-kpis"></a>Exibir os KPIs do sistema

A página **Painel** exibe os KPIs calculados pelo acelerador de solução no painel **Análise**:

[![KPIs do painel](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

O painel mostra três KPIs para os alertas selecionados pelos filtros de dispositivo e o período de tempo atuais:

* O número de alertas ativos para as regras que dispararam a maioria dos alertas.
* A proporção de alertas por tipo de dispositivo.
* O percentual de alertas que são alertas críticos.

Para o caminhão-02, todos os alertas são avisos de temperatura da carga acima do normal.

Os mesmos filtros que definem o período de tempo para alertas e controlam quais dispositivos são exibidos determinam como os KPIs são agregados. Por padrão, o painel exibe os KPIs agregados da última hora.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrou como usar a página **Painel** no acelerador de solução de Monitoramento Remoto para filtrar e monitorar os caminhões simulados. Para saber como usar o acelerador de solução para detectar problemas com seus dispositivos conectados, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Detectar problemas com dispositivos conectados à sua solução de monitoramento](iot-accelerators-remote-monitoring-automate.md)