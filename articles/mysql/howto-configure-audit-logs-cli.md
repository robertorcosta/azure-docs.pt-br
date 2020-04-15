---
title: Registros de auditoria de acesso - Azure CLI - Banco de dados Azure para MySQL
description: Este artigo descreve como configurar e acessar os logs de auditoria no Banco de Dados Azure para MySQL a partir do Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: d532e1990586d80d675a8ccb247c0c9f7908bb6f
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384162"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Configure e acesse registros de auditoria na CLI do Azure

Você pode configurar o [banco de dados Azure para registros de auditoria MySQL](concepts-audit-logs.md) a partir do Azure CLI.

> [!IMPORTANT]
> A funcionalidade do registro de auditoria está atualmente em visualização.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções, você precisa:

- [Banco de dados Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Este guia de instruções requer que você use a CLI do Azure versão 2.0 ou posterior. Para confirmar a versão, no prompt de comando da CLI do Azure, digite `az --version`. Para instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Configurar o registro de auditoria

Habilite e configure o registro de auditoria usando as seguintes etapas:

1. Ative os registros de auditoria definindo o parâmetro **audit_logs_enabled** como "ON". 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Selecione os [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) a serem registrados atualizando o parâmetro **audit_log_events.**
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Adicione quaisquer usuários Do MySQL a serem excluídos do registro atualizando o parâmetro **audit_log_exclude_users.** Especifique os usuários fornecendo seu nome de usuário MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Adicione quaisquer usuários MySQL específicos a serem incluídos para registro atualizando o parâmetro **audit_log_include_users.** Especifique os usuários fornecendo seu nome de usuário MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [registros de auditoria](concepts-audit-logs.md) no Banco de Dados Azure para MySQL
- Saiba como configurar registros de auditoria no [portal Azure](howto-configure-audit-logs-portal.md)