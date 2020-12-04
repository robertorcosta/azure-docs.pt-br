---
title: Azure Defender para SQL – Benefícios e recursos
description: Saiba mais sobre os benefícios e os recursos do Azure Defender para SQL.
author: memildin
ms.author: memildin
ms.date: 11/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 102d4916caf9dcae577006b6b847979f98ab3673
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499893"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Introdução ao Azure Defender para SQL

O Azure defender para SQL inclui dois planos do Azure Defender que estendem o [pacote de segurança de dados](../azure-sql/database/azure-defender-for-sql.md) da Central de Segurança do Azure para proteger os bancos de dados e os respectivos dados, onde quer que estejam localizados. 

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|**Azure Defender para Servidores de Banco de Dados SQL do Azure** – GA (Disponibilidade Geral)<br>**Azure Defender para SQL Servers em Computadores** – Versão Prévia<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Preço:|Os dois planos que formam o **Azure Defender para SQL** são cobrados conforme mostrado na [página de preços](security-center-pricing.md)|
|Versões do SQL protegidas:|SQL em máquinas virtuais do Azure – [Windows](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) e [Linux](../azure-sql/virtual-machines/linux/sql-server-on-linux-vm-what-is-iaas-overview.md)<br>[SQL Servers habilitados para o Arc](/sql/sql-server/azure-arc/overview) (inclui SQL Servers locais)<br>[Bancos de dados individuais](../azure-sql/database/single-database-overview.md) e [pools elásticos](../azure-sql/database/elastic-pool-overview.md) do SQL do Azure<br>[Instância Gerenciada do SQL do Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Pool de SQL dedicado do Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Gov dos EUA<br>![Não](./media/icons/no-icon.png) China Gov, outros Gov|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>O que o Azure Defender para SQL protege?

**O Azure Defender para SQL** inclui dois planos separados do Azure Defender:

- O **Azure Defender para Servidores de Banco de Dados SQL do Azure** protege:
  - [Banco de Dados SQL do Azure](../azure-sql/database/sql-database-paas-overview.md)
  - [Instância Gerenciada do SQL do Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Pool de SQL dedicado no Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- O **Azure Defender para SQL Servers em Computadores (versão prévia)** estende as proteções para seus SQL Servers nativos do Azure a fim de oferecer suporte total a ambientes híbridos e proteger os SQL Servers (todas as versões compatíveis) hospedados no Azure, outros ambientes de nuvem e até mesmo computadores locais


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Quais são os benefícios do Azure Defender para SQL?

Esses dois planos incluem a funcionalidade para identificar e atenuar possíveis vulnerabilidades de banco de dados e detectar atividades anormais que podem indicar ameaças aos seus banco de dados:

- [Avaliação de vulnerabilidade](../azure-sql/database/sql-vulnerability-assessment.md) – Serviço de verificação para descobrir, acompanhar e ajudar você a corrigir possíveis vulnerabilidades de banco de dados. As verificações de avaliação fornecem uma visão geral do estado de segurança dos computadores SQL e detalhes sobre eventuais descobertas de segurança.

- [Proteção avançada contra ameaças](../azure-sql/database/threat-detection-overview.md) – Serviço de detecção que monitora continuamente os SQL Servers em busca de ameaças como injeção de SQL, ataques de força bruta e abuso de privilégios. Esse serviço fornece alertas de segurança orientados a ações na Central de Segurança do Azure com detalhes sobre a atividade suspeita, orientação sobre como atenuar as ameaças e opções para continuar suas investigações com o Azure Sentinel.


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Quais tipos de alertas são fornecidos pelo Azure Defender para SQL?

Os alertas de segurança são disparados quando há:

- **Possíveis ataques de injeção de SQL** – Incluindo vulnerabilidades detectadas quando os aplicativos geram uma instrução SQL com falha no banco de dados
- **Padrões anômalos de consulta e acesso a banco de dados** – Por exemplo, um número estranhamente elevado de tentativas de entrada sem sucesso usando credenciais diferentes (uma tentativa de força bruta)
- **Atividade de banco de dados suspeita** – Por exemplo, uma alteração no destino de armazenamento de exportação para uma operação de importação e exportação do SQL

Um alerta inclui detalhes sobre o incidente que o disparou, bem como recomendações sobre como investigar e corrigir as ameaças.



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Azure Defender para SQL.

Para obter material relacionado, consulte os seguintes artigos: 

- [Como habilitar o Azure Defender para SQL Servers em Computadores](defender-for-sql-usage.md)
- [Como habilitar o Azure Defender para Servidores de Banco de Dados SQL](../azure-sql/database/azure-defender-for-sql.md)
- [A lista de alertas do Azure Defender para SQL](alerts-reference.md#alerts-sql-db-and-warehouse)
