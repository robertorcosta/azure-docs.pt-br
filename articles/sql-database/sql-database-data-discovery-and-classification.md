---
title: Descoberta e Classificação de Dados
description: Classificação de & de descoberta de dados para banco de dados SQL do Azure e Análise sinapse do Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: eb4e7907c3dcffed035307c2084160ce6051be13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409942"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Classificação de & de descoberta de dados para banco de dados SQL do Azure e Análise sinapse do Azure

A Classificação de & de descoberta de dados fornece recursos avançados incorporados ao Banco de Dados SQL do Azure para **descobrir,** **classificar,** **rotular** & **endodendo os** dados confidenciais em seus bancos de dados.

Descobrir e classificar seus dados mais confidenciais (negócios, financeiros, atendimento à saúde, PII [informações de identificação pessoal] etc.) pode desempenhar uma função essencial para a estatura de proteção das informações organizacionais. Esse recurso pode funcionar como a infraestrutura para:

- Ajudar a cumprir os padrões de privacidade de dados e os requisitos de conformidade a normas.
- Vários cenários de segurança, como monitoramento (auditoria) e alertas sobre acesso anômalo a dados confidenciais.
- Controlar o acesso e proteção da segurança de bancos de dados contendo dados altamente confidenciais.

A Classificação de & de Descoberta de Dados faz parte da oferta [de Advanced Data Security](sql-database-advanced-data-security.md) (ADS), que é um pacote unificado para recursos avançados de segurança SQL. A descoberta e classificação de dados pode ser acessada e gerenciada por meio do portal central de ADS do SQL.

> [!NOTE]
> Este documento diz respeito ao Banco de Dados SQL do Azure e ao Azure Synapse. Para simplificar, o Banco de Dados SQL é usado quando se refere tanto ao Banco de Dados SQL quanto ao Sinapse Azure. Para obter o SQL Server (no local), consulte [SQL Data Discovery and Classification](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="subheading-1"></a>O que é descoberta e classificação de dados

A Descoberta e Classificação de Dados apresenta um conjunto de serviços avançados e novos recursos SQL, formando um novo paradigma de Proteção de Informações do SQL, visando proteger os dados e não apenas o banco de dados:

- **Descoberta e recomendações**

  O mecanismo de classificação examina o banco de dados e identifica colunas contendo dados potencialmente confidenciais. Em seguida, fornece uma maneira fácil de revisar e aplicar as recomendações de classificação apropriadas por meio do Portal do Azure.

- **Rotulação**

  Os rótulos de classificação de confidencialidade podem ser marcados persistentemente em colunas usando novos atributos de metadados de classificação introduzidos no SQL Engine. Esses metadados podem ser utilizados para cenários de proteção e auditoria baseada em confidencialidade.

- **Sensibilidade de conjunto de resultados de consulta**

  A confidencialidade do conjunto de resultados da consulta é calculada em tempo real para fins de auditoria.

- **Visibilidade**

  O estado da classificação do banco de dados pode ser visualizado em um painel detalhado no portal. Além disso, você pode fazer o download de um relatório (no formato Excel) para ser usado para fins de conformidade e auditoria, bem como outras necessidades.

## <a name="discover-classify--label-sensitive-columns"></a><a id="subheading-2"></a>Descobrir, classificar e rotular colunas confidenciais

A seção a seguir descreve as etapas para descobrir, classificar e rotular colunas que contenham dados confidenciais no banco de dados, bem como exibir o estado atual de classificação do banco de dados e exportar relatórios.

A classificação inclui dois atributos de metadados:

- Rótulos – os atributos de classificação principais, usados para definir o nível de confidencialidade dos dados armazenados na coluna.  
- Tipos de informações – fornecem uma granularidade adicional para o tipo dos dados armazenados na coluna.

## <a name="define-and-customize-your-classification-taxonomy"></a>Definir e personalizar sua taxonomia de classificação

A Classificação & de Descoberta de Dados vem com um conjunto integrado de rótulos de sensibilidade e um conjunto integrado de tipos de informações e lógica de descoberta. Agora, você tem a possibilidade de personalizar esta taxonomia e definir um conjunto e uma classificação de constructos de classificação especificamente para seu ambiente.

A definição e a personalização de sua taxonomia de classificação são feitas em um local central para todo o seu locatário do Azure. O local é a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), como parte de sua Política de Segurança. Somente alguém com direitos administrativos no grupo de gerenciamento raiz do Locatário pode executar essa tarefa.

