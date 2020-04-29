---
title: Descoberta e Classificação de Dados
description: Data Discovery & classificação para o banco de dados SQL do Azure e o Azure Synapse Analytics
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81766950"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Data Discovery & classificação para o banco de dados SQL do Azure e o Azure Synapse Analytics

A classificação de & de descoberta de dados é incorporada ao banco de dado SQL do Azure. Ele fornece recursos avançados para descobrir, classificar, rotular e relatar os dados confidenciais em seus bancos de dado.

Seus dados mais confidenciais podem incluir informações comerciais, financeiras, de saúde ou pessoais. Descobrir e classificar esses dados pode desempenhar uma função dinâmica na abordagem de proteção de informações da sua organização. Esse recurso pode funcionar como a infraestrutura para:

- Ajudando a atender aos padrões de privacidade de dados e requisitos de conformidade regulatória.
- Vários cenários de segurança, como monitoramento (auditoria) e alertas sobre acesso anômalo a dados confidenciais.
- Controlando o acesso e protegendo a segurança de bancos de dados que contêm um dado altamente confidencial.

A classificação de & de dados de descoberta faz parte da oferta de [segurança de dados avançada](sql-database-advanced-data-security.md) , que é um pacote unificado para recursos avançados de segurança do SQL. Você pode acessar e gerenciar a descoberta de dados & classificação por meio da seção central do **SQL Advanced Data Security** do portal do Azure.

