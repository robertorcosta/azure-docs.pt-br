---
title: Segurança de dados avançada
description: Saiba mais sobre a funcionalidade para descobrir e classificar dados confidenciais, gerenciar suas vulnerabilidades de banco de dado e detectar atividades anormais que podem indicar uma ameaça ao banco de dados no banco de dados SQL do Azure, Azure SQL Instância Gerenciada ou Azure Synapse.
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
ms.date: 04/23/2020
ms.openlocfilehash: 53765ee97f0f253db4df4ecca3c1c90d6068fb07
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85983987"
---
# <a name="advanced-data-security"></a>Segurança de dados avançada
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


O ADS (segurança de dados avançada) é um pacote unificado para recursos avançados de segurança do SQL. O ADS está disponível para o banco de dados SQL do Azure, o Azure SQL Instância Gerenciada e o Azure Synapse Analytics. Ele inclui a funcionalidade para descobrir e classificar dados confidenciais, identificando e atenuante banco de dados vulnerabilidades potenciais e detectar atividades anormais que podem indicar uma ameaça para seu banco de dados. Fornece um local único para habilitar e gerenciar esses recursos.

## <a name="overview"></a>Visão geral

O ADS fornece um conjunto de recursos avançados de segurança do SQL, incluindo a descoberta de dados & classificação, avaliação de vulnerabilidades do SQL e proteção avançada contra ameaças.
- A [classificação de & de descoberta de dados](data-discovery-and-classification-overview.md) fornece recursos incorporados ao Azure SQL Database, azure SQL instância gerenciada e Synapse do Azure para descobrir, classificar, rotular e relatar os dados confidenciais em seus bancos. Pode ser usada para fornecer visibilidade em seu estado de classificação do banco de dados e para controlar o acesso a dados confidenciais no banco de dados e, além de suas bordas.
- A [avaliação de vulnerabilidade](sql-vulnerability-assessment.md) é um serviço fácil de configurar que pode descobrir, acompanhar e ajudá-lo a corrigir possíveis vulnerabilidades no banco de dados. Ele fornece visibilidade sobre seu estado de segurança e inclui etapas acionáveis para resolver problemas de segurança e aprimorar seu banco de dados fortifications.
- A [Proteção Avançada contra Ameaças](threat-detection-overview.md) detecta atividades anômalas, indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar seus bancos de dados. Ele monitora continuamente seu banco de dados em busca de atividades suspeitas e fornece alertas de segurança imediatos sobre possíveis vulnerabilidades, ataques de injeção de SQL do Azure e padrões de acesso de banco de dados anormais. Os alertas da Proteção Avançada contra Ameaças fornecem detalhes de atividades suspeitas e recomendam ações para investigar e atenuar a ameaça.

Habilite a segurança de dados avançada uma vez para habilitar todos esses recursos incluídos. Com um clique, você pode habilitar anúncios para todos os bancos de dados em seu [servidor](logical-servers.md) no Azure ou em seu instância gerenciada do SQL. A habilitação ou o gerenciamento de configurações de anúncios requer que pertençam à função do [Gerenciador de segurança do SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) ou a uma das funções de administrador de banco de dados ou servidor.

Os preços dos anúncios se alinham com a camada Standard da central de segurança do Azure, em que cada servidor protegido ou instância gerenciada é contada como um nó. Recursos protegidos recentemente se qualificam para uma avaliação gratuita da camada Standard da Central de Segurança. Para obter mais informações, confira a [página de preços](https://azure.microsoft.com/pricing/details/security-center/) da Central de Segurança do Azure.

## <a name="getting-started-with-ads"></a>Introdução ao ADS

As etapas a seguir são para você começar a usar a ADS.

## <a name="1-enable-ads"></a>1. habilitar anúncios

Habilite anúncios navegando até **segurança de dados avançada** no título de **segurança** para seu servidor ou instância gerenciada.

> [!NOTE]
> Uma conta de armazenamento é criada e configurada automaticamente para armazenar os resultados da verificação de **avaliação de vulnerabilidade** . Se você já tiver habilitado o ADS para outro servidor no mesmo grupo de recursos e região, a conta de armazenamento existente será usada.

![Habilitar a ADS](./media/advanced-data-security/enable_ads.png)

> [!NOTE]
> O custo dos anúncios está alinhado com o preço da camada Standard da central de segurança do Azure por nó, em que um nó é o servidor inteiro ou a instância gerenciada. Portanto, você está pagando apenas uma vez para proteger todos os bancos de dados no servidor ou instância gerenciada com anúncios. Você pode experimentar a ADS inicialmente com uma avaliação gratuita.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. iniciar a classificação de dados, o controle de vulnerabilidades e a investigação de alertas de ameaça

Clique no cartão **Descoberta de Dados e Classificação** para ver colunas confidenciais a serem classificadas e classificar seus dados com rótulos de confidencialidade persistente. Clique no cartão **Avaliação de Vulnerabilidade** para exibir e gerenciar relatórios e verificações de vulnerabilidade e relatórios e acompanhar sua estatura de segurança. Se alertas de segurança tiverem sido recebidos, clique no cartão **proteção avançada contra ameaças** para exibir detalhes dos alertas e para ver um relatório consolidado sobre todos os alertas em sua assinatura do Azure por meio da página alertas de segurança da central de segurança do Azure.

## <a name="3-manage-ads-settings"></a>3. gerenciar configurações de anúncios

Para exibir e gerenciar as configurações do ADS, navegue até **segurança de dados avançada** no título de **segurança** para seu servidor ou instância gerenciada. Nessa página, você pode habilitar ou desabilitar anúncios e modificar as configurações de avaliação de vulnerabilidade e proteção avançada contra ameaças para todo o servidor ou instância gerenciada.

![Configurações do servidor](./media/advanced-data-security/server_settings.png)

## <a name="4-manage-ads-settings-for-a-database"></a>4. gerenciar configurações de anúncios para um banco de dados

Para substituir as configurações da ADS para determinado banco de dados, marque a caixa de seleção **Habilitar Segurança de Dados Avançada no nível do banco de dados**. Use esta opção somente se você tiver um requisito específico para receber alertas separados de proteção avançada contra ameaças ou resultados de avaliação de vulnerabilidade para o banco de dados individual, no lugar do ou além dos alertas e resultados recebidos para todos os bancos de dado no servidor ou instância gerenciada.

Depois que a caixa de seleção for selecionada, você poderá definir as configurações relevantes para esse banco de dados.

![Configurações de proteção avançada contra ameaças e banco de dados](./media/advanced-data-security/database_threat_detection_settings.png)

As configurações de segurança de dados avançadas para seu servidor ou instância gerenciada também podem ser acessadas no painel de banco de dados ADS. Clique em **Configurações** no painel principal da ADS e, em seguida, clique em **Exibir configurações do servidor da Segurança de Dados Avançada**.

![Configurações de banco de dados](./media/advanced-data-security/database_settings.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [classificação de & de descoberta de dados](data-discovery-and-classification-overview.md)
- Saiba mais sobre a [avaliação de vulnerabilidade](sql-vulnerability-assessment.md)
- Saiba mais sobre a [proteção avançada contra ameaças](threat-detection-configure.md)
- Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
