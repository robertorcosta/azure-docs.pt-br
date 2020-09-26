---
title: Configurar logs de auditoria-portal do Azure-banco de dados do Azure para MySQL-servidor flexível
description: Este artigo descreve como configurar e acessar os logs de auditoria no banco de dados do Azure para MySQL servidor flexível do portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: b8fe32a079358fda48c6f5ee0c7eec9894a543a5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91295899"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Configurar e acessar os logs de auditoria do banco de dados do Azure para MySQL-servidor flexível usando o portal do Azure

> [!IMPORTANT]
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

Você pode configurar os [logs de auditoria](concepts-audit-logs.md) do banco de dados do Azure para MySQL e as configurações de diagnóstico do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
As etapas neste artigo exigem que você tenha um [servidor flexível](quickstart-create-server-portal.md).

## <a name="configure-audit-logging"></a>Configurar o log de auditoria

>[!IMPORTANT]
> É recomendável registrar apenas os tipos de eventos e os usuários necessários para fins de auditoria para garantir que o desempenho do servidor não seja muito afetado.

Habilite e configure o log de auditoria.

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione seu servidor flexível.

1. Na seção **configurações** na barra lateral, selecione **parâmetros do servidor**.
    <!--:::image type="content" source="./media/howto-configure-audit-logs-portal/server-parameters.png" alt-text="Server parameters":::-->

1. Atualize o parâmetro **audit_log_enabled** como ativado.
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Enable audit logs":::-->

1. Selecione os [tipos de eventos](concepts-audit-logs.md#configure-audit-logging) a serem registrados atualizando o parâmetro **audit_log_events** .
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-events.png" alt-text="Audit log events":::-->

1. Adicione os usuários do MySQL a serem excluídos do log atualizando o parâmetro **audit_log_exclude_users** . Especifique os usuários fornecendo seu nome de usuário do MySQL.
    <!--:::image type="content" source="./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Audit log exclude users":::-->

1. Depois que você alterar os parâmetros, clique em **Salvar**. Ou então, você pode **Descartar** suas alterações.
    <!--:::image type="content" source="./media/howto-configure-audit-logs-portal/save-parameters.png" alt-text="Save":::-->

## <a name="set-up-diagnostics"></a>Configurar o diagnóstico

> [!NOTE]
> A integração com as configurações de diagnóstico Azure Monitor para acessar logs está no processo de implantação e a funcionalidade completa estará disponível em breve.

Os logs de auditoria são integrados às configurações de diagnóstico Azure Monitor para permitir que você direcione seus logs para Azure Monitor logs, hubs de eventos ou armazenamento do Azure.

1. Na seção **monitoramento** na barra lateral, selecione **configurações de diagnóstico**.

1. Clique em "+ adicionar configuração de diagnóstico"  <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Add diagnostic setting":::-->

1. Forneça um nome de configuração de diagnóstico.

1. Especifique quais destinos enviar os logs de auditoria (conta de armazenamento, Hub de eventos e/ou espaço de trabalho Log Analytics).

1. Selecione **MySqlAuditLogs** como o tipo de log.
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Configure diagnostic setting"::: -->

1. Depois de configurar os coletores de dados para canalizar os logs de auditoria, você pode clicar em **salvar**.
    <!-- :::image type="content" source="./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Save diagnostic setting":::-->

1. Acesse os logs de auditoria explorando-os nos coletores de dados que você configurou. Pode levar até 10 minutos para que os logs sejam exibidos.

Se você canalizasse seus logs de auditoria para Azure Monitor logs (Log Analytics), consulte algumas [consultas de exemplo](concepts-audit-logs.md#analyze-logs-in-azure-monitor-logs) que você pode usar para análise.  

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [os logs de auditoria](concepts-audit-logs.md)
- Saiba mais sobre [logs de consulta lentos](concepts-slow-query-logs.md)
<!-- - Learn how to configure audit logs in the [Azure CLI](howto-configure-audit-logs-cli.md)-->