---
title: Limites e cotas – Serviço de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Este artigo explica os diferentes tipos de chaves de licenciamento e sobre os limites e cotas para o Serviço de Visão Personalizada.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 5f481ebf219eea8ae31e9802144bbf771a24e8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79081770"
---
# <a name="limits-and-quotas"></a>Limites e cotas

Existem dois níveis de chaves para o serviço Visão Personalizada. Você pode se inscrever para uma assinatura F0 (gratuita) ou S0 (padrão) por meio do portal do Azure. Consulte a [página de preços de serviços cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) correspondente para obter detalhes sobre preços e transações.

Espera-se que o número de imagens de treinamento por projeto e tags por projeto aumente com o tempo para projetos S0.

||**F0**|**S0**|
|-----|-----|-----|
|Projetos|2|100|
|Imagens de treinamento por projeto |5.000|100.000|
|Previsões / mês|10.000 |Ilimitado|
|Tags / projeto|50|500|
|Iterações |10|10|
|Imagens rotuladas min por Tag, Classificação (50+ recomendados) |5|5|
|Imagens rotuladas min por Tag, Detecção de Objeto (50+ recomendados)|15|15|
|Por quanto tempo as imagens de previsão são armazenadas|30 dias|30 dias|
|Operações de [Previsão](https://go.microsoft.com/fwlink/?linkid=865445) com armazenamento (Transações por Segundo)|2|10|
|Operações de [Previsão](https://go.microsoft.com/fwlink/?linkid=865445) sem armazenamento (Transações por Segundo)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (chamadas à API por Segundo)|2|10|
|[Outras chamadas à API](https://go.microsoft.com/fwlink/?linkid=865446) (Transações por Segundo)|10|10|
|Tipos de imagem aceitos|jpg, png, bmp, gif|jpg, png, bmp, gif|
|Altura/largura da imagem min em pixels|256 (ver nota)|256 (ver nota)|
|Altura/largura máxima da imagem em pixels|ilimitado|ilimitado|
|Tamanho máximo da imagem (upload da imagem de treinamento) |6 MB|6 MB|
|Tamanho máximo da imagem (previsão)|4 MB|4 MB|
|Regiões máximas por imagem de treinamento de detecção de objeto|300|300|
|Marcas máximas por imagem de classificação|100|100|

> [!NOTE]
> Serão aceitas imagens menores que 256 pixels, mas em escala.
