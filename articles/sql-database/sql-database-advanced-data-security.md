---
title: Segurança de dados avançada
description: Saiba sobre a funcionalidade para descobrir e classificar dados sensíveis, gerenciando suas vulnerabilidades do banco de dados e detectando atividades anômalas que podem indicar uma ameaça ao banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
ms.author: memildin
author: memildin
manager: rkarlin
ms.reviewer: vanto
ms.date: 03/31/2019
ms.openlocfilehash: 1f0e6694e596dc60264dfe0789a2f80090e0da3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269127"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Segurança de Dados Avançada para Banco de Dados SQL do Azure

A Segurança de Dados Avançada do SQL é um pacote unificado de funcionalidades avançadas de segurança do SQL. Ele inclui a funcionalidade para descobrir e classificar dados confidenciais, identificando e atenuante banco de dados vulnerabilidades potenciais e detectar atividades anormais que podem indicar uma ameaça para seu banco de dados. Fornece um local único para habilitar e gerenciar esses recursos.

## <a name="overview"></a>Visão geral

O ADS (Advanced Data Security, segurança avançada de dados) fornece um conjunto de recursos avançados de segurança SQL, incluindo a classificação de & de detecção de dados, avaliação de vulnerabilidades e Proteção avançada contra ameaças.

- [A classificação de & de descoberta de dados](sql-database-data-discovery-and-classification.md) fornece recursos incorporados ao Banco de Dados SQL do Azure para descobrir, classificar, rotular & proteger os dados confidenciais em seus bancos de dados. Pode ser usada para fornecer visibilidade em seu estado de classificação do banco de dados e para controlar o acesso a dados confidenciais no banco de dados e, além de suas bordas.
- [A avaliação de vulnerabilidades](sql-vulnerability-assessment.md) é um serviço fácil de configurar que pode descobrir, rastrear e ajudá-lo a corrigir possíveis vulnerabilidades no banco de dados. Fornece visibilidade sobre o estado de segurança e inclui etapas acionáveis para resolver problemas de segurança e aperfeiçoar as fprtificações do banco de dados.
- A [Proteção Avançada contra Ameaças](sql-database-threat-detection-overview.md) detecta atividades anômalas, indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar seus bancos de dados. Monitora continuamente o banco de dados com relação a atividades suspeitas e fornece alertas de segurança imediata sobre vulnerabilidades potenciais, ataques de injeção de SQL e padrões de acesso anormal do banco de dados. Os alertas da Proteção Avançada contra Ameaças fornecem detalhes de atividades suspeitas e recomendam ações para investigar e atenuar a ameaça.

Habilite a ADS do SQL uma vez para habilitar todos esses recursos incluídos. Com um clique, você pode habilitar a ADS para todos os bancos de dados em seu servidor do Banco de Dados SQL ou instância gerenciada. A habilitação ou o gerenciamento das configurações da ADS requer a função de [Gerenciador de Segurança de SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager), a função de administrador do banco de dados SQL ou a função de administrador do servidor SQL. 

O preço da ADS está alinhado à camada padrão da Central de Segurança do Azure, na qual cada instância gerenciada ou servidor do Banco de Dados SQL protegido é contado como um nó. Recursos protegidos recentemente se qualificam para uma avaliação gratuita da camada Standard da Central de Segurança. Para obter mais informações, confira a [página de preços](https://azure.microsoft.com/pricing/details/security-center/) da Central de Segurança do Azure.

## <a name="getting-started-with-ads"></a>Introdução à ADS

As etapas a seguir são para você começar a usar a ADS.

## <a name="1-enable-ads"></a>1. Habilitar ADS

Habilite o ADS navegando para a **Segurança Avançada de Dados** o título **Segurança** para o servidor do Banco de Dados SQL ou instância gerenciada. Para habilitar a ADS para todos os bancos de dados no servidor de banco de dados ou instância gerenciada, clique em **Habilitar segurança de dados avançada no servidor**.

> [!NOTE]
> Uma conta de armazenamento é criada e configurada automaticamente para armazenar os resultados da **verificação** de avaliação de vulnerabilidades. Se você já habilitou o ADS para outro servidor no mesmo grupo de recursos e região, então a conta de armazenamento existente será usada.

![Habilitar a ADS](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> O custo da ADS está alinhado aos preços da camada Standard da Central de Segurança do Azure por nó, em que um nó é toda a instância gerenciada ou servidor do Banco de Dados SQL. Portanto, você está pagando apenas uma vez para proteger todos os bancos de dados na instância gerenciada ou servidor de banco de dados com a ADS. Você pode experimentar a ADS inicialmente com uma avaliação gratuita.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Comece a classificar dados, rastrear vulnerabilidades e investigar alertas de ameaças

Clique no cartão **Descoberta de Dados e Classificação** para ver colunas confidenciais a serem classificadas e classificar seus dados com rótulos de confidencialidade persistente. Clique no cartão **Avaliação de Vulnerabilidade** para exibir e gerenciar relatórios e verificações de vulnerabilidade e relatórios e acompanhar sua estatura de segurança. Se os alertas de segurança forem recebidos, clique no cartão **de proteção de ameaças avançadas** para visualizar os detalhes dos alertas e ver um relatório consolidado sobre todos os alertas em sua assinatura do Azure através da página de alertas de segurança do Azure Security Center.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. Gerencie as configurações do ADS no servidor do Banco de Dados SQL ou na instância gerenciada

Para exibir e gerenciar as configurações da ADS, navegue até **Segurança de Dados Avançada** sob o título **Segurança** para seu servidor do Banco de Dados SQL ou instância gerenciada. Nesta página, você pode ativar ou desativar o ADS e modificar a avaliação de vulnerabilidades e as configurações de Proteção avançada de ameaças para todo o servidor do Banco de Dados SQL ou instância gerenciada.

![Configurações do servidor](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. Gerenciar configurações ADS para um banco de dados SQL

Para substituir as configurações da ADS para determinado banco de dados, marque a caixa de seleção **Habilitar Segurança de Dados Avançada no nível do banco de dados**. Use esta opção somente se você tiver um requisito específico para receber alertas avançados de proteção contra ameaças ou resultados de avaliação de vulnerabilidades separados para o banco de dados individual, no lugar ou além dos alertas e resultados recebidos para todos os bancos de dados no servidor de banco de dados ou instância gerenciada.

Uma vez que a caixa de seleção é selecionada, você pode configurar as configurações relevantes para este banco de dados.
 
![Configurações de banco de dados e proteção avançada de ameaças](./media/sql-advanced-protection/database_threat_detection_settings.png) 

As configurações de Segurança de Dados Avançada para seu servidor de banco de dados ou instância gerenciada também podem ser acessadas do painel de banco de dados da ADS. Clique em **Configurações** no painel principal da ADS e, em seguida, clique em **Exibir configurações do servidor da Segurança de Dados Avançada**. 

![Configurações de banco de dados](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Próximas etapas 

- Saiba mais sobre [descoberta e classificação de dados](sql-database-data-discovery-and-classification.md) 
- Saiba mais sobre [avaliação de vulnerabilidade](sql-vulnerability-assessment.md) 
- Saiba mais sobre [proteção avançada contra ameaças](sql-database-threat-detection.md)
- Saiba mais sobre [o centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
