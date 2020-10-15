---
title: Script do CLI do Azure – Baixar logs de servidor no Banco de Dados do Azure para PostgreSQL
description: Esse exemplo de script de CLI do Azure mostra como habilitar e fazer o download dos logs do servidor de um servidor de Banco de Dados do Azure para PostgreSQL.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 6042b58dc4187898985abad29d8050ed2be58a13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710304"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Habilitar e fazer o download dos logs de consulta lenta de um servidor de Banco de Dados do Azure para PostgreSQL usando a CLI do Azure
Esse exemplo de script de CLI mostra como habilitar e fazer o download dos logs de consulta lenta de um único servidor de Banco de Dados do Azure para PostgreSQL.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se você optar por executar a CLI localmente, este artigo exigirá a CLI do Azure versão 2.0 ou posterior. Verifique a versão executando `az --version`. Confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli) para instalar ou atualizar sua versão da CLI do Azure.

## <a name="sample-script"></a>Exemplo de script
Neste script de exemplo, edite as linhas destacadas para atualizar o nome de usuário administrador e a senha com os seus próprios. Substitua &lt;log_file_name&gt; nos comandos `az monitor` pelo seu próprio nome de arquivo de log do servidor.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele após executar o exemplo de script. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação sobre o script
Esse script usa os comandos descritos na tabela abaixo:

| **Comando** | **Observações** |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az postgres server create](/cli/azure/postgres/server) | Cria um servidor PostgreSQL que hospeda os bancos de dados. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | Liste os valores de configuração para um servidor. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Atualize a configuração de um servidor. |
| [az postgres server-logs list](/cli/azure/postgres/server-logs) | Lista arquivos de log para um servidor. |
| [az postgres server-logs download](/cli/azure/postgres/server-logs) | Faça o download de arquivos de log. |
| [az group delete](/cli/azure/group) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
- Para ler mais sobre a CLI do Azure: [documentação da CLI do Azure](/cli/azure).
- Experimente scripts adicionais: [Exemplos da CLI do Azure para o Banco de Dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md)
- [Configurar e acessar os logs de servidor no Portal do Azure](../howto-configure-server-logs-in-portal.md)
