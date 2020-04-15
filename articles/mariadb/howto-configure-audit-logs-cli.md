---
title: Registros de auditoria de acesso - Azure CLI - Banco de dados Azure para MariaDB
description: Este artigo descreve como configurar e acessar os registros de auditoria no Banco de Dados Do Azure para MariaDB a partir do Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: e9716f0fa8e0ae44d614bbb28ed6846105e683d6
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384188"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Configure e acesse registros de auditoria na CLI do Azure

Você pode configurar o [banco de dados Azure para registros](concepts-audit-logs.md) de auditoria do MariaDB a partir do Azure CLI.

> [!IMPORTANT]
> A funcionalidade do registro de auditoria está atualmente em visualização.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções, você precisa:

- [Banco de dados do Azure para MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de instruções requer que você use a CLI do Azure versão 2.0 ou posterior. Para confirmar a versão, no prompt de comando da CLI do Azure, digite `az --version`. Para instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Configurar o registro de auditoria

Habilite e configure o registro de auditoria usando as seguintes etapas: 

1. Ative os registros de auditoria definindo o parâmetro **audit_logs_enabled** como "ON". 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Selecione os [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) a serem registrados atualizando o parâmetro **audit_log_egitvents.**
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Adicione quaisquer usuários do MariaDB a serem excluídos do registro atualizando o parâmetro **audit_log_exclude_users.** Especifique os usuários fornecendo seu nome de usuário MariaDB.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Adicione quaisquer usuários específicos do MariaDB a serem incluídos para registro atualizando o parâmetro **audit_log_include_users.** Especifique os usuários fornecendo seu nome de usuário MariaDB.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [registros de auditoria](concepts-audit-logs.md) no Banco de Dados Do Azure para MariaDB
- Saiba como configurar registros de auditoria no [portal Azure](howto-configure-audit-logs-portal.md)