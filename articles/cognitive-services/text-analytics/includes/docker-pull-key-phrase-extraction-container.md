---
title: Puxe docker para o recipiente de extração de frases-chave
titleSuffix: Azure Cognitive Services
description: Comando docker pull para contêiner de extração de frases-chave
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 59313528e4beb343f9ac1103d3f9f2faae5dfc16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966666"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Puxe docker para o recipiente de extração de frases-chave

Use [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) o comando para baixar uma imagem de contêiner do Microsoft Container Registry.

Para obter uma descrição completa das tags disponíveis para os contêineres do Text Analytics, consulte o contêiner [Deextração de frases-chave](https://go.microsoft.com/fwlink/?linkid=2018757) no Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