> [!NOTE]
> Este artigo está relacionado ao banco de dados SQL do Azure e ao Azure Synapse Analytics. Para simplificar, usamos o *banco de dados SQL* aqui para fazer referência ao banco de dados SQL e ao Azure Synapse. Para obter informações sobre SQL Server (local), consulte [descoberta e classificação de dados SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>O que é a descoberta de dados & classificação?

A classificação de & de descoberta de dados apresenta um conjunto de serviços avançados e novos recursos de banco de dado SQL. Ele forma um novo paradigma de proteção de informações para o banco de dados SQL, destinado a proteger os dados e não apenas ao banco de dado. O paradigma inclui:

- **Descoberta e recomendações:** O mecanismo de classificação verifica seu banco de dados e identifica as colunas que contenham informações potencialmente confidenciais. Em seguida, ele fornece uma maneira fácil de revisar e aplicar a classificação recomendada por meio do portal do Azure.

- **Rotulagem:** Você pode aplicar rótulos de classificação de sensibilidade persistentemente a colunas usando novos atributos de metadados que foram adicionados ao mecanismo de banco de dados SQL. Esses metadados podem então ser usados para cenários de proteção e auditoria avançada e baseada em sensibilidade.

- **Sensibilidade do conjunto de resultados da consulta:** A sensibilidade de um conjunto de resultados de consulta é calculada em tempo real para fins de auditoria.

- **Visibilidade:** Você pode exibir o estado de classificação do banco de dados em um painel detalhado na portal do Azure. Além disso, você pode baixar um relatório no formato do Excel para usar para fins de conformidade e auditoria e outras necessidades.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Descobrir, classificar e rotular colunas confidenciais

Esta seção descreve as etapas para:

- Descobrindo, classificando e rotulando colunas que contêm dados confidenciais em seu banco de dado.
- Exibindo o estado de classificação atual do banco de dados e exportando relatórios.

A classificação inclui dois atributos de metadados:

- **Rótulos**: os principais atributos de classificação, usados para definir o nível de sensibilidade dos dados armazenados na coluna.  
- **Tipos de informações**: atributos que fornecem informações mais granulares sobre o tipo de dados armazenados na coluna.

### <a name="define-and-customize-your-classification-taxonomy"></a>Definir e personalizar sua taxonomia de classificação

A classificação de & de descoberta de dados vem com um conjunto interno de rótulos de sensibilidade e um conjunto interno de tipos de informações e lógica de descoberta. Agora é possível personalizar essa taxonomia e definir um conjunto e uma classificação de constructos de classificação especificamente para seu ambiente.

Você define e personaliza sua taxonomia de classificação em um local central para toda a organização do Azure. Esse local está na [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), como parte de sua política de segurança. Somente alguém com direitos administrativos no grupo de gerenciamento raiz da organização pode realizar essa tarefa.

Como parte do gerenciamento de políticas da proteção de informações do SQL, você pode definir rótulos personalizados, classificá-los e associá-los a um conjunto selecionado de tipos de informações. Você também pode adicionar seus próprios tipos de informações personalizados e configurá-los com padrões de cadeia de caracteres. Os padrões são adicionados à lógica de descoberta para identificar esse tipo de dados em seus bancos.

Saiba mais sobre como personalizar e gerenciar sua política no [Guia de instruções da política de proteção de informações do SQL](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Depois que a política de toda a organização tiver sido definida, você poderá continuar classificando bancos de dados individuais usando sua política personalizada.

### <a name="classify-your-sql-database"></a>Classificar seu banco de dados SQL

1. Vá para o [Portal do Azure](https://portal.azure.com).

2. Vá para **segurança de dados avançada** no título **segurança** no painel do banco de dados SQL do Azure. Selecione **segurança de dados avançada**e, em seguida, selecione o cartão **Data Discovery & classificação** .

   ![Painel de segurança de dados avançado no portal do Azure](./media/sql-data-discovery-and-classification/data_classification.png)

3. Na página **classificação de data discovery &** , a guia **visão geral** inclui um resumo do estado de classificação atual do banco de dados. O resumo inclui uma lista detalhada de todas as colunas classificadas, que você também pode filtrar para mostrar apenas partes de esquema, tipos de informações e rótulos específicos. Se você ainda não classificou nenhuma coluna, [pule para a etapa 5](#step-5).

   ![Resumo do estado atual de classificação](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Para baixar um relatório no formato do Excel, selecione **Exportar** no menu superior do painel.

5. <a id="step-5"></a>Para começar a classificar seus dados, selecione a guia **classificação** na página **classificação de & de dados data Discovery** .

    O mecanismo de classificação verifica o banco de dados em busca de colunas que contenham dado potencialmente confidenciais e fornece uma lista de classificações de coluna recomendadas.

6. Exibir e aplicar recomendações de classificação:

   - Para exibir a lista de classificações de coluna recomendadas, selecione o painel recomendações na parte inferior do painel.

   - Para aceitar uma recomendação para uma coluna específica, marque a caixa de seleção na coluna à esquerda da linha relevante. Para marcar todas as recomendações como aceitas, marque a caixa de seleção mais à esquerda no cabeçalho da tabela de recomendações.

       ![Revisar e selecionar na lista de recomendações de classificação](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Para aplicar as recomendações selecionadas, selecione **aceitar recomendações selecionadas**.

7. Você também pode classificar colunas manualmente, como uma alternativa ou além da classificação baseada em recomendação:

   1. Selecione **Adicionar classificação** no menu superior do painel.

   1. Na janela de contexto que é aberta, selecione o esquema, a tabela e a coluna que você deseja classificar e o tipo de informação e o rótulo de sensibilidade.

   1. Selecione **Adicionar classificação** na parte inferior da janela de contexto.

      ![Selecione uma coluna para classificar](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Para concluir sua classificação e rotular de forma persistente (marca) as colunas do banco de dados com os novos metadados de classificação, selecione **salvar** no menu superior da janela.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Auditoria de acesso a dados confidenciais

Um aspecto importante do paradigma da proteção de informações é a capacidade de monitorar o acesso a dados confidenciais. A [auditoria do banco de dados SQL do Azure](sql-database-auditing.md) foi aprimorada para incluir um novo campo no `data_sensitivity_information`log de auditoria chamado. Esse campo registra as classificações de sensibilidade (rótulos) dos dados que foram retornados por uma consulta. Aqui está um exemplo:

![Log de auditoria](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Permissões

Essas funções internas podem ler a classificação de dados de um banco de dado SQL do Azure:

- Proprietário
- Leitor
- Colaborador
- Gerenciador de Segurança do SQL
- Administrador de Acesso do Usuário

Essas funções internas podem modificar a classificação de dados de um banco de dado SQL do Azure:

- Proprietário
- Colaborador
- Gerenciador de Segurança do SQL

Saiba mais sobre permissões baseadas em função no [RBAC para recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/overview).

## <a name="manage-classifications"></a><a id="manage-classification"></a>Gerenciar classificações

Você pode usar o T-SQL, uma API REST ou o PowerShell para gerenciar as classificações.

### <a name="use-t-sql"></a>Usar T-SQL

Você pode usar o T-SQL para adicionar ou remover classificações de coluna e para recuperar todas as classificações de todo o banco de dados.

> [!NOTE]
> Quando você usa o T-SQL para gerenciar rótulos, não há nenhuma validação que os rótulos que você adiciona a uma coluna existam na política de proteção de informações da organização (o conjunto de rótulos que aparecem nas recomendações do Portal). Então, cabe a você validar isso.

Para obter informações sobre como usar o T-SQL para classificações, consulte as seguintes referências:

- Para adicionar ou atualizar a classificação de uma ou mais colunas: [Adicionar classificação de sensibilidade](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Para remover a classificação de uma ou mais colunas: [remover classificação de sensibilidade](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Para exibir todas as classificações no banco de dados: [Sys. sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-the-rest-api"></a>Usar a API REST

Você pode usar a API REST para gerenciar programaticamente as classificações e recomendações. A API REST publicada dá suporte às seguintes operações:

- [Criar ou atualizar](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate): cria ou atualiza o rótulo de sensibilidade da coluna especificada.
- [Excluir](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete): exclui o rótulo de sensibilidade da coluna especificada.
- [Desabilitar recomendação](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation): desabilita as recomendações de sensibilidade na coluna especificada.
- [Habilitar recomendação](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation): habilita as recomendações de sensibilidade na coluna especificada. (As recomendações são habilitadas por padrão em todas as colunas.)
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get): Obtém o rótulo de sensibilidade da coluna especificada.
- [Listar atual pelo banco de dados](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase): Obtém os rótulos de sensibilidade atuais do banco de dados especificado.
- [Lista recomendada pelo banco de dados](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): Obtém os rótulos de sensibilidade recomendados do banco de dados especificado.

### <a name="use-powershell-cmdlets"></a>Usar cmdlets do PowerShell
Você pode usar o PowerShell para gerenciar classificações e recomendações para o banco de dados SQL do Azure e instâncias gerenciadas.

#### <a name="powershell-cmdlets-for-sql-database"></a>Cmdlets do PowerShell para banco de dados SQL

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Habilitar-AzSqlDatabaSesensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Desabilitar-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>Cmdlets do PowerShell para instâncias gerenciadas

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Habilitar-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Desabilitar-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>Próximas etapas

- Saiba mais sobre a [segurança de dados avançada](sql-database-advanced-data-security.md).
- Considere configurar a [Auditoria do Banco de Dados SQL do Azure](sql-database-auditing.md) para monitorar e auditar o acesso aos seus dados confidenciais classificados.
- Para uma apresentação que inclui descoberta e classificação de dados, consulte [descobrindo, classificando, rotulando & protegendo dados SQL | Dados expostos](https://www.youtube.com/watch?v=itVi9bkJUNc).
