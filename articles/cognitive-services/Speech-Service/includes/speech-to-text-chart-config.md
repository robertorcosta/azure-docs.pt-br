---
title: Instalar contêineres de fala
titleSuffix: Azure Cognitive Services
description: Detalha as opções de configuração do gráfico de Helm de fala para texto.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: trbye
ms.openlocfilehash: f7ca8fdaddab9757292939c4f7e658179d6e517c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422263"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Conversão de fala em texto (subgráfico: gráficos/speechToText)

Para substituir o gráfico "abrangência", adicione o prefixo `speechToText.` em qualquer parâmetro para torná-lo mais específico. Por exemplo, ele substituirá o parâmetro correspondente, por exemplo `speechToText.numberOfConcurrentRequest` , `numberOfConcurrentRequest`substitui.

|Parâmetro|Descrição|Padrão|
| -- | -- | -- |
| `enabled` | Se o serviço de **fala a texto** está habilitado. | `false` |
| `numberOfConcurrentRequest` | O número de solicitações simultâneas para o serviço de **conversão de fala em texto** . Este gráfico calcula automaticamente os recursos de CPU e memória, com base nesse valor. | `2` |
| `optimizeForAudioFile`| Se o serviço precisa otimizar para entrada de áudio por meio de arquivos de áudio. Se `true`, esse gráfico alocará mais recursos de CPU ao serviço. | `false` |
| `image.registry`| O registro de imagem do Docker de **fala para texto** . | `containerpreview.azurecr.io` |
| `image.repository` | O repositório de imagens do Docker de **fala para texto** . | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | A marca de imagem do Docker de **fala para texto** . | `latest` |
| `image.pullSecrets` | Os segredos da imagem para obter a imagem do Docker de **fala para texto** . | |
| `image.pullByHash`| Se a imagem do Docker é retirada por hash. Se `true`, `image.hash` for necessário. | `false` |
| `image.hash`| O hash de imagem do Docker de **fala para texto** . Usado somente quando `image.pullByHash: true`.  | |
| `image.args.eula` (obrigatório) | Indica que você aceitou a licença. O único valor válido é`accept` | |
| `image.args.billing` (obrigatório) | O valor do URI do ponto de extremidade de cobrança está disponível na página Visão geral de fala do portal do Azure. | |
| `image.args.apikey` (obrigatório) | Usado para rastrear informações de cobrança. ||
| `service.type` | O tipo de serviço kubernetes do serviço de **fala a texto** . Consulte as [instruções de tipos de serviço kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) para obter mais detalhes e verificar o suporte ao provedor de nuvem. | `LoadBalancer` |
| `service.port`|  A porta do serviço de **conversão de fala em texto** . | `80` |
| `service.annotations` | As anotações de **fala para texto** para os metadados de serviço. As anotações são pares chave-valor. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Se a [escala de aumento horizontal do pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) é habilitada. Se `true`, o `speech-to-text-autoscaler` será implantado no cluster kubernetes. | `true` |
| `service.podDisruption.enabled` | Se o [orçamento de interrupção do pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) está habilitado. Se `true`, o `speech-to-text-poddisruptionbudget` será implantado no cluster kubernetes. | `true` |