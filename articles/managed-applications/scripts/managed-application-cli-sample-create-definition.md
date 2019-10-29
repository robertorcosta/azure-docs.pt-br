---
title: Criar uma definição de aplicativo gerenciado – CLI do Azure
description: Fornece uma amostra de script da CLI do Azure que cria uma definição de aplicativo gerenciado na assinatura.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: b5ac119aa2c4d79c046a6eeabe252801496caa1c
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528921"
---
# <a name="create-a-managed-application-definition-with-azure-cli"></a>Criar uma definição de aplicativo gerenciado com a CLI do Azure

Este script publica uma definição de aplicativo gerenciado em um catálogo de serviços. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/managed-applications/create-definition/create-definition.sh "Create definition")]


## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para implantar a definição de aplicativo gerenciado. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az managedapp definition create](https://docs.microsoft.com/cli/azure/managedapp/definition#az-managedapp-definition-create) | Crie uma definição de aplicativo gerenciado. Forneça o pacote que contém os arquivos necessários. |


## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](../overview.md).
* Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).
