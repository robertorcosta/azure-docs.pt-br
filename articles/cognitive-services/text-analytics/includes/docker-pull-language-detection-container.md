---
title: Puxar docker para o recipiente de detecção de linguagem
titleSuffix: Azure Cognitive Services
description: Comando docker pull para contêiner de detecção de idiomas
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 3444d6267c09056697abf8339cc688a606b6dc17
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876946"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Puxar docker para o recipiente de detecção de linguagem

Use [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) o comando para baixar uma imagem de contêiner do Microsoft Container Registry.

Para obter uma descrição completa das tags disponíveis para os contêineres do Text Analytics, consulte o contêiner [Detecção de idiomas](https://go.microsoft.com/fwlink/?linkid=2018759) no Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
