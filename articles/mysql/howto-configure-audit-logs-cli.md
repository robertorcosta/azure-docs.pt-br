---
title: Acessar logs de auditoria-CLI do Azure-banco de dados do Azure para MySQL
description: Este artigo descreve como configurar e acessar os logs de auditoria no banco de dados do Azure para MySQL no CLI do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 568f49565c6e6d8062f8869566cf3879b7c97eaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87503320"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Configurar e acessar os logs de auditoria no CLI do Azure

Você pode configurar os [logs de auditoria do banco de dados do Azure para MySQL](concepts-audit-logs.md) no CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções, você precisa:

- [Banco de dados do Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de instruções requer que você use a CLI do Azure versão 2.0 ou posterior. Para confirmar a versão, no prompt de comando da CLI do Azure, digite `az --version`. Para instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Configurar o log de auditoria

>[!IMPORTANT]
> É recomendável registrar apenas os tipos de eventos e os usuários necessários para fins de auditoria para garantir que o desempenho do servidor não seja muito afetado.

Habilite e configure o log de auditoria usando as seguintes etapas:

1. Ative os logs de auditoria definindo o parâmetro **audit_logs_enabled** como "ativado". 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Selecione os [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) a serem registrados atualizando o parâmetro **audit_log_events** .
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Adicione os usuários do MySQL a serem excluídos do log atualizando o parâmetro **audit_log_exclude_users** . Especifique os usuários fornecendo seu nome de usuário do MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Adicione qualquer usuário específico do MySQL a ser incluído para registro em log atualizando o parâmetro **audit_log_include_users** . Especifique os usuários fornecendo seu nome de usuário do MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [os logs de auditoria](concepts-audit-logs.md) no banco de dados do Azure para MySQL
- Saiba como configurar logs de auditoria no [portal do Azure](howto-configure-audit-logs-portal.md)
