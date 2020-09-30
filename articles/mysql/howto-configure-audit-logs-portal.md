---
title: Acessar logs de auditoria-portal do Azure-banco de dados do Azure para MySQL
description: Este artigo descreve como configurar e acessar os logs de auditoria no banco de dados do Azure para MySQL no portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: c406fa6b49e800912edb5738b4d60596d828fc94
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570488"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Configurar e acessar os logs de auditoria do banco de dados do Azure para MySQL no portal do Azure

Você pode configurar os [logs de auditoria do banco de dados do Azure para MySQL](concepts-audit-logs.md) e as configurações de diagnóstico do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções, você precisa:

- [Banco de dados do Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Configurar o log de auditoria

>[!IMPORTANT]
> É recomendável registrar apenas os tipos de eventos e os usuários necessários para fins de auditoria para garantir que o desempenho do servidor não seja muito afetado.

Habilite e configure o log de auditoria.

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione seu servidor de Banco de Dados do Azure para MySQL.

1. Na seção **configurações** na barra lateral, selecione **parâmetros do servidor**.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/server-parameters.png" alt-text="Parâmetros do servidor":::

1. Atualize o parâmetro **audit_log_enabled** como ativado.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Parâmetros do servidor":::

1. Selecione os [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) a serem registrados atualizando o parâmetro **audit_log_events** .
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-events.png" alt-text="Parâmetros do servidor":::

1. Adicione os usuários do MySQL a serem incluídos ou excluídos do log atualizando os parâmetros **audit_log_exclude_users** e **audit_log_include_users** . Especifique os usuários fornecendo seu nome de usuário do MySQL.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Parâmetros do servidor":::

1. Depois que você alterar os parâmetros, clique em **Salvar**. Ou então, você pode **Descartar** suas alterações.
    :::image type="content" source="./media/howto-configure-audit-logs-portal/save-parameters.png" alt-text="Parâmetros do servidor":::

## <a name="set-up-diagnostic-logs"></a>Configuração dos logs de diagnóstico

1. Na seção **monitoramento** na barra lateral, selecione **configurações de diagnóstico**.

1. Clique em "+ adicionar configuração de diagnóstico" :::image type="content" source="./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Parâmetros do servidor" como o tipo de log.
:::image type="content" source="./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Parâmetros do servidor":::

1. Depois de configurar os coletores de dados para canalizar os logs de auditoria, você pode clicar em **salvar**.
:::image type="content" source="./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Parâmetros do servidor":::

1. Acesse os logs de auditoria explorando-os nos coletores de dados que você configurou. Pode levar até 10 minutos para que os logs sejam exibidos.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [os logs de auditoria](concepts-audit-logs.md) no banco de dados do Azure para MySQL
- Saiba como configurar logs de auditoria no [CLI do Azure](howto-configure-audit-logs-cli.md)