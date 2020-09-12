---
title: Suporte para mover recursos do SQL do Azure entre regiões com o Azure Resource mover.
description: Examine o suporte para mover recursos do SQL do Azure entre regiões com o Azure Resource mover.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 4925f6ffd2383c21f8ff9b0e3196d44fc15bb657
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652742"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>Suporte para mover recursos do SQL do Azure entre regiões do Azure

Este artigo resume o suporte e os pré-requisitos para mover os recursos do SQL Azure entre regiões do Azure com o Azure Resource mover.

## <a name="requirements"></a>Requisitos

Os requisitos são resumidos na tabela a seguir.

**Recurso** | **Com suporte/Sem suporte** | **Detalhes**
--- | --- | ---
**Hiperescala do Banco de Dados SQL do Azure** | Sem suporte | Não é possível mover bancos de dados na camada de serviço de hiperescala do SQL do Azure com o Resource mover.
**Redundância de zona** | Com suporte |  Opções de movimentação com suporte:<br/><br/> -Entre regiões que dão suporte à redundância de zona.<br/><br/> -Entre regiões que não dão suporte à redundância de zona.<br/><br/> -Entre uma região que dá suporte à redundância de zona para uma região que não dá suporte à redundância de zona.<br/><br/> -Entre uma região que não dá suporte à redundância de zona para uma região que dá suporte à redundância de zona. 
**Sincronização de dados** | Banco de dados de Hub/sincronização: sem suporte<br/><br/> Membro de sincronização: com suporte. | Se um membro de sincronização for movido, você precisará configurar a sincronização de dados para o novo banco de dado de destino.
**Replicação geográfica existente** | Com suporte | As réplicas geográficas existentes são remapeadas para o novo primário na região de destino.<br/><br/> A propagação deve ser inicializada após a movimentação. [Saiba mais](/azure/sql-database/sql-database-active-geo-replication-portal)
**Transparent Data Encryption (TDE) com Bring Your Own Key (BYOK)** | Com suporte | [Saiba mais](../key-vault/general/move-region.md) sobre como mover os cofres de chaves entre regiões.
**TDE com chave gerenciada por serviço** | Com suporte. |  [Saiba mais](../key-vault/general/move-region.md) sobre como mover os cofres de chaves entre regiões.
**Regras de mascaramento de dados dinâmicos** | Com suporte. | As regras são automaticamente copiadas para a região de destino como parte da movimentação. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started-portal).
**Segurança de Dados Avançada** | Não há suporte. | Solução alternativa: Configure no nível de SQL Server na região de destino. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).
**Regras de firewall** | Não há suporte. | Solução alternativa: Configure as regras de firewall para SQL Server na região de destino. As regras de firewall no nível de banco de dados são copiadas do servidor de origem para o servidor de destino. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-server-level-firewall-rule).
**Políticas de auditoria** | Não há suporte. | As políticas serão redefinidas para o padrão após a movimentação. [Saiba](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) como redefinir.
**Retenção de backup** | Com suporte. | As políticas de retenção de backup do banco de dados de origem são transportadas para o banco de dados de destino. [Saiba](/azure/sql-database/sql-database-long-term-backup-retention-configure) como modificar as configurações após a movimentação.
**Ajuste automático** | Não há suporte. | Solução alternativa: defina as configurações de ajuste automático após a movimentação. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-automatic-tuning-enable).
**Alertas de banco de dados** | Não há suporte. | Solução alternativa: Defina alertas após a movimentação. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal).
**Banco de dados de Stretch SQL Server do Azure** | Sem suporte | Não é possível mover os bancos de dados de Stretch do SQL Server com o Resource mover.
**Azure Synapse Analytics** | Sem suporte | Não é possível mover a Synapse Analytics (anteriormente Azure SQL Data Warehouse) com o Resource mover.
## <a name="next-steps"></a>Próximas etapas

Experimente os [recursos do SQL Azure](tutorial-move-region-sql.md) para outra região com o Resource mover.