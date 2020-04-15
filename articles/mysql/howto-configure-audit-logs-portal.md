---
title: Registros de auditoria de acesso - Portal Azure - Banco de Dados Azure para MySQL
description: Este artigo descreve como configurar e acessar os registros de auditoria no Banco de Dados Azure para MySQL a partir do portal Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: fcd436f87b24d8cdc246d2f1d33a49311e72d46b
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380296"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>Configure e acesse registros de auditoria para o Banco de Dados Azure para MySQL no portal Azure

Você pode configurar o [banco de dados Azure para registros de auditoria MySQL](concepts-audit-logs.md) e configurações de diagnóstico a partir do portal Azure.

> [!IMPORTANT]
> A funcionalidade do registro de auditoria está atualmente em visualização.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções, você precisa:

- [Banco de dados Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Configurar o registro de auditoria

Habilite e configure o registro de auditoria.

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione seu servidor de Banco de Dados do Azure para MySQL.

1. Na seção **Configurações** na barra lateral, selecione **parâmetros do servidor**.
    ![Parâmetros do servidor](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Atualize o parâmetro **audit_log_enabled** para ON.
    ![Habilitar registros de auditoria](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Selecione os [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) a serem registrados atualizando o parâmetro **audit_log_events.**
    ![Eventos de registro de auditoria](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Adicione quaisquer usuários Do MySQL a serem excluídos do registro atualizando o parâmetro **audit_log_exclude_users.** Especifique os usuários fornecendo seu nome de usuário MySQL.
    ![O registro de auditoria exclui usuários](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Depois que você alterar os parâmetros, clique em **Salvar**. Ou então, você pode **Descartar** suas alterações.
    ![Salvar](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Configuração dos logs de diagnóstico

1. Na seção **Monitoramento** na barra lateral, **selecione Configurações de Diagnóstico**.

1. Clique em "+ Adicionar ![configuração de diagnóstico" Adicionar configuração de diagnóstico](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Forneça um nome de definição de diagnóstico.

1. Especifique quais dados afundam para enviar os logs de auditoria (conta de armazenamento, hub de eventos e/ou espaço de trabalho do Log Analytics).

1. Selecione "MySqlAuditLogs" como o tipo de log.
![Configurar a configuração de diagnóstico](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Depois de configurar os dissipadores de dados para canalizar os registros de auditoria, você pode clicar **em Salvar**.
![Salvar configuração de diagnóstico](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Acesse os registros de auditoria explorando-os nos dissipadores de dados configurados. Pode levar até 10 minutos para que os registros apareçam.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [registros de auditoria](concepts-audit-logs.md) no Banco de Dados Azure para MySQL
- Saiba como configurar registros de auditoria na CLI do [Azure](howto-configure-audit-logs-cli.md)