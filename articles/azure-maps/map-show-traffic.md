---
title: Mostrar o tráfego em um mapa | Microsoft Azure Mapas
description: Descubra como adicionar dados de tráfego a mapas. Saiba mais sobre os dados de fluxo e veja como usar o SDK da Web do Azure Maps para adicionar dados de incidentes e dados de fluxo a mapas.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 584db064bf6f6a6b99e6e2d09f6046912cfcd1bf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335238"
---
# <a name="show-traffic-on-the-map"></a>Mostrar tráfego no mapa

Há dois tipos de dados de tráfego disponíveis no Azure Mapas:

- Dados de incidentes – consistem em dados baseados em ponto e em linha para coisas como obras, fechamentos de estrada e acidentes.
- Dados de fluxo – fornecem métricas sobre o fluxo do tráfego nas estradas. Os dados de fluxo de tráfego são frequentemente usados para colorir as estradas. As cores são baseadas no quanto o tráfego está diminuindo o fluxo, em relação ao limite de velocidade ou a outra métrica. Os dados de fluxo de tráfego no Azure Mapas têm três métricas diferentes de medida:
    - `relative` – é relativo à velocidade da estrada com fluxo livre.
    - `absolute` – é a velocidade absoluta de todos os veículos na estrada.
    - `relative-delay` – exibe áreas em que o trânsito está mais lento do que o atraso médio esperado.

O código a seguir mostra como exibir dados de tráfego no mapa.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Veja abaixo o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Mostrar tráfego em um mapa' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Mostrar tráfego em um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Opções de sobreposição de tráfego

A ferramenta a seguir permite alternar entre as diferentes configurações de sobreposição de tráfego para ver como a renderização é alterada. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opções de sobreposição de tráfego" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Confira as <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>Opções de sobreposição de tráfego</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="add-traffic-controls"></a>Adicionar controles de tráfego

Há dois controles de tráfego diferentes que podem ser adicionados ao mapa. O primeiro controle, `TrafficControl` , adiciona um botão de alternância que pode ser usado para ativar e desativar o tráfego. As opções para esse controle permitem especificar quando as configurações de tráfego são usadas quando mostram o tráfego. Por padrão, esse controle exibirá o fluxo de tráfego relativo e os dados do incidente. no entanto, você pode alterar isso para mostrar o fluxo de tráfego absoluto e nenhum incidente, se desejado. O segundo controle, `TrafficLegendControl` , adiciona uma legenda de fluxo de tráfego ao mapa que ajuda o usuário a entender o que significam os destaques do código de cor. Esse controle só aparecerá no mapa quando os dados de fluxo de tráfego forem exibidos no mapa e serão ocultados em todos os outros momentos.

O código a seguir mostra como adicionar os controles de tráfego ao mapa.

```JavaScript
//Att the traffic control toogle button to the top right corner of the map.
map.controls.add(new atlas.control.TrafficControl(), { position: 'top-right' });

//Att the traffic legend control to the bottom left corner of the map.
map.controls.add(new atlas.control.TrafficLegendControl(), { position: 'bottom-left' });
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Controles de tráfego" src="https://codepen.io/azuremaps/embed/ZEWaeLJ?height500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte os <a href='https://codepen.io/azuremaps/pen/ZEWaeLJ'>controles de tráfego</a> da caneta por mapas do Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Aprimore suas experiências de usuário:

> [!div class="nextstepaction"]
> [Mapeie a interação com eventos do mouse](map-events.md)

> [!div class="nextstepaction"]
> [Criando um mapa acessível](map-accessibility.md)

> [!div class="nextstepaction"]
> [Página de exemplo de código](https://aka.ms/AzureMapsSamples)
