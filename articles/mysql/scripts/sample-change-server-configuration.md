---
title: Script da CLI do Azure – alterar as configurações do servidor (MySQL)
description: Este script de exemplo da CLI lista todas as configurações do servidor disponíveis e atualiza o valor de innodb_lock_wait_timeout.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: faeebc2b4c963b46a0cbb805269078dee562a3f4
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970637"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Listar e atualizar as configurações de um Banco de Dados do Azure para o servidor MySQL usando a CLI do Azure
Este script de exemplo da CLI lista todos os parâmetros de configuração disponíveis, bem como seus valores permitidos para o Banco de Dados do Azure para o servidor MySQL e define o *innodb_lock_wait_timeout* com um valor diferente do padrão.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se você optar por executar a CLI localmente, este artigo exigirá a CLI do Azure versão 2.0 ou posterior. Verifique a versão executando `az --version`. Confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli) para instalar ou atualizar sua versão da CLI do Azure. 

## <a name="sample-script"></a>Script de exemplo
Neste script de exemplo, edite as linhas destacadas para atualizar o nome de usuário administrador e a senha com os seus próprios.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele após executar o exemplo de script. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação sobre o script
Esse script usa os comandos descritos na tabela abaixo:

| **Comando** | **Observações** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Cria um servidor MySQL que hospeda os bancos de dados. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) | Liste as configurações de um Banco de Dados do Azure para o servidor MySQL. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) | Atualize a configuração de um Banco de Dados do Azure para o servidor MySQL. |
| [az mysql server configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) | Mostre a configuração de um Banco de Dados do Azure para o servidor MySQL. |
| [az group delete](/cli/azure/group#az-group-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
- Leia mais sobre a CLI do Azure: [Documentação da CLI do Azure](/cli/azure).
- Experimente scripts adicionais: [Exemplos da CLI do Azure para o Banco de Dados do Azure para MySQL](../sample-scripts-azure-cli.md)
- Para obter mais informações sobre parâmetros do servidor, consulte [Como configurar parâmetros do servidor no Banco de Dados do Azure para MySQL](../howto-server-parameters.md).
