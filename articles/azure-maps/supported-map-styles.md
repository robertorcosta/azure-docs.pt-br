---
title: Estilos de mapa com suporte | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre os diferentes estilos de renderização de mapa com suporte pelos mapas do Microsoft Azure.
author: philmea
ms.author: philmea
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 3db8ae98302ad6df7355ce03f5a33b5d21f1ea09
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86242093"
---
# <a name="azure-maps-supported-map-styles"></a>Categorias compatíveis com o Azure Mapas
O Azure Mapas dá suporte a vários estilos de mapa internos diferentes, como descrito abaixo.

## <a name="road"></a>rodoviário
Um **mapa** de estradas é um mapa padrão que exibe estradas, naturais e artificiais recursos juntamente com os rótulos para esses recursos.

![estilo de mapa de estrada](./media/supported-map-styles/road.png)

**APIs aplicáveis:**
* [Imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Bloco de mapa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controle de mapa do SDK da Web
* Controle de mapa do Android
* Visual do Power BI

## <a name="blank-and-blank_accessible"></a>em branco e blank_accessible

Os estilos de mapa **em branco** e de **blank_accessible** fornecem uma tela em branco na qual os dados são visualizados. O estilo de **blank_accessible** continuará a fornecer atualizações de leitor de tela com detalhes de local do mapa, mesmo que o mapa base não seja exibido.

> [!Note]
> No SDK da Web, você pode alterar a cor do plano de fundo do mapa definindo o `background-color` estilo CSS do elemento do mapa div.

**APIs aplicáveis:**
* Controle de mapa do SDK da Web

## <a name="satellite"></a>satellite 
O estilo **satélite** é uma combinação de imagens de satélite e aéreas.

![estilo de mapa de bloco satélite](./media/supported-map-styles/satellite.png)

**APIs aplicáveis:**
* [Peça de satélite](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Controle de mapa do SDK da Web
* Controle de mapa do Android
* Visual do Power BI

## <a name="satellite_road_labels"></a>satellite_road_labels
Este estilo de mapa é um híbrido de estradas e rótulos sobrepostos sobre imagens aéreas e de satélite.

![satellite_road_labels estilo de mapa](./media/supported-map-styles/satellite-road-labels.png)

**APIs aplicáveis:**
* Controle de mapa do SDK da Web
* Controle de mapa do Android
* Visual do Power BI

## <a name="grayscale_dark"></a>grayscale_dark
**Escala de cinza escuro** é uma versão escura do estilo de mapa rodoviário.

![gray_scale estilo de mapa](./media/supported-map-styles/grayscale-dark.png)

**APIs aplicáveis:**
* [Imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Bloco de mapa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controle de mapa do SDK da Web 
* Controle de mapa do Android
* Visual do Power BI


## <a name="grayscale_light"></a>grayscale_light
a **luz de escala de cinza** é uma versão leve do estilo de mapa rodoviário.

![estilo de mapa da luz em tons de cinza](./media/supported-map-styles/grayscale-light.png)

**APIs aplicáveis:**
* Controle de mapa do SDK da Web
* Controle de mapa do Android
* Visual do Power BI


## <a name="night"></a>noite
**noite** é uma versão escura do estilo de mapa rodoviário com estradas e símbolos coloridos.

![estilo de mapa noturno](./media/supported-map-styles/night.png)

**APIs aplicáveis:**
* Controle de mapa do SDK da Web
* Controle de mapa do Android
* Visual do Power BI

## <a name="road_shaded_relief"></a>road_shaded_relief
**road shaded relief** é o estilo principal do Azure Mapas concluído com as delimitações da Terra.

![estilo de mapa de alívio sombreado](./media/supported-map-styles/shaded-relief.png)

**APIs aplicáveis:**
* [Bloco de mapa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controle de mapa do SDK da Web
* Controle de mapa do Android
* Visual do Power BI

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** é um estilo de mapa escuro com um contraste maior do que os outros estilos.

![estilo de mapa escuro de alto contraste](./media/supported-map-styles/high-contrast-dark.png)

**APIs aplicáveis:**
* Controle de mapa do SDK da Web
* Visual do Power BI

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como definir um estilo de mapa no Azure Maps:

> [!div class="nextstepaction"]
> [Escolher um estilo de mapa](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
