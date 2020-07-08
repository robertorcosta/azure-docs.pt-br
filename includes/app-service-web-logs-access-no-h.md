---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: e6c4b07d01a4992e22107cb7d524646f439c37c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905860"
---
Para acessar os logs de console gerados de dentro do código do aplicativo no serviço de aplicativo, ative o log de diagnóstico executando o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

Os valores possíveis para `--level` são: `Error` , `Warning` , `Info` e `Verbose` . Cada nível subsequente inclui o nível anterior. Por exemplo: `Error` inclui apenas mensagens de erro e `Verbose` inclui todas as mensagens.

Depois que o log de diagnóstico for ativado, execute o seguinte comando para ver o fluxo de log:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Se você não vir os logs do console imediatamente, verifique novamente após 30 segundos.

> [!NOTE]
> Você também pode inspecionar os arquivos de log do navegador em `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Para interromper o streaming de log a qualquer momento, digite `Ctrl`+`C`.
