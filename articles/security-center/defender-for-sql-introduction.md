---
title: Azure defender para SQL-os benefícios e recursos
description: Saiba mais sobre os benefícios e recursos do Azure defender para SQL.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: dcdbc3efba53f78890816721b6659aa69553d6ea
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301594"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Introdução ao Azure defender para SQL

O Azure defender para SQL inclui dois planos do Azure defender que estendem o [pacote de segurança de dados](../azure-sql/database/advanced-data-security.md) da central de segurança do Azure para proteger seus bancos de dados e os respectivos locais onde estiverem localizados. 

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|**Azure defender para servidores de banco de dados SQL do Azure** – disponibilidade geral (GA)<br>**Azure defender para SQL Servers em computadores** – versão prévia|
|Refere|Os dois planos que formam o **Azure defender para SQL** são cobrados conforme mostrado na [página de preços](security-center-pricing.md)|
|Versões protegidas do SQL:|Banco de Dados SQL do Azure <br>Instância Gerenciada do Azure SQL<br>Azure Synapse Analytics (antigo SQL DW)<br>SQL Server (todas as versões com suporte)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Gov dos EUA<br>![Não](./media/icons/no-icon.png) China gov, outros gov|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>O que o Azure defender para SQL protege?

O **Azure defender para SQL consiste em** dois planos separados do Azure defender:

- O **Azure defender para servidores de banco de dados SQL do Azure** protege:
  - [Banco de Dados SQL do Azure](../azure-sql/database/sql-database-paas-overview.md)
  - [Instância Gerenciada do SQL do Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- O **Azure defender para SQL Servers em computadores (visualização)** estende as proteções para seus SQL Servers nativos do Azure para oferecer suporte total a ambientes híbridos e proteger os SQL Servers (todas as versões com suporte) hospedadas no Azure, em outros ambientes de nuvem e até mesmo em computadores locais


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Quais são os benefícios do Azure defender para SQL?

Esses dois planos incluem a funcionalidade para identificar e atenuar possíveis vulnerabilidades de banco de dados e detectar atividades anormais que podem indicar ameaças aos seus banco de dados:

- [Avaliação de vulnerabilidade](../azure-sql/database/sql-vulnerability-assessment.md) -o serviço de verificação para descobrir, acompanhar e ajudá-lo a corrigir possíveis vulnerabilidades de banco de dados. As verificações de avaliação fornecem uma visão geral do estado de segurança de suas máquinas SQL e detalhes de quaisquer descobertas de segurança.

- [Proteção avançada contra ameaças](../azure-sql/database/threat-detection-overview.md) – o serviço de detecção que monitora continuamente os SQL Servers em busca de ameaças como injeção de SQL, ataques de força bruta e abuso de privilégio. Esse serviço fornece alertas de segurança orientados a ações na central de segurança do Azure com detalhes da atividade suspeita, orientação sobre como mitigar as ameaças e opções para continuar suas investigações com o Azure Sentinel.


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Que tipo de alertas é fornecido pelo Azure defender para SQL?

Os alertas de segurança são disparados quando há:

- **Possíveis ataques de injeção de SQL** -incluindo vulnerabilidades detectadas quando os aplicativos geram uma instrução SQL com falha no banco de dados
- **Padrões de consulta e acesso de banco de dados anômalas** – por exemplo, um número anormalmente alto de tentativas de entrada com falha com credenciais diferentes (uma tentativa de força bruta)
- **Atividade de banco de dados suspeita** -por exemplo, uma alteração no destino de armazenamento de exportação para uma operação de importação e exportação do SQL

Os alertas incluem detalhes do incidente que os disparou, bem como recomendações sobre como investigar e corrigir ameaças.



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Azure defender para SQL.

Para obter material relacionado, consulte os seguintes artigos: 

- [Como habilitar o Azure defender para SQL Servers em computadores](defender-for-sql-usage.md)
- [Como habilitar o Azure defender para servidores de banco de dados SQL](../azure-sql/database/advanced-data-security.md)
- [A lista de alertas do Azure defender para SQL](alerts-reference.md#alerts-sql-db-and-warehouse)