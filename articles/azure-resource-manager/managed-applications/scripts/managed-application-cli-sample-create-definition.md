---
title: Criar uma definição de aplicativo gerenciado – CLI do Azure
description: Fornece uma amostra de script da CLI do Azure que cria uma definição de aplicativo gerenciado na assinatura.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 63182eb382e96f47c1c90dc5d212e064d0945ba7
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86056036"
---
# <a name="create-a-managed-application-definition-with-azure-cli"></a>Criar uma definição de aplicativo gerenciado com a CLI do Azure

Este script publica uma definição de aplicativo gerenciado em um catálogo de serviços. 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-definition/create-definition.sh "Create definition")]


## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para implantar a definição de aplicativo gerenciado. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az managedapp definition create](/cli/azure/managedapp/definition#az-managedapp-definition-create) | Crie uma definição de aplicativo gerenciado. Forneça o pacote que contém os arquivos necessários. |


## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](../overview.md).
* Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).
