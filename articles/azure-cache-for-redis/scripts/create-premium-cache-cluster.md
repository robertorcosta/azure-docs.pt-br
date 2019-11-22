---
title: Exemplo de script da CLI do Azure – criar Cache do Azure para Redis Premium com clustering
description: Amostra de script da CLI do Azure – Criar uma camada Premium do Cache do Azure para Redis com clustering
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: 1813bf7112969436012627147b94f656537029d5
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122495"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Criar um Cache do Azure para Redis Premium com clustering

Neste cenário, você aprenderá a criar uma camada Premium de 6 GB do Cache do Azure para Redis com clustering habilitado e dois fragmentos.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos para criar um grupo de recursos e uma camada Premium do Cache do Azure para Redis com clustering habilitado. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az redis create](https://docs.microsoft.com/cli/azure/redis) | Criar instância de Cache do Azure para Redis. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Exemplos adicionais de scripts da CLI do Cache Redis do Azure podem ser encontrados na [Documentação do Cache Redis do Azure](../cli-samples.md).
