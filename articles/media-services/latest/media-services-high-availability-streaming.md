---
title: Azure Media Services streaming de alta disponibilidade
description: Saiba como falhar em uma conta secundária dos Serviços de Mídia se ocorrer uma paralisação ou falha no data center regional.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 1492dd392eabc4331f8e3d4604fb245a89dedff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899224"
---
# <a name="media-services-high-availability-streaming"></a>Streaming de alta disponibilidade dos Serviços de Mídia

Atualmente, o Azure Media Services não fornece failover instantâneo do serviço se houver uma paralisação regional do data center ou falha de componentes subjacentes ou serviços dependentes. Este artigo dá orientações sobre como construir streaming de região transversal de vídeo demanda.

## <a name="prerequisites"></a>Pré-requisitos

Revisão [Como construir um sistema de codificação inter-regional](media-services-high-availability-encoding.md)

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Como construir streaming de região transversal de vídeo demanda 

* O streaming de região transversal de vídeo demanda envolve a duplicação de [ativos,](assets-concept.md) [políticas de chave de conteúdo](content-key-policy-concept.md) (se usado), políticas de [streaming](streaming-policy-concept.md)e [localizadores de streaming.](streaming-locators-concept.md) 
* Você terá que criar as políticas em ambas as regiões e mantê-las atualizadas. 
* Quando você criar os localizadores de streaming, você vai querer usar o mesmo valor de ID do Localizador, o valor de ID do ContentKey e o valor do ContentKey.  
* Se você estiver codificando o conteúdo, é aconselhável codificar o conteúdo na região A e publicá-lo, em seguida, copie o conteúdo codificado para a região B e publique-o usando os mesmos valores da região A.
* Você pode usar o Traffic Manager nos nomes de host para a origem e no serviço de entrega chave (na configuração de Serviços de Mídia, isso parecerá uma URL de servidor de chave personalizada).

## <a name="next-steps"></a>Próximas etapas

Fazer Check-out:

* [Tutorial: Encodifique um arquivo remoto baseado em URL e transmita o vídeo](stream-files-dotnet-quickstart.md)
* [amostras de código](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
