---
title: Exemplo da CLI de auditoria e Proteção Avançada contra Ameaças – Banco de Dados SQL do Azure
description: Script de exemplo da CLI do Azure para configurar a auditoria e a Proteção Avançada contra Ameaças em um Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: security
ms.devlang: azurecli
ms.topic: sample
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 6791691be976e541c6400e89dde8f892fe50c6dc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336166"
---
# <a name="use-cli-to-configure-sql-database-auditing-and-advanced-threat-protection"></a>Usar a CLI para configurar a auditoria e a Proteção Avançada contra Ameaças do Banco de Dados SQL

Este script de exemplo da CLI do Azure configura a auditoria e a Proteção Avançada contra Ameaças do Banco de Dados SQL.

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemplo de script

### <a name="sign-in-to-azure"></a>Entrar no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.sh "Configure auditing and threat detection")]

### <a name="clean-up-deployment"></a>Limpar a implantação

Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exemplo de referência

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Descrição |
|---|---|
| [az sql db audit-policy](/cli/azure/sql/db/audit-policy) | Define a política de auditoria para um banco de dados. |
| [az sql db threat-policy](/cli/azure/sql/db/threat-policy) | Define uma política de Proteção Avançada contra Ameaças em um banco de dados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Os exemplos de script da CLI do Banco de Dados SQL adicionais podem ser encontrados na [documentação do Banco de Dados SQL do Azure](../../azure-sql/database/az-cli-script-samples-content-guide.md).
