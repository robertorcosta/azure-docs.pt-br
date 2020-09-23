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
ms.openlocfilehash: 95bcb4b424010f63ac1ee4eb02f9e4793647051a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905993"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Pull do Docker para o contêiner de Detecção de Idioma

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem de contêiner do registro de contêiner da Microsoft.

Para obter uma descrição completa das marcas disponíveis para os contêineres de Análise de Texto, consulte o contêiner [detecção de idioma](https://go.microsoft.com/fwlink/?linkid=2018759) no Hub do Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/language:latest
```
