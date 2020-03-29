---
title: Puxar docker para o recipiente de detecção de linguagem
titleSuffix: Azure Cognitive Services
description: Comando docker pull para contêiner de detecção de idiomas
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 23669a5440799ff071b3a30e3e41ab5d49f212df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966747"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Puxar docker para o recipiente de detecção de linguagem

Use [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) o comando para baixar uma imagem de contêiner do Microsoft Container Registry.

Para obter uma descrição completa das tags disponíveis para os contêineres do Text Analytics, consulte o contêiner [Detecção de idiomas](https://go.microsoft.com/fwlink/?linkid=2018759) no Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
