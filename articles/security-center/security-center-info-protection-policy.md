---
title: Política de proteção de informações do SQL na central de segurança do Azure
description: Aprenda a personalizar as políticas de proteção de informações na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2020
ms.author: memildin
ms.openlocfilehash: aa3492cb67a4ccd1c09a1f1cb55ddc4f2e00953d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318594"
---
# <a name="sql-information-protection-policy-in-azure-security-center"></a>Política de proteção de informações do SQL na central de segurança do Azure
 
O [mecanismo de classificação e descoberta de dados](../azure-sql/database/data-discovery-and-classification-overview.md) da proteção de informações do SQL é integrado ao Azure SQL [Database](../azure-sql/database/sql-database-paas-overview.md), ao [Azure SQL instância gerenciada](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)e ao [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Ele fornece recursos avançados para descobrir, classificar, rotular e relatar os dados confidenciais em seus bancos de dado.

O mecanismo de classificação baseia-se em duas construções principais que compõem a taxonomia de classificação:

- **Rótulos** – os principais atributos de classificação, usados para definir o nível de sensibilidade dos dados armazenados na coluna. 
- **Tipos de informações** – fornece uma granularidade adicional para o tipo de dados armazenados na coluna.

As opções de política da proteção de informações na central de segurança fornecem um conjunto predefinido de rótulos e tipos de informações que servem como padrões para o mecanismo de classificação. Você pode personalizar a política, de acordo com as necessidades da sua organização, conforme descrito abaixo.

:::image type="content" source="./media/security-center-info-protection-policy/sql-information-protection-policy-page.png" alt-text="A página que mostra sua política de proteção de informações do SQL":::
 



## <a name="how-do-i-access-the-sql-information-protection-policy"></a>Como fazer acessar a política de proteção de informações do SQL?

Há três maneiras de acessar a política de proteção de informações:

- **(Recomendado)** Na página preços e configurações da central de segurança.
- Na recomendação de segurança, "dados confidenciais em seus bancos de dado SQL devem ser classificados".
- Na página de descoberta de dados do BD SQL do Azure.

Cada um deles é mostrado na guia relevante abaixo.



### <a name="from-security-centers-settings"></a>[**Das configurações da central de segurança**](#tab/sqlip-tenant)

### <a name="access-the-policy-from-security-centers-pricing-and-settings-page"></a>Acessar a política na página de preços e configurações da central de segurança <a name="sqlip-tenant"></a>

Para personalizar a política de proteção de informações do seu locatário do Azure, você precisa ter [privilégios administrativos no grupo de gerenciamento de raiz do locatário](security-center-management-groups.md). 

Na página de **preços e configurações** da central de segurança, selecione **proteção de informações do SQL**.

:::image type="content" source="./media/security-center-info-protection-policy/pricing-settings-link-to-information-protection.png" alt-text="Acessando a política de proteção de informações do SQL na página preços e configurações da central de segurança do Azure":::



### <a name="from-security-centers-recommendation"></a>[**Da recomendação da central de segurança**](#tab/sqlip-db)

### <a name="access-the-policy-from-the-security-center-recommendation"></a>Acessar a política da recomendação da central de segurança <a name="sqlip-db"></a>

Use a recomendação da central de segurança, "dados confidenciais em seus bancos de dados SQL devem ser classificados", para exibir a página de classificação e descoberta de dados para seu banco de dado. Lá, você também verá as colunas descobertas para conter informações que recomendamos classificar.

1. Na página **recomendações** da central de segurança, pesquise os **dados confidenciais de recomendação em seus bancos de dado SQL devem ser classificados**.

    :::image type="content" source="./media/security-center-info-protection-policy/sql-sensitive-data-recommendation.png" alt-text="Encontrando a recomendação que fornece acesso às políticas de proteção de informações do SQL":::

1. Na página detalhes da recomendação, selecione o banco de dados relevante nas guias **íntegros** ou **não íntegros** .

1. A página **classificação de & de dados de descoberta** é aberta. Selecione **Configurar**. 

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-security-center-recommendation.png" alt-text="Abrindo a política de proteção de informações do SQL da recomendação relevante na central de segurança do Azure":::



### <a name="from-azure-sql"></a>[**Do Azure SQL**](#tab/sqlip-azuresql)

### <a name="access-the-policy-from-azure-sql"></a>Acessar a política do SQL do Azure <a name="sqlip-azuresql"></a>

1. No portal do Azure, abra o SQL do Azure.

    :::image type="content" source="./media/security-center-info-protection-policy/open-azure-sql.png" alt-text="Abrindo o SQL do Azure no portal do Azure":::

1. Selecione qualquer banco de dados.

1. Na área **segurança** do menu, abra a página de **classificação de & de descoberta de dados** (1) e selecione **Configurar** (2).

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-azure-sql.png" alt-text="Abrindo a política de proteção de informações do SQL do Azure SQL":::

--- 


## <a name="customize-your-information-types"></a>Personalizar seus tipos de informações

Para gerenciar e personalizar tipos de informações:

1. Selecione **gerenciar tipos de informações**.

    :::image type="content" source="./media/security-center-info-protection-policy/manage-types.png" alt-text="Gerenciar tipos de informações para sua política de proteção de informações":::

1. Para adicionar uma nova **tipo de informação** , selecione **criar o tipo de informação** no menu superior. Você pode configurar um nome, descrição e pesquisar cadeias de caracteres padrão para o **tipo de informação**. As sequências padrão de pesquisa podem, opcionalmente, usar palavras-chave com caracteres curinga (usando o caractere '%'), que o mecanismo de descoberta automatizada usa para identificar dados confidenciais em seus bancos de dados, com base nos metadados das colunas.
 
    :::image type="content" source="./media/security-center-info-protection-policy/configure-new-type.png" alt-text="Configurar um novo tipo de informação para sua política de proteção de informações":::

1. Você também pode configurar os **Tipos de informações** internos adicionando cadeias de padrões de pesquisa adicionais, desabilitando algumas das cadeias existentes ou alterando a descrição. Você não pode excluir **tipos de informações** internos ou editar seus nomes. 
1. **Os tipos de informação** são listados em ordem de classificação de descoberta ascendente, o que significa que os tipos mais altos na lista tentarão corresponder primeiro. Para alterar a classificação entre os tipos de informação, arraste os tipos para o ponto certo na tabela ou use os botões **Mover para cima** e **Mover para baixo** para alterar a ordem. 
1. Selecione **OK** quando terminar.
1. Após você gerenciar os tipos de informação, certifique-se de associar os tipos relevantes com os rótulos relevantes, clicando **configurar** para um rótulo específico e adicionar ou excluir tipos de informações conforme apropriado.
1. Para aplicar suas alterações, selecione **salvar** na página **Rótulos** principais.
 

## <a name="exporting-and-importing-a-policy"></a>Exportando e importando uma política

Você pode baixar um arquivo JSON com os rótulos e tipos de informações definidos, editar o arquivo no editor de sua escolha e, em seguida, importar o arquivo atualizado. 

:::image type="content" source="./media/security-center-info-protection-policy/export-import.png" alt-text="Exportando e importando sua política de proteção de informações":::

> [!NOTE]
> Você precisará de permissões de nível de locatário para importar um arquivo de política. 


## <a name="manage-sql-information-protection-using-azure-powershell"></a>Gerenciar a proteção de informações do SQL usando o Azure PowerShell

- [Get-AzSqlInformationProtectionPolicy](/powershell/module/az.security/get-azsqlinformationprotectionpolicy): recupera a política de proteção de informações SQL do locatário em vigor.
- [Set-AzSqlInformationProtectionPolicy](/powershell/module/az.security/set-azsqlinformationprotectionpolicy): define a política de proteção de informações SQL do locatário em vigor.
 

## <a name="next-steps"></a>Próximas etapas
 
Neste artigo, você aprendeu a definir uma política de Proteção de Informações do SQL na Central de Segurança do Azure. Para saber mais sobre como usar o SQL Information Protection para classificar e proteger dados confidenciais em seus bancos de dados SQL, consulte [Descoberta e Classificação de Dados do Banco de Dados SQL do Azure](../azure-sql/database/data-discovery-and-classification-overview.md). 

Para obter mais informações sobre políticas de segurança e segurança de dados na Central de Segurança do Azure, consulte os seguintes artigos:
 
- [Configurando políticas de segurança na central de segurança do Azure](tutorial-security-policy.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure
- [Segurança de dados da central de segurança do Azure](security-center-data-security.md): saiba como a central de segurança gerencia e protege os dados