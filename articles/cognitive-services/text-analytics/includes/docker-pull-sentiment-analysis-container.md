---
title: Docker puxar para o recipiente de Análise de Sentimento
titleSuffix: Azure Cognitive Services
description: Comando docker pull para contêiner de análise de sentimento
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: df00d469052fa30c3f2aaa5afe1881ef74587f9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966780"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Docker puxar para o recipiente de Análise de Sentimento

Use [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) o comando para baixar uma imagem de contêiner do Microsoft Container Registry.

Para obter uma descrição completa das tags disponíveis para os contêineres text analytics, consulte o contêiner [Análise de Sentimento](https://go.microsoft.com/fwlink/?linkid=2018654) no Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
