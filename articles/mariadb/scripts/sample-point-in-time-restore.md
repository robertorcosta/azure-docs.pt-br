---
title: Script da CLI – Restaurar servidor – Banco de Dados do Azure para MariaDB
description: Esse script de exemplo de CLI do Azure mostra como restaurar um servidor do Banco de Dados do Azure para MariaDB e seus bancos de dados a um ponto anterior.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 839aae2ccf9433dafec0ddee692c476611924aa0
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497385"
---
# <a name="restore-an-azure-database-for-mariadb-server-using-azure-cli"></a>Restaurar um servidor do Banco de Dados do Azure para MariaDB usando a CLI do Azure
Esse script de exemplo do CLI restaura um servidor do Banco de Dados do Azure para MariaDB individual a um ponto anterior.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se você optar por executar a CLI localmente, este artigo exigirá a CLI do Azure versão 2.0 ou posterior. Verifique a versão executando `az --version`. Confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli) para instalar ou atualizar sua versão da CLI do Azure. 

## <a name="sample-script"></a>Exemplo de script
Neste script de exemplo, edite as linhas destacadas para atualizar o nome de usuário administrador e a senha com os seus próprios. Substitua a ID de assinatura usada nos comandos `az monitor` pela sua ID de assinatura.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/backup-restore-pitr/backup-restore.sh?highlight=15-16 "Restore Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele após executar o exemplo de script. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/backup-restore-pitr/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação sobre o script
Esse script usa os comandos descritos na tabela abaixo:

| **Comando** | **Observações** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Cria um servidor MariaDB que hospeda os bancos de dados. |
| [az mariadb server restore](/cli/azure/mariadb/server#az-mariadb-server-restore) | Restaura um servidor de backup. |
| [az group delete](/cli/azure/group#az-group-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
- Para ler mais sobre a CLI do Azure: [documentação da CLI do Azure](/cli/azure).
- Experimente scripts adicionais: [Amostras da CLI do Azure para o Banco de Dados do Azure para MariaDB](../sample-scripts-azure-cli.md)
