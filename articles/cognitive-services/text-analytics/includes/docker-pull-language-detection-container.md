---
title: Pull do Docker para o contêiner de Detecção de Idioma
titleSuffix: Azure Cognitive Services
description: Comando de pull do Docker para Detecção de Idioma contêiner
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 3444d6267c09056697abf8339cc688a606b6dc17
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876946"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Pull do Docker para o contêiner de Detecção de Idioma

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem de contêiner do registro de contêiner da Microsoft.

Para obter uma descrição completa das marcas disponíveis para os contêineres de Análise de Texto, consulte o contêiner [detecção de idioma](https://go.microsoft.com/fwlink/?linkid=2018759) no Hub do Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
