---
title: Script da CLI do Azure – Alterar as configurações do servidor (PostgreSQL)
description: Este script de exemplo da CLI lista todas as configurações do servidor disponíveis e atualiza o valor de uma das opções.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: d2b54d1173b9591de2482f4b3368d3dde8b8c766
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947791"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Listar e atualizar as configurações de um servidor do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure
Este script de exemplo da CLI lista todos os parâmetros de configuração disponíveis, bem como seus valores permitidos para o servidor do Banco de Dados do Azure para PostgreSQL e define o *log_retention_days* como um valor diferente do padrão.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se você optar por executar a CLI localmente, este artigo exigirá a CLI do Azure versão 2.0 ou posterior. Verifique a versão executando `az --version`. Confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli) para instalar ou atualizar sua versão da CLI do Azure. 

## <a name="sample-script"></a>Script de exemplo
Neste script de exemplo, edite as linhas destacadas para atualizar o nome de usuário administrador e a senha com os seus próprios.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele após executar o exemplo de script. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação sobre o script
Esse script usa os comandos descritos na tabela abaixo:

| **Comando** | **Observações** |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az postgres server create](/cli/azure/postgres/server) | Cria um servidor PostgreSQL que hospeda os bancos de dados. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | Liste as configurações de um servidor do Banco de Dados do Azure para PostgreSQL. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Atualize as configurações de um servidor do Banco de Dados do Azure para PostgreSQL. |
| [az postgres server configuration show](/cli/azure/postgres/server/configuration) | Mostre as configurações de um servidor do Banco de Dados do Azure para PostgreSQL. |
| [az group delete](/cli/azure/group) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
- Leia mais sobre a CLI do Azure: [Documentação da CLI do Azure](/cli/azure).
- Experimente scripts adicionais: [Exemplos da CLI do Azure para o Banco de Dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md)
- Para saber mais sobre parâmetros do servidor, veja [Como configurar parâmetros do servidor no portal do Azure](../howto-configure-server-parameters-using-portal.md).
