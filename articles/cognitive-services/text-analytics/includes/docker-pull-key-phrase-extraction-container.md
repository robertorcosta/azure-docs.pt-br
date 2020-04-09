---
title: Puxe docker para o recipiente de extração de frases-chave
titleSuffix: Azure Cognitive Services
description: Comando docker pull para contêiner de extração de frases-chave
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: db5d0cfecde32d3dd6afe181d67a6ce6c0826eda
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876948"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Puxe docker para o recipiente de extração de frases-chave

Use [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) o comando para baixar uma imagem de contêiner do Microsoft Container Registry.

Para obter uma descrição completa das tags disponíveis para os contêineres do Text Analytics, consulte o contêiner [Deextração de frases-chave](https://go.microsoft.com/fwlink/?linkid=2018757) no Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
