---
title: Use a CLI para dimensionar as Unidades Reservadas de Mídia - Azure | Microsoft Docs
description: Este tópico mostra como usar a CLI para dimensionar processamento de mídia com os Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9f0a7425fc09d391828a748832f662f02c6022cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970777"
---
# <a name="scaling-media-processing"></a>Dimensionamento de processamento de mídia

Os Serviços de Mídia do Azure permitem dimensionar o processamento de mídia em sua conta, gerenciando Unidades Reservadas de Mídia (MRUs). MrUs determinam a velocidade com que suas tarefas de processamento de mídia são processadas. Você pode escolher entre os seguintes tipos de unidade reservada: **S1**, **S2** ou **S3**. Por exemplo, o mesmo trabalho de codificação é executado mais rapidamente quando você usa o tipo de unidade reservada **S2** em comparação ao tipo **S1**. 

Além de especificar o tipo de unidade reservada, você pode especificar o provisionamento da sua conta com unidades reservadas. O número de unidades reservadas provisionadas determina o número de tarefas de mídia que podem ser processadas simultaneamente em uma determinada conta. Por exemplo, se sua conta tiver cinco unidades reservadas, as cinco tarefas de mídia serão executadas simultaneamente enquanto houver tarefas a serem processadas. As tarefas restantes irão aguardar na fila e serão selecionadas para processamento sequencialmente quando uma tarefa em execução for concluída. Se uma conta não tiver nenhuma unidade reservada provisionada, as tarefas serão selecionadas sequencialmente. Nesse caso, o tempo de espera entre a conclusão de uma tarefa e o início da próxima dependerá da disponibilidade dos recursos do sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>Escolha entre tipos diferentes de unidade reservada

A tabela a seguir o ajudará a tomar uma decisão ao escolher entre diferentes velocidades de codificação. Ela também fornece alguns casos de parâmetro de comparação quanto a [um vídeo que você pode baixar](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) para executar seus próprios testes:

|Tipo de RU|Cenário|Exemplo de resultados para o [7 min 1080p vídeo](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Codificação de taxa de bits única. <br/>Arquivos com resoluções SD ou inferiores, não sensível ao tempo, de baixo custo.|A codificação para um único bitrate de resolução Mp4 arquivo usando "H264 Single Bitrate SD 16x9" leva cerca de 7 minutos.|
| **S2**|Codificação de taxa de bits única e de taxa de bits múltipla.<br/>Uso normal para codificação SD e HD.|A codificação com predefinido "H264 Single Bitrate 720p" leva cerca de 6 minutos.<br/><br/>A codificação com predefinido "H264 Multiple Bitrate 720p" leva cerca de 12 minutos.|
| **S3**|Codificação de taxa de bits única e de taxa de bits múltipla.<br/>Vídeos com resolução Full HD e 4K. Codificação urgente com retorno mais rápido.|A codificação com predefinido "H264 Single Bitrate 1080p" leva aproximadamente 3 minutos.<br/><br/>A codificação com a predefinição "H264 Taxas de Bits Múltiplas 1080p" leva aproximadamente oito minutos.|

## <a name="considerations"></a>Considerações

* Para os trabalhos de Análise de Vídeo e Análise de Áudio que são disparados pelos Serviços de Mídia v3 ou pelo Video Indexer, é altamente recomendável o tipo de unidade S3.
* Se estiver usando o pool compartilhado, ou seja, sem nenhuma unidade reservada, suas tarefas de codificação terão o mesmo desempenho do que com unidades reservadas S1. No entanto, não há nenhum limite superior para o tempo que as Tarefas podem gastar no estado na fila e, a qualquer momento específico, no máximo, apenas uma Tarefa será executada.

O resto do artigo mostra como usar o [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) para dimensionar mrus.

> [!NOTE]
> Para os trabalhos de análise de áudio e análise de vídeo acionados pelo Serviços de Mídia do Microsoft Azure v3 ou pelo Video Indexer, é altamente recomendável provisionar sua conta com 10 MRUs do S3. Se você precisar de mais de 10 MRUs do S3, abra um ticket de suporte usando o [Portal do Microsoft Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Pré-requisitos 

[Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Unidades com a CLI reservadas de mídia de escala

Execute o comando `mru`.

O comando [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) a seguir define as Unidades Reservadas de Mídia na conta "amsaccount" usando os parâmetros **count** e **type**.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Cobrança

Você é cobrado com base no número de minutos que as Unidades Reservadas de Mídia são provisionadas em sua conta. Isso ocorre independentemente de haver algum Emprego em execução em sua conta. Para obter uma explicação detalhada, consulte a seção Perguntas frequentes da página [Preços dos Serviços de Mídia](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="next-step"></a>Próxima etapa

[Analisar vídeos](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Confira também

* [Cotas e limitações](limits-quotas-constraints.md)
* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
