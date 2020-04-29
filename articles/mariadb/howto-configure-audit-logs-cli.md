---
title: Acessar logs de auditoria-CLI do Azure-banco de dados do Azure para MariaDB
description: Este artigo descreve como configurar e acessar os logs de auditoria no banco de dados do Azure para MariaDB no CLI do Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: e9716f0fa8e0ae44d614bbb28ed6846105e683d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81384188"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Configurar e acessar os logs de auditoria no CLI do Azure

Você pode configurar os [logs de auditoria do banco de dados do Azure para MariaDB](concepts-audit-logs.md) no CLI do Azure.

> [!IMPORTANT]
> A funcionalidade de log de auditoria está atualmente em visualização.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções, você precisa:

- [Banco de dados do Azure para MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de instruções requer que você use a CLI do Azure versão 2.0 ou posterior. Para confirmar a versão, no prompt de comando da CLI do Azure, digite `az --version`. Para instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Configurar o log de auditoria

Habilite e configure o log de auditoria usando as seguintes etapas: 

1. Ative os logs de auditoria definindo o parâmetro **audit_logs_enabled** como "ativado". 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Selecione os [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) a serem registrados atualizando o parâmetro **audit_log_egitvents** .
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Adicione os usuários do MariaDB a serem excluídos do log atualizando o parâmetro **audit_log_exclude_users** . Especifique os usuários fornecendo seu nome de usuário MariaDB.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Adicione qualquer usuário MariaDB específico a ser incluído para registro em log atualizando o parâmetro **audit_log_include_users** . Especifique os usuários fornecendo seu nome de usuário MariaDB.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [os logs de auditoria](concepts-audit-logs.md) no banco de dados do Azure para MariaDB
- Saiba como configurar logs de auditoria no [portal do Azure](howto-configure-audit-logs-portal.md)