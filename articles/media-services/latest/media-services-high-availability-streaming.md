---
title: Streaming de alta disponibilidade dos serviços de mídia do Azure
description: Saiba como fazer failover para uma conta de serviços de mídia secundária se ocorrer uma interrupção ou falha de um datacenter regional.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78899224"
---
# <a name="media-services-high-availability-streaming"></a>Streaming de alta disponibilidade dos serviços de mídia

Atualmente, os serviços de mídia do Azure não fornecem failover instantâneo do serviço se houver uma interrupção regional no Datacenter ou falha do componente subjacente ou dos serviços dependentes. Este artigo fornece orientações sobre como criar streaming de região cruzada de vídeo por demanda.

## <a name="prerequisites"></a>Pré-requisitos

Examine [como criar um sistema de codificação entre regiões](media-services-high-availability-encoding.md)

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Como criar streaming de região cruzada de vídeo por demanda 

* O streaming de região cruzada de vídeo por demanda envolve a duplicação de [ativos](assets-concept.md), [políticas de chave de conteúdo](content-key-policy-concept.md) (se usadas), políticas de [streaming](streaming-policy-concept.md)e [localizadores de streaming](streaming-locators-concept.md). 
* Você precisará criar as políticas em ambas as regiões e mantê-las atualizadas. 
* Ao criar os localizadores de streaming, você desejará usar o mesmo valor de ID de localizador, o valor de ID de ContentKey e o valor de ContentKey.  
* Se você estiver codificando o conteúdo, é aconselhável codificar o conteúdo na região A e publicá-lo, em seguida, copiar o conteúdo codificado para a região B e publicá-lo usando os mesmos valores da região A.
* Você pode usar o Gerenciador de tráfego nos nomes de host para a origem e o serviço de distribuição de chaves (na configuração dos serviços de mídia, isso será semelhante a uma URL de servidor de chave personalizada).

## <a name="next-steps"></a>Próximas etapas

Fazer Check-out:

* [Tutorial: codificar um arquivo remoto com base na URL e transmitir o vídeo](stream-files-dotnet-quickstart.md)
* [exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
