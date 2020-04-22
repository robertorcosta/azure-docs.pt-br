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
ms.date: 04/21/2020
tags: azure-synapse
ms.openlocfilehash: f05b4d4fec99aaa2fb79da46e2167d883d1f15ec
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766950"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Classificação de & de descoberta de dados para banco de dados SQL do Azure e Análise sinapse do Azure

A classificação & de descoberta de dados é incorporada ao Banco de Dados Do Azure SQL. Ele fornece recursos avançados para descobrir, classificar, rotular e relatar os dados confidenciais em seus bancos de dados.

Seus dados mais confidenciais podem incluir informações comerciais, financeiras, de saúde ou pessoais. Descobrir e classificar esses dados pode desempenhar um papel fundamental na abordagem de proteção de informações da sua organização. Esse recurso pode funcionar como a infraestrutura para:

- Ajudando a atender aos padrões de privacidade de dados e requisitos para conformidade normativa.
- Vários cenários de segurança, como monitoramento (auditoria) e alertas sobre acesso anômalo a dados confidenciais.
- Controlar o acesso e endurecer a segurança de bancos de dados que contenham dados altamente confidenciais.

A Classificação de & de Descoberta de Dados faz parte da oferta de Segurança Avançada de [Dados,](sql-database-advanced-data-security.md) que é um pacote unificado para recursos avançados de segurança SQL. Você pode acessar e gerenciar a Classificação de & de Descoberta de Dados através da seção central **sql advanced de segurança** de dados do portal Azure.

