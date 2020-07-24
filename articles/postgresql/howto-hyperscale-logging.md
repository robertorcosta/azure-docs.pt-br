---
title: Logs-hiperescala (Citus)-banco de dados do Azure para PostgreSQL
description: Como acessar logs de banco de dados do banco de dados do Azure para PostgreSQL-Citus (hiperescala)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 4ca64a7793d229e7910fb122fb33996b1ac0c4fe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095872"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Logs no banco de dados do Azure para PostgreSQL-Citus (hiperescala)

Os logs do PostgreSQL estão disponíveis em todos os nós de um grupo de servidores de hiperescala (Citus). Você pode enviar logs para um servidor de armazenamento ou para um serviço de análise. Os logs podem ser usados para identificar, solucionar problemas e reparar erros de configuração e desempenho inferior.

## <a name="accessing-logs"></a>Acessando os logs

Para acessar os logs do PostgreSQL para um coordenador ou nó de trabalho de hiperescala (Citus), abra o nó no portal do Azure:

![lista de nós](media/howto-hyperscale-logging/choose-node.png)

Para o nó selecionado, abra **configurações de diagnóstico**e clique em **+ Adicionar configuração de diagnóstico**.

![Botão Adicionar configurações de diagnóstico](media/howto-hyperscale-logging/diagnostic-settings.png)

Escolha um nome para as novas configurações de diagnóstico e marque a caixa **PostgreSQLLogs** .  Escolha quais destinos devem receber os logs.

![Escolher logs do PostgreSQL](media/howto-hyperscale-logging/diagnostic-create-setting.png)

## <a name="next-steps"></a>Próximas etapas

- [Introdução às consultas do log Analytics](/azure/azure-monitor/log-query/get-started-portal)
- Saiba mais sobre os [hubs de eventos do Azure](/azure/event-hubs/event-hubs-about)
