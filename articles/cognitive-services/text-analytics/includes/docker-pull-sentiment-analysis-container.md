---
title: Pull do Docker para o contêiner de Análise de Sentimento
titleSuffix: Azure Cognitive Services
description: Comando de pull do Docker para Análise de Sentimento contêiner
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e502b886c8031174180d40f1c5b7e373a974ee8e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876942"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Pull do Docker para o contêiner de Análise de Sentimento

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem de contêiner do registro de contêiner da Microsoft.

Para obter uma descrição completa das marcas disponíveis para os contêineres de Análise de Texto, consulte o contêiner [análise de sentimento](https://go.microsoft.com/fwlink/?linkid=2018654) no Hub do Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