> [!NOTE]
> Este artigo refere-se ao Azure SQL Database e ao Azure Synapse Analytics. Para simplificar, usamos *o Banco de Dados SQL* aqui para se referir tanto ao Banco de Dados SQL quanto ao Azure Synapse. Para obter informações sobre o SQL Server (no local), consulte [SQL Data Discovery and Classification](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>O que é classificação de & de descoberta de dados?

Data Discovery & Classification introduz um conjunto de serviços avançados e novos recursos de banco de dados SQL. Ele forma um novo paradigma de proteção de informações para o Banco de Dados SQL, destinado a proteger os dados e não apenas o banco de dados. O paradigma inclui:

- **Descoberta e recomendações:** O mecanismo de classificação verifica seu banco de dados e identifica colunas que contêm dados potencialmente sensíveis. Em seguida, fornece-lhe uma maneira fácil de rever e aplicar classificação recomendada através do portal Azure.

- **Rotulagem:** Você pode aplicar rótulos de classificação de sensibilidade persistentemente às colunas usando novos atributos de metadados que foram adicionados ao mecanismo de banco de dados SQL. Esses metadados podem ser usados para cenários avançados de auditoria e proteção baseados em sensibilidade.

- **Sensibilidade do conjunto de resultados da consulta:** A sensibilidade de um conjunto de resultados de consulta é calculada em tempo real para fins de auditoria.

- **Visibilidade:** Você pode visualizar o estado de classificação do banco de dados em um painel detalhado no portal Azure. Além disso, você pode baixar um relatório em formato Excel para usar para fins de conformidade e auditoria e outras necessidades.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Descubra, classifique e rotule colunas sensíveis

Esta seção descreve as etapas para:

- Descobrir, classificar e rotular colunas que contenham dados confidenciais em seu banco de dados.
- Visualizando o estado de classificação atual do seu banco de dados e exportando relatórios.

A classificação inclui dois atributos de metadados:

- **Rótulos**: Os principais atributos de classificação, utilizados para definir o nível de sensibilidade dos dados armazenados na coluna.  
- **Tipos de informações**: Atributos que fornecem informações mais granulares sobre o tipo de dados armazenados na coluna.

### <a name="define-and-customize-your-classification-taxonomy"></a>Definir e personalizar sua taxonomia de classificação

A Classificação & de Descoberta de Dados vem com um conjunto integrado de rótulos de sensibilidade e um conjunto integrado de tipos de informações e lógica de descoberta. Agora é possível personalizar essa taxonomia e definir um conjunto e uma classificação de constructos de classificação especificamente para seu ambiente.

Você define e personaliza sua taxonomia de classificação em um lugar central para toda a sua organização Azure. Esse local está no [Azure Security Center,](https://docs.microsoft.com/azure/security-center/security-center-intro)como parte de sua política de segurança. Somente alguém com direitos administrativos no grupo de gerenciamento raiz da organização pode fazer essa tarefa.

Como parte do gerenciamento de políticas para proteção de informações SQL, você pode definir rótulos personalizados, classificá-los e associá-los a um conjunto selecionado de tipos de informações. Você também pode adicionar seus próprios tipos de informações personalizadas e configurá-los com padrões de strings. Os padrões são adicionados à lógica de descoberta para identificar esse tipo de dados em seus bancos de dados.

Saiba mais sobre a personalização e o gerenciamento de sua política na [política de proteção de informações SQL como orientar](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Depois que a política em toda a organização foi definida, você pode continuar classificando bancos de dados individuais usando sua política personalizada.

### <a name="classify-your-sql-database"></a>Classifique seu banco de dados SQL

1. Vá para o [Portal do Azure](https://portal.azure.com).

2. Vá para **a Segurança Avançada de Dados** sob o título **Segurança** no painel do banco de dados SQL do Azure. Selecione **Segurança avançada de dados**e selecione o cartão Data Discovery & **Classification.**

   ![Painel avançado de segurança de dados no portal Azure](./media/sql-data-discovery-and-classification/data_classification.png)

3. Na página **de classificação & de detecção de dados,** a guia **Visão geral** inclui um resumo do estado de classificação atual do banco de dados. O resumo inclui uma lista detalhada de todas as colunas classificadas, que você também pode filtrar para mostrar apenas partes específicas do esquema, tipos de informações e rótulos. Se você ainda não classificou nenhuma coluna, [pule para o passo 5](#step-5).

   ![Resumo do estado atual de classificação](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Para baixar um relatório no formato Excel, **selecione Exportar** no menu superior do painel.

5. <a id="step-5"></a>Para começar a classificar seus dados, selecione a guia **Classificação** na página **de classificação & de detecção de & de detecção de dados.**

    O mecanismo de classificação verifica seu banco de dados em busca de colunas que contenham dados potencialmente sensíveis e fornece uma lista de classificações recomendadas de colunas.

6. Veja e aplique recomendações de classificação:

   - Para ver a lista de classificações de colunarecomendadas, selecione o painel de recomendações na parte inferior do painel.

   - Para aceitar uma recomendação para uma coluna específica, selecione a caixa de seleção na coluna esquerda da linha relevante. Para marcar todas as recomendações conforme aceita, selecione a caixa de seleção mais à esquerda no cabeçalho da tabela de recomendações.

       ![Revisar e selecionar na lista de recomendações de classificação](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Para aplicar as recomendações selecionadas, **selecione Aceitar recomendações selecionadas**.

7. Você também pode classificar as colunas manualmente, como uma alternativa ou além da classificação baseada em recomendações:

   1. Selecione **Adicionar classificação** no menu superior do painel.

   1. Na janela de contexto que se abre, selecione o esquema, tabela e coluna que você deseja classificar e o tipo de informação e o rótulo de sensibilidade.

   1. Selecione **Adicionar classificação** na parte inferior da janela de contexto.

      ![Selecione uma coluna para classificar](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Para completar sua classificação e rotular persistentemente (tag) as colunas de banco de dados com os novos metadados de classificação, **selecione Salvar** no menu superior da janela.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Acesso de auditoria a dados confidenciais

Um aspecto importante do paradigma de proteção da informação é a capacidade de monitorar o acesso a dados confidenciais. [A auditoria do banco de dados Azure SQL](sql-database-auditing.md) foi aprimorada `data_sensitivity_information`para incluir um novo campo no registro de auditoria chamado . Este campo registra as classificações de sensibilidade (rótulos) dos dados que foram devolvidos por uma consulta. Aqui está um exemplo:

![Log de auditoria](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Permissões

Essas funções incorporadas podem ler a classificação de dados de um banco de dados SQL do Azure:

- Proprietário
- Leitor
- Colaborador
- Gerenciador de Segurança do SQL
- Administrador de Acesso do Usuário

Essas funções incorporadas podem modificar a classificação de dados de um banco de dados SQL do Azure:

- Proprietário
- Colaborador
- Gerenciador de Segurança do SQL

Saiba mais sobre permissões baseadas em papéis no [RBAC para recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview).

## <a name="manage-classifications"></a><a id="manage-classification"></a>Gerenciar classificações

Você pode usar o T-SQL, uma API REST ou powerShell para gerenciar classificações.

### <a name="use-t-sql"></a>Usar T-SQL

Você pode usar o T-SQL para adicionar ou remover classificações de colunas e recuperar todas as classificações para todo o banco de dados.

> [!NOTE]
> Quando você usa o T-SQL para gerenciar rótulos, não há validação de que os rótulos que você adiciona a uma coluna existem na política de proteção de informações da organização (o conjunto de rótulos que aparecem nas recomendações do portal). Então, cabe a você validar isso.

Para obter informações sobre o uso do T-SQL para classificações, consulte as seguintes referências:

- Para adicionar ou atualizar a classificação de uma ou mais colunas: [ADD SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Para remover a classificação de uma ou mais colunas: [CLASSIFICAÇÃO DE SENSIBILIDADE AO GOTA](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Para visualizar todas as classificações no banco de dados: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-the-rest-api"></a>Use a API resto

Você pode usar a API REST para gerenciar programáticamente classificações e recomendações. A API REST publicada suporta as seguintes operações:

- [Criar ou atualizar](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate): Cria ou atualiza o rótulo de sensibilidade da coluna especificada.
- [Excluir](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete): Exclui o rótulo de sensibilidade da coluna especificada.
- [Recomendação desabilitar](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation): Desativar recomendações de sensibilidade na coluna especificada.
- [Habilitar recomendação](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation): Habilita recomendações de sensibilidade na coluna especificada. (As recomendações são habilitadas por padrão em todas as colunas.)
- [Obter](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get): Obtém o rótulo de sensibilidade da coluna especificada.
- [Lista Corrente por Banco de Dados](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase): Obtém os rótulos de sensibilidade atuais do banco de dados especificado.
- [Lista recomendada por banco de dados](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): Obtém os rótulos de sensibilidade recomendados do banco de dados especificado.

### <a name="use-powershell-cmdlets"></a>Usar cmdlets do PowerShell
Você pode usar o PowerShell para gerenciar classificações e recomendações para o Banco de Dados SQL do Azure e instâncias gerenciadas.

#### <a name="powershell-cmdlets-for-sql-database"></a>Cmdlets PowerShell para banco de dados SQL

- [Classificação de sensibilidade do banco de dados get-AzSql](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Recomendação de sensibilidade ao banco de dados get-AzSql](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Recomendação de sensibilidade de Enable-AzSqlDatabaSe](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Desativação-AzSqlDatabaseSensitivityRecomendação](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>Cmdlets PowerShell para instâncias gerenciadas

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Recomendação de sensibilidade ao banco de dados do Enable-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Desativação-AzSqlInstanceDatabaseSensitivityRecomendação](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>Próximas etapas

- Saiba mais sobre [o Advanced Data Security](sql-database-advanced-data-security.md).
- Considere configurar a [Auditoria do Banco de Dados SQL do Azure](sql-database-auditing.md) para monitorar e auditar o acesso aos seus dados confidenciais classificados.
- Para uma apresentação que inclua a descoberta e classificação de dados, consulte [Descobrir, classificar, rotular & proteger dados SQL | Dados expostos](https://www.youtube.com/watch?v=itVi9bkJUNc).
