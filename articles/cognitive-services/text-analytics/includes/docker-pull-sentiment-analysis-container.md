---
title: Docker puxar para o recipiente de Análise de Sentimento
titleSuffix: Azure Cognitive Services
description: Comando docker pull para contêiner de análise de sentimento
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e502b886c8031174180d40f1c5b7e373a974ee8e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876942"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Docker puxar para o recipiente de Análise de Sentimento

Use [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) o comando para baixar uma imagem de contêiner do Microsoft Container Registry.

Para obter uma descrição completa das tags disponíveis para os contêineres text analytics, consulte o contêiner [Análise de Sentimento](https://go.microsoft.com/fwlink/?linkid=2018654) no Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
