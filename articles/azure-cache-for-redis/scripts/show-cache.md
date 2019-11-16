---
title: Exemplo de script da CLI do Azure – Obter detalhes de um Cache Redis do Azure
description: Exemplo de script da CLI do Azure – Obter detalhes de um Cache Redis do Azure
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: f3e6c6dab95722eebdc4a175379444ef5840cad1
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122474"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Obter detalhes de um Cache Redis do Azure

Nesse cenário, você aprende como recuperar os detalhes de uma instância de Cache Redis do Azure, incluindo seu status de provisionamento.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para recuperar detalhes de uma instância de Cache Redis do Azure. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [apresentação de redis az](https://docs.microsoft.com/cli/azure/redis) | Recuperar os detalhes de uma instância do Cache Redis do Azure. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Exemplos adicionais de scripts da CLI do Cache Redis do Azure podem ser encontrados na [Documentação do Cache Redis do Azure](../cli-samples.md).
