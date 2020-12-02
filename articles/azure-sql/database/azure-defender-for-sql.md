---
title: Azure Defender para SQL
description: Saiba mais sobre a funcionalidade para gerenciar suas vulnerabilidades de banco de dados e detectar atividades anormais que podem indicar uma ameaça ao banco de dados no banco de dados SQL do Azure, Azure SQL Instância Gerenciada ou Azure Synapse.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: 4bb7f2fd5823a9d8ebf4234f3bb41d955574b838
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451564"
---
# <a name="azure-defender-for-sql"></a>Azure Defender para SQL
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


O Azure Defender para SQL é um pacote unificado de funcionalidades avançadas de segurança do SQL. O Azure defender está disponível para o banco de dados SQL do Azure, o Azure SQL Instância Gerenciada e o Azure Synapse Analytics. Ele inclui a funcionalidade para descobrir e classificar dados confidenciais, identificando e atenuante banco de dados vulnerabilidades potenciais e detectar atividades anormais que podem indicar uma ameaça para seu banco de dados. Fornece um local único para habilitar e gerenciar esses recursos.

## <a name="overview"></a>Visão geral

O Azure defender fornece um conjunto de recursos avançados de segurança do SQL, incluindo a avaliação de vulnerabilidades do SQL e a proteção avançada contra ameaças.
- A [avaliação de vulnerabilidade](sql-vulnerability-assessment.md) é um serviço fácil de configurar que pode descobrir, acompanhar e ajudá-lo a corrigir possíveis vulnerabilidades no banco de dados. Ele fornece visibilidade sobre seu estado de segurança e inclui etapas acionáveis para resolver problemas de segurança e aprimorar seu banco de dados fortifications.
- A [Proteção Avançada contra Ameaças](threat-detection-overview.md) detecta atividades anômalas, indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar seus bancos de dados. Ele monitora continuamente seu banco de dados em busca de atividades suspeitas e fornece alertas de segurança imediatos sobre possíveis vulnerabilidades, ataques de injeção de SQL do Azure e padrões de acesso de banco de dados anormais. Os alertas da Proteção Avançada contra Ameaças fornecem detalhes de atividades suspeitas e recomendam ações para investigar e atenuar a ameaça.

Habilite o Azure Defender para SQL uma vez para habilitar todos esses recursos incluídos. Com um clique, você pode habilitar o Azure defender para todos os bancos de dados em seu [servidor](logical-servers.md) no Azure ou em seu instância gerenciada do SQL. Habilitar ou gerenciar as configurações do Azure defender requer que pertença à função do [Gerenciador de segurança do SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) ou a uma das funções de administrador de banco de dados ou servidor.

Para obter mais informações sobre preços do Azure defender para SQL, consulte a [página de preços da central de segurança do Azure](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-azure-defender"></a>Introdução ao Azure defender

As etapas a seguir o ajudarão a começar a usar o Azure defender.

## <a name="enable-azure-defender"></a>Habilitar o Azure Defender

O Azure defender pode ser acessado por meio do [portal do Azure](https://portal.azure.com). Habilite o Azure defender navegando até a **central de segurança** no título de **segurança** do seu servidor ou instância gerenciada.

> [!NOTE]
> Uma conta de armazenamento é criada e configurada automaticamente para armazenar os resultados da verificação de **avaliação de vulnerabilidade** . Se você já tiver habilitado o Azure defender para outro servidor no mesmo grupo de recursos e região, a conta de armazenamento existente será usada.
>
> O custo do Azure defender está alinhado com o preço da camada Standard da central de segurança do Azure por nó, em que um nó é o servidor inteiro ou a instância gerenciada. Portanto, você está pagando apenas uma vez para proteger todos os bancos de dados no servidor ou instância gerenciada com o Azure defender. Você pode experimentar o Azure defender inicialmente com uma avaliação gratuita.

:::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="habilitar o Azure defender":::

## <a name="start-tracking-vulnerabilities-and-investigating-threat-alerts"></a>Iniciar o acompanhamento de vulnerabilidades e investigação de alertas de ameaça

Clique no cartão **Avaliação de Vulnerabilidade** para exibir e gerenciar relatórios e verificações de vulnerabilidade e relatórios e acompanhar sua estatura de segurança. Se alertas de segurança tiverem sido recebidos, clique no cartão **proteção avançada contra ameaças** para exibir detalhes dos alertas e para ver um relatório consolidado sobre todos os alertas em sua assinatura do Azure por meio da página alertas de segurança da central de segurança do Azure.

## <a name="manage-azure-defender-settings"></a>Gerenciar configurações do Azure defender

Para exibir e gerenciar as configurações do Azure defender, navegue até a **central de segurança** no título de **segurança** do seu servidor ou instância gerenciada. Nesta página, você pode habilitar ou desabilitar o Azure defender e modificar as configurações de avaliação de vulnerabilidade e proteção avançada contra ameaças para todo o servidor ou instância gerenciada.

:::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="configurações do servidor de segurança":::

## <a name="manage-azure-defender-settings-for-a-database"></a>Gerenciar configurações do Azure defender para um banco de dados

Para substituir as configurações do Azure defender para um determinado banco de dados, marque a caixa de seleção **habilitar o Azure defender para SQL no nível do banco** de dados nas configurações da **central de segurança** do banco de dados. Use esta opção somente se você tiver um requisito específico para receber alertas separados de proteção avançada contra ameaças ou resultados de avaliação de vulnerabilidade para o banco de dados individual, no lugar do ou além dos alertas e resultados recebidos para todos os bancos de dado no servidor ou instância gerenciada.

Depois que a caixa de seleção for selecionada, você poderá definir as configurações relevantes para esse banco de dados.

:::image type="content" source="media/azure-defender-for-sql/enable-for-database-level.png" alt-text="habilitar o Azure defender no nível do banco de dados":::

As configurações do Azure defender para SQL para seu servidor ou instância gerenciada também podem ser acessadas no painel do banco de dados do Azure defender. Clique em **configurações** no painel central de segurança principal e, em seguida, clique em **exibir configurações do Azure defender para SQL Server**.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [avaliação de vulnerabilidade](sql-vulnerability-assessment.md)
- Saiba mais sobre a [proteção avançada contra ameaças](threat-detection-configure.md)
- Saiba mais sobre a [Central de Segurança do Azure](../../security-center/security-center-introduction.md)