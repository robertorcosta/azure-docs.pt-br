---
title: Instale recipientes de fala
titleSuffix: Azure Cognitive Services
description: Detalha as opções de configuração do gráfico de leme texto-para-fala.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 22168974ab8b285413b4fa6e947c05f65a73ae12
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874323"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Texto para fala (subgráfico: gráficos/textToSpeech)

Para substituir o gráfico "guarda-chuva", adicione o prefixo `textToSpeech.` em qualquer parâmetro para torná-lo mais específico. Por exemplo, ele substituirá o parâmetro `textToSpeech.numberOfConcurrentRequest` correspondente, `numberOfConcurrentRequest`por exemplo, sobreposição .

|Parâmetro|Descrição|Padrão|
| -- | -- | -- |
| `enabled` | Se o **serviço texto-para-fala** está habilitado. | `false` |
| `numberOfConcurrentRequest` | O número de solicitações simultâneas para o serviço **de texto para fala.** Este gráfico calcula automaticamente os recursos de CPU e memória, com base neste valor. | `2` |
| `optimizeForTurboMode`| Se o serviço precisa otimizar para entrada de texto através de arquivos de texto. Se `true`, este gráfico irá alocar mais recursos de CPU para serviço. | `false` |
| `image.registry`| O registro de imagem do docker **texto-para-fala.** | `containerpreview.azurecr.io` |
| `image.repository` | O repositório de imagem de docker **texto-para-fala.** | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | A tag de imagem de docker **texto-para-fala.** | `latest` |
| `image.pullSecrets` | Os segredos de imagem para puxar a imagem de docker **texto-para-fala.** | |
| `image.pullByHash`| Se a imagem do docker é puxada pelo hash. Se `true` `image.hash` for necessário. | `false` |
| `image.hash`| O hash de imagem de texto **para fala.** Só usado `image.pullByHash: true`quando .  | |
| `image.args.eula` (obrigatório) | Indica que aceitou a licença. O único valor válido é`accept` | |
| `image.args.billing` (obrigatório) | O valor uri do ponto final de faturamento está disponível na página Visão Geral de Fala do portal Azure. | |
| `image.args.apikey` (obrigatório) | Usado para rastrear informações de cobrança. ||
| `service.type` | O tipo de serviço Kubernetes do serviço **texto-para-fala.** Consulte as instruções dos [tipos de serviço kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) para obter mais detalhes e verifique o suporte ao provedor de nuvem. | `LoadBalancer` |
| `service.port`|  A porta do serviço **texto-para-fala.** | `80` |
| `service.annotations` | As **anotações texto-a-fala** para os metadados de serviço. Anotações são pares de valores-chave. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Se o [Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) está habilitado. Se `true`, `text-to-speech-autoscaler` o será implantado no cluster Kubernetes. | `true` |
| `service.podDisruption.enabled` | Se o [Orçamento de Interrupção do Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) está habilitado. Se `true`, `text-to-speech-poddisruptionbudget` o será implantado no cluster Kubernetes. | `true` |