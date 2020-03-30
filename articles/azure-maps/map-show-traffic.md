---
title: Mostrar o tráfego em um mapa | Mapas do Microsoft Azure
description: Neste artigo você aprenderá como exibir dados de tráfego em um mapa usando o Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9bd5ae462013924a46a3da8400719e83abae3424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534807"
---
# <a name="show-traffic-on-the-map"></a>Mostrar tráfego no mapa

Existem dois tipos de dados de tráfego disponíveis no Azure Maps:

- Os dados de incidentes - consistem em dados baseados em pontos e linhas para coisas como construção, fechamento de estradas e acidentes.
- Dados de fluxo - fornece métricas sobre o fluxo de tráfego nas estradas. Muitas vezes, os dados de fluxo de tráfego são usados para colorir as estradas. As cores são baseadas no quanto o tráfego está diminuindo o fluxo, em relação ao limite de velocidade, ou outra métrica. Os dados de fluxo de tráfego no Azure Maps têm três métricas diferentes de medição:
    - `relative`- é relativo à velocidade de fluxo livre da estrada.
    - `absolute`- é a velocidade absoluta de todos os veículos na estrada.
    - `relative-delay`- exibe áreas mais lentas do que o atraso médio esperado.

O código a seguir mostra como exibir dados de tráfego no mapa.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Abaixo está a amostra completa de código de execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Mostrar tráfego em um mapa' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Mostrar tráfego em um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Opções de sobreposição de tráfego

A ferramenta a seguir permite alternar entre as diferentes configurações de sobreposição de tráfego para ver como a renderização muda. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opções de sobreposição de tráfego" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte as <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>opções de sobreposição de tráfego de</a> caneta pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Opções de tráfego](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Aprimore suas experiências de usuário:

> [!div class="nextstepaction"]
> [Mapeie a interação com eventos do mouse](map-events.md)

> [!div class="nextstepaction"]
> [Criando um mapa acessível](map-accessibility.md)

> [!div class="nextstepaction"]
> [Página de exemplo de código](https://aka.ms/AzureMapsSamples)
