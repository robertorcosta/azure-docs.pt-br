---
title: Pull do Docker para o contêiner de integridade
titleSuffix: Azure Cognitive Services
description: Comando de pull do Docker para Análise de Texto para o contêiner de integridade
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 4c33952ed56399b94a7db1088cb60919a4f88137
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108887"
---
Preencha e envie o [formulário de solicitação de contêineres de serviços cognitivas](https://aka.ms/cognitivegate) para solicitar acesso ao contêiner.

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

Use o comando Docker login com credenciais fornecidas em seu email de integração para se conectar ao nosso registro de contêiner privado para contêineres de serviços cognitivas.

```bash
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar essa imagem de contêiner do nosso registro de contêiner privado.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
