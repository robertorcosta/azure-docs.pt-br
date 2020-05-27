---
title: Exemplo de CLI – criar uma instância gerenciada no Banco de Dados SQL do Azure
description: Script de exemplo da CLI do Azure para criar uma instância gerenciada no Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: f52a460be33b23da3da0cb32075ab5f75bb8d129
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772617"
---
# <a name="use-cli-to-create-an-azure-sql-database-managed-instance"></a>Usar a CLI para criar uma Instância Gerenciada do Banco de Dados SQL do Azure

Este exemplo de script da CLI do Azure cria uma instância gerenciada do Banco de Dados SQL do Azure em uma sub-rede dedicada dentro de uma nova rede virtual. Ele também configura uma tabela de rotas e um grupo de segurança de rede para a rede virtual. Depois que o script tiver sido executado com êxito, a instância gerenciada poderá ser acessada de dentro da rede virtual ou de um ambiente local. Veja [Configurar a VM do Azure para se conectar a uma Instância Gerenciada do Banco de Dados SQL do Azure](../sql-database-managed-instance-configure-vm.md) e [Configurar uma conexão ponto a site para uma Instância Gerenciada do Banco de Dados SQL do Azure de local](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Para limitações, consulte [regiões com suporte](../sql-database-managed-instance-resource-limits.md#supported-regions) e [tipos de assinatura compatíveis](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemplo de script

### <a name="sign-in-to-azure"></a>Entrar no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Limpar a implantação

Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exemplo de referência

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| | |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | Comandos de rede virtual. |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | Comandos de sub-rede de rede virtual. |
| [az network route-table](/cli/azure/network/route-table) | Comandos da tabela de rotas de rede. |
| [az sql mi](/cli/azure/sql/mi) | Comandos de instância gerenciada. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Os exemplos de script da CLI do Banco de Dados SQL adicionais podem ser encontrados na [documentação do Banco de Dados SQL do Azure](../sql-database-cli-samples.md).
