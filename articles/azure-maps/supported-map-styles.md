---
title: Estilos de mapa suportados | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre diferentes estilos de renderização de mapas suportados pelo Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2eafe3c16a89723d55ec52fde785e9ec69e45e0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334031"
---
# <a name="azure-maps-supported-map-styles"></a>Categorias compatíveis com o Azure Mapas
O Azure Mapas dá suporte a vários estilos de mapa internos diferentes, como descrito abaixo.

## <a name="road"></a>rodoviário
Um **mapa** de estradas é um mapa padrão que exibe estradas, naturais e artificiais recursos juntamente com os rótulos para esses recursos.

![estilo mapa de estrada](./media/supported-map-styles/road.png)

**APIs aplicáveis:**
* [Imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Azulejo de mapa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controle do mapa Web SDK
* Controle de mapa android

## <a name="blank-and-blank_accessible"></a>em branco e blank_accessible

Os estilos de mapa **em branco** e **blank_accessible** fornecem uma tela em branco para visualizar dados. O estilo **blank_accessible** continuará a fornecer atualizações de leitores de tela com detalhes de localização do mapa, mesmo que o mapa base não seja exibido.

> [!Note]
> No Web SDK você pode alterar a cor de fundo `background-color` do mapa definindo o estilo CSS do elemento DIV do mapa.

**APIs aplicáveis:**
* Controle do mapa Web SDK

## <a name="satellite"></a>satélite 
O estilo **satélite** é uma combinação de imagens de satélite e aéreas.

![estilo mapa de telha por satélite](./media/supported-map-styles/satellite.png)

**APIs aplicáveis:**
* [Peça de satélite](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Controle do mapa Web SDK
* Controle de mapa android

## <a name="satellite_road_labels"></a>satellite_road_labels
Este estilo de mapa é um híbrido de estradas e rótulos sobrepostos sobre imagens aéreas e de satélite.

![satellite_road_labels estilo mapa](./media/supported-map-styles/satellite-road-labels.png)

**APIs aplicáveis:**
* Controle do mapa Web SDK
* Controle de mapa android

## <a name="grayscale_dark"></a>grayscale_dark
**Escala de cinza escuro** é uma versão escura do estilo de mapa rodoviário.

![gray_scale estilo do mapa](./media/supported-map-styles/grayscale-dark.png)

**APIs aplicáveis:**
* [Imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Azulejo de mapa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controle do mapa Web SDK 
* Controle de mapa android


## <a name="grayscale_light"></a>grayscale_light
**luz em escala de cinza** é uma versão leve do estilo do roteiro.

![estilo mapa de luz em cinza](./media/supported-map-styles/grayscale-light.png)

**APIs aplicáveis:**
* Controle do mapa Web SDK
* Controle de mapa android


## <a name="night"></a>noite
**noite** é uma versão escura do estilo de mapa rodoviário com estradas e símbolos coloridos.

![estilo mapa noturno](./media/supported-map-styles/night.png)

**APIs aplicáveis:**
* Controle do mapa Web SDK
* Controle de mapa android

## <a name="road_shaded_relief"></a>road_shaded_relief
**road shaded relief** é o estilo principal do Azure Mapas concluído com as delimitações da Terra.

![estilo mapa de alívio sombreado](./media/supported-map-styles/shaded-relief.png)

**APIs aplicáveis:**
* [Azulejo de mapa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controle do mapa Web SDK
* Controle de mapa android

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** é um estilo de mapa escuro com um contraste maior do que os outros estilos.

![alto contraste estilo mapa escuro](./media/supported-map-styles/high-contrast-dark.png)

**APIs aplicáveis:**
* Controle do mapa Web SDK

## <a name="next-steps"></a>Próximas etapas

Saiba como definir um estilo de mapa no Azure Maps:

> [!div class="nextstepaction"]
> [Escolher um estilo de mapa](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
