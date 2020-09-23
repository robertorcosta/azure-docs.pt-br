---
title: Logs-hiperescala (Citus)-banco de dados do Azure para PostgreSQL
description: Como acessar logs de banco de dados do banco de dados do Azure para PostgreSQL-Citus (hiperescala)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 1dc7bc8e119de7c8fdcf09713286be2633457486
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90895863"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Logs no banco de dados do Azure para PostgreSQL-Citus (hiperescala)

Os logs do PostgreSQL estão disponíveis em todos os nós de um grupo de servidores de hiperescala (Citus). Você pode enviar logs para um servidor de armazenamento ou para um serviço de análise. Os logs podem ser usados para identificar, solucionar problemas e reparar erros de configuração e desempenho inferior.

## <a name="accessing-logs"></a>Acessando os logs

Para acessar os logs do PostgreSQL para um coordenador ou nó de trabalho de hiperescala (Citus), abra o nó no portal do Azure:

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="lista de nós":::

Para o nó selecionado, abra **configurações de diagnóstico**e clique em **+ Adicionar configuração de diagnóstico**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="Botão Adicionar configurações de diagnóstico":::

Escolha um nome para as novas configurações de diagnóstico e marque a caixa **PostgreSQLLogs** .  Escolha quais destinos devem receber os logs.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="Escolher logs do PostgreSQL":::

## <a name="next-steps"></a>Próximas etapas

- [Introdução às consultas do log Analytics](/azure/azure-monitor/log-query/get-started-portal)
- Saiba mais sobre os [hubs de eventos do Azure](/azure/event-hubs/event-hubs-about)
