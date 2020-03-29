---
title: Instale recipientes de fala
titleSuffix: Azure Cognitive Services
description: Detalha as opções de configuração do gráfico de leme de fala para texto.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: 3f390affe7badb401277aa86d1867c763aa0ae3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "69971352"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Fala-para-Texto (subgráfico: gráficos/speechToText)

Para substituir o gráfico "guarda-chuva", adicione o prefixo `speechToText.` em qualquer parâmetro para torná-lo mais específico. Por exemplo, ele substituirá o parâmetro `speechToText.numberOfConcurrentRequest` correspondente, `numberOfConcurrentRequest`por exemplo, sobreposição .

|Parâmetro|Descrição|Padrão|
| -- | -- | -- |
| `enabled` | Se o serviço **de fala para texto** está habilitado. | `false` |
| `numberOfConcurrentRequest` | O número de solicitações simultâneas para o serviço **de fala-texto.** Este gráfico calcula automaticamente os recursos de CPU e memória, com base neste valor. | `2` |
| `optimizeForAudioFile`| Se o serviço precisa otimizar para entrada de áudio via arquivos de áudio. Se `true`, este gráfico irá alocar mais recursos de CPU para serviço. | `false` |
| `image.registry`| O registro de imagem de docker **de fala para texto.** | `containerpreview.azurecr.io` |
| `image.repository` | O repositório de imagem de dispor de **fala para texto.** | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | A tag de imagem de docker **de fala para texto.** | `latest` |
| `image.pullSecrets` | Os segredos de imagem para puxar a imagem de docker **de fala para texto.** | |
| `image.pullByHash`| Se a imagem do docker é puxada pelo hash. Se `true` `image.hash` for necessário. | `false` |
| `image.hash`| O hash de imagem de fala **para texto.** Só usado `image.pullByHash: true`quando .  | |
| `image.args.eula` (obrigatório) | Indica que aceitou a licença. O único valor válido é`accept` | |
| `image.args.billing` (obrigatório) | O valor uri do ponto final de faturamento está disponível na página Visão Geral de Fala do portal Azure. | |
| `image.args.apikey` (obrigatório) | Usado para rastrear informações de cobrança. ||
| `service.type` | O tipo de serviço Kubernetes do serviço **de fala para texto.** Consulte as instruções dos [tipos de serviço kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) para obter mais detalhes e verifique o suporte ao provedor de nuvem. | `LoadBalancer` |
| `service.port`|  A porta do serviço **de fala para texto.** | `80` |
| `service.annotations` | As **anotações de fala para texto** para os metadados do serviço. Anotações são pares de valores-chave. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Se o [Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) está habilitado. Se `true`, `speech-to-text-autoscaler` o será implantado no cluster Kubernetes. | `true` |
| `service.podDisruption.enabled` | Se o [Orçamento de Interrupção do Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) está habilitado. Se `true`, `speech-to-text-poddisruptionbudget` o será implantado no cluster Kubernetes. | `true` |