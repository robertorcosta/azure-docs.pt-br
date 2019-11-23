---
title: Estilos de mapa compatíveis com o Azure Mapas | Microsoft Docs
description: Estilos de mapa compatíveis com o Azure Mapas
author: walsehgal
ms.author: v-musehg
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 5368aec04eb0a57654adf5b0d5914282a979dcfd
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325407"
---
# <a name="azure-maps-supported-map-styles"></a>Categorias compatíveis com o Azure Mapas
O Azure Mapas dá suporte a vários estilos de mapa internos diferentes, como descrito abaixo.

## <a name="road"></a>rodoviário
Um **mapa** de estradas é um mapa padrão que exibe estradas, naturais e artificiais recursos juntamente com os rótulos para esses recursos.

![rodoviário](./media/supported-map-styles/road.png)

**APIs aplicáveis:**
* [Imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Peça de mapa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK map control
* Android map control

## <a name="blank-and-blank_accessible"></a>blank and blank_accessible

The **blank** and **blank_accessible** map styles provide a blank canvas on which to visualize data on. The **blank_accessible** style will continue to provide screen reader updates with location details of where the map is located, even though the base map is not displayed.

> [!Note]
> In the web SDK you can change the background color of the map by setting the CSS `background-color` style of map DIV element.

**APIs aplicáveis:**
* Web SDK map control

## <a name="satellite"></a>satélite 
O estilo **satélite** é uma combinação de imagens de satélite e aéreas.

![satélite](./media/supported-map-styles/satellite.png)

**APIs aplicáveis:**
* [Peça de satélite](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK map control
* Android map control

## <a name="satellite_road_labels"></a>satellite_road_labels
Este estilo de mapa é um híbrido de estradas e rótulos sobrepostos sobre imagens aéreas e de satélite.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**APIs aplicáveis:**
* Web SDK map control
* Android map control

## <a name="grayscale_dark"></a>grayscale_dark
**Escala de cinza escuro** é uma versão escura do estilo de mapa rodoviário.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**APIs aplicáveis:**
* [Imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Peça de mapa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK map control 
* Android map control


## <a name="grayscale_light"></a>grayscale_light
**grayscale light** is a light version of the road map style.

![grayscale light](./media/supported-map-styles/grayscale_light.png)

**APIs aplicáveis:**
* Web SDK map control
* Android map control


## <a name="night"></a>noite
**noite** é uma versão escura do estilo de mapa rodoviário com estradas e símbolos coloridos.

![noite](./media/supported-map-styles/night.png)

**APIs aplicáveis:**
* Web SDK map control
* Android map control

## <a name="road_shaded_relief"></a>road_shaded_relief
**road shaded relief** é o estilo principal do Azure Mapas concluído com as delimitações da Terra.

![shaded relief](./media/supported-map-styles/shaded-relief.png)

**APIs aplicáveis:**
* [Peça de mapa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK map control
* Android map control


## <a name="next-steps"></a>Próximos passos

Learn about how to set a map style in Azure Maps:

> [!div class="nextstepaction"]
> [Escolher um estilo de mapa](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