Como parte do gerenciamento de políticas da Proteção de Informações, você pode definir rótulos personalizados, classificá-los e associá-los a um conjunto selecionado de tipos de informações. Você também pode adicionar seus próprios tipos de informações personalizadas e configurá-los com padrões de cadeia de caracteres, que são adicionados à lógica de descoberta para identificar esse tipo de dados em seus bancos de dados.
Saiba mais sobre como personalizar e gerenciar sua política no [Guia de instruções sobre política da Proteção de Informações](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Depois que a política de todo o locatário tiver sido definida, você poderá continuar com a classificação de bancos de dados individuais usando sua política personalizada.

## <a name="classify-your-sql-database"></a>Classificar o Banco de Dados SQL

1. Vá para o [portal Azure.](https://portal.azure.com)

2. Navegue para **Segurança de Dados Avançada** no cabeçalho de segurança do painel do Banco de Dados SQL do Azure. Clique para ativar a segurança avançada dos dados e clique no cartão **de classificação data discovery &.**

   ![Examinar um banco de dados](./media/sql-data-discovery-and-classification/data_classification.png)

3. A guia **Visão geral** inclui um resumo do estado de classificação atual do banco de dados, incluindo uma lista detalhada de todas as colunas classificadas, que você também pode filtrar para exibir apenas partes específicas do esquema, tipos de informações e rótulos. Se ainda não classificou nenhuma coluna, [pule para a etapa 5](#step-5).

   ![Resumo do estado atual de classificação](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Para fazer o download de um relatório no formato Excel, clique na opção **Exportar** no menu superior da janela.

   ![Exportar para o Excel](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>Para começar a classificar os dados, clique na guia **Classificação** na parte superior da janela.

    ![Classificar os dados](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. O mecanismo de classificação verifica seu banco de dados em busca de colunas contendo dados potencialmente sensíveis e fornece uma lista de classificações de **colunarecomendadas**. Para visualizar e aplicar recomendações de classificação:

   - Para visualizar a lista das classificações de colunas recomendadas, clique no painel de recomendações na parte inferior da janela:

      ![Classificar os dados](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Revise a lista de recomendações – para aceitar uma recomendação de uma coluna específica, marque a caixa de seleção na coluna esquerda da linha relevante. Você também pode marcar *todas as recomendações* como aceitas, marcando a caixa de seleção no cabeçalho da tabela de recomendações.

       ![Examinar a lista de recomendação](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Para aplicar as recomendações selecionadas, clique no botão **Aceitar recomendações selecionadas** azul.

      ![Aplicar recomendações](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Você também pode **classificar manualmente** as colunas como uma alternativa ou, além da classificação de recomendação:

   - Clicar em **Adicionar classificação** no menu superior da janela.

      ![Adicionar a classificação manualmente](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - Na janela de contexto que é aberta, selecionar o esquema > a tabela > a coluna que você deseja classificar e, em seguida, o tipo de informações e o rótulo confidencialidade. Em seguida, clicar no botão **Adicionar classificação** azul na parte inferior da janela do contexto.

      ![Selecionar a coluna a ser classificada](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Para concluir sua classificação e definir um rótulo (uma marca) persistente para as colunas do banco de dados com os novos metadados de classificação, clique em **Salvar** no menu superior da janela.

   ![Salvar](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a name="auditing-access-to-sensitive-data"></a><a id="subheading-3"></a>Auditoria de acesso aos dados confidenciais

Um aspecto importante do paradigma da proteção de informações é a capacidade de monitorar o acesso a dados confidenciais. A [Auditoria do Banco de Dados SQL do Azure](sql-database-auditing.md) foi aprimorada para incluir um novo campo no log de auditoria chamado *data_sensitivity_information*, que registra a classificações de confidencialidade (rótulos) dos dados reais que foram retornados pela consulta.

![Log de auditoria](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="subheading-4"></a>Permissões

As seguintes funções incorporadas podem ler a classificação de `Owner` `Reader`dados `Contributor` `SQL Security Manager` de `User Access Administrator`um banco de dados Azure SQL: , , e .

As seguintes funções incorporadas podem modificar a classificação de `Owner` `Contributor`dados `SQL Security Manager`de um banco de dados Azure SQL: , .

Saiba mais sobre [o RBAC para recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="manage-classifications"></a><a id="subheading-5"></a>Gerenciar classificações

# <a name="t-sql"></a>[T-SQL](#tab/azure-t-sql)
Você pode usar o T-SQL para adicionar/remover classificações de coluna, bem como para recuperar todas as classificações para o banco de dados inteiro.

> [!NOTE]
> Ao usar o T-SQL para gerenciar rótulos, não há nenhuma validação de que os rótulos adicionados a uma coluna existem na política de proteção de informações organizacionais (o conjunto de rótulos exibido nas recomendações do portal). Portanto, cabe a você validar isso.

- Adicione/atualize a classificação de uma ou mais colunas: [ADD SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Remova a classificação de uma ou mais colunas: [DROP SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Exiba todas as classificações no banco de dados: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

# <a name="rest-apis"></a>[APIs de descanso](#tab/azure-rest-api)
Você pode usar APIs REST para gerenciar programáticamente classificações e recomendações. As APIs REST publicadas dão suporte às seguintes operações:

- [Criar ou atualizar](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) - Cria ou atualiza o rótulo de sensibilidade de uma determinada coluna
- [Excluir](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) - Exclui o rótulo de confidencialidade de uma determinada coluna
- [Recomendação de desabilitação](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) - Desativar recomendações de sensibilidade em uma determinada coluna
- [Habilitar recomendação](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) - Habilita recomendações de sensibilidade em uma determinada coluna (as recomendações são habilitadas por padrão em todas as colunas)
- [Obter](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get)  - Obtém o rótulo de confidencialidade de uma determinada coluna
- [Lista por Banco de Dados Atual](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) - obtém os rótulos atuais de confidencialidade de determinado banco de dados
- [Lista recomendada por banco de dados](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) - Obtém os rótulos de sensibilidade recomendados de um determinado banco de dados

# <a name="powershell-cmdlet"></a>[Cmdlet do PowerShell](#tab/azure-powelshell)
Você pode usar o PowerShell para gerenciar classificações e recomendações para o Banco de Dados SQL do Azure e a Instância Gerenciada.

### <a name="powershell-cmdlet-for-azure-sql-database"></a>Cmdlet PowerShell para banco de dados SQL do Azure
- [Classificação de sensibilidade do banco de dados get-AzSql](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Recomendação de sensibilidade ao banco de dados get-AzSql](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Recomendação de sensibilidade de Enable-AzSqlDatabaSe](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Desativação-AzSqlDatabaseSensitivityRecomendação](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>Cmdlets do PowerShell para instância gerenciada
- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Recomendação de sensibilidade ao banco de dados do Enable-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Desativação-AzSqlInstanceDatabaseSensitivityRecomendação](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

---

## <a name="next-steps"></a><a id="subheading-6"></a>Próximas etapas

- Saiba mais sobre a [segurança de dados avançada](sql-database-advanced-data-security.md).
- Considere configurar a [Auditoria do Banco de Dados SQL do Azure](sql-database-auditing.md) para monitorar e auditar o acesso aos seus dados confidenciais classificados.
- Para uma apresentação no YouTube que inclua classificação de & de descoberta de dados, consulte [Descobrir, classificar, rotular & proteger dados SQL | Dados expostos](https://www.youtube.com/watch?v=itVi9bkJUNc).

<!--Anchors-->
[What is data discovery & classification]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Permissions]: #subheading-4
[Manage classifications]: #subheading-5
[Next Steps]: #subheading-6
