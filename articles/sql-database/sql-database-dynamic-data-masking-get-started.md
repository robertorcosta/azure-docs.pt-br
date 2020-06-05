---
title: Mascaramento de dados dinâmicos
description: A máscara de dados dinâmicos limita a exposição de dados confidenciais por intermédio do seu mascaramento para usuários sem privilégios no SQL Database e Azure Synapse
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/06/2020
tags: azure-synpase
ms.openlocfilehash: 9ebbd773d56a54114f4bbb9b0db7ffddd2fbf893
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727022"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-azure-synapse-analytics"></a>Mascaramento de dados dinâmicos para Banco de Dados SQL do Azure e Azure Synapse Analytics

A máscara de dados dinâmicos do Banco de Dados SQL limita a exposição de dados confidenciais através do mascaramento dos dados para usuários sem privilégios. 

A Máscara de dados dinâmica ajuda a impedir o acesso não autorizado a dados confidenciais, permitindo que os clientes especifiquem qual a quantidade de dados confidenciais revelar, com impacto mínimo sobre a camada de aplicativo. É um recurso de segurança baseado em políticas que oculta os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos do banco de dados designados, sendo que os dados no banco de dados não são alterados.

Por exemplo, um representante de serviço em um centro de chamada pode identificar os chamadores por vários dígitos do seu número de cartão de crédito, mas os itens de dados não devem ser totalmente expostos para o representante de serviço. Uma regra de mascaramento pode ser definida para mascarar tudo menos os quatro últimos dígitos de qualquer número de cartão de crédito no conjunto de resultados de qualquer consulta. Como outro exemplo, uma máscara de dados apropriada pode ser definida para proteger os dados de informações de identificação pessoal (PII), de forma que um desenvolvedor possa consultar os ambientes de produção para fins de solução de problemas sem violar os regulamentos de conformidade.

## <a name="dynamic-data-masking-basics"></a>Aspectos básicos do mascaramento de dados dinâmicos

É possível configurar uma política de máscara de dados dinâmicos no Portal do Azure selecionando a operação máscara de dados dinâmicos na folha de configuração do Banco de Dados SQL ou na folha de configurações. Este recurso não pode ser definido com o uso do portal para Azure Synapse (use o PowerShell ou a API REST)

### <a name="dynamic-data-masking-permissions"></a>Permissões de mascaramento de dados dinâmico

A Máscara de Dados Dinâmicos pode ser configurada por meio do administrador do Banco de Dados SQL do Azure, do administrador do servidor ou de funções do [Gerenciador de Segurança do SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager).

### <a name="dynamic-data-masking-policy"></a>Política de mascaramento de dados dinâmico

* **Usuários SQL excluídos do mascaramento** – um conjunto de usuários do SQL ou de identidades do AAD que obtém dados não mascarados nos resultados da consulta SQL. Usuários com privilégios de administrador sempre são excluídos do mascaramento e veem os dados originais sem qualquer máscara.
* **Regras de mascaramento** – um conjunto de regras que definem os campos designados que serão mascarados e a função de mascaramento que será usada. Os campos correspondentes podem ser definidos usando um nome de esquema de banco de dados, um nome de tabela e um nome da coluna.
* **Funções de mascaramento** : um conjunto de métodos que controlam a exposição de dados para cenários diferentes.

| Função de mascaramento | Lógica de mascaramento |
| --- | --- |
| **Default** |**Mascaramento completo de acordo com os tipos de dados dos campos designados**<br/><br/>• Use XXXX ou menos Xs se o tamanho do campo tiver menos de quatro caracteres para os tipos de dados da cadeia de caracteres (nchar, ntext, nvarchar).<br/>• Use um valor zero para tipos de dados numéricos (bigint, bit, decimal, int, money, numérico, smallint, smallmoney, tinyint, float, real).<br/>• Use 01-01-1900 para os tipos de dados de data/hora (data, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Para SQL variant, o valor padrão do tipo atual é usado.<br/>• Para XML, o documento \<mascarado/> é usado.<br/>• Use um valor vazio para tipos de dados especiais (timestamp table, hierarchyid, GUID, binary, image, varbinary spatial types). |
| **Cartão de crédito** |**Método de mascaramento que expõe os últimos quatro dígitos dos campos designados** e adiciona uma cadeia de caracteres constante como um prefixo na forma de um cartão de crédito.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **Email** |**Método de mascaramento que expõe a primeira letra e substitui o domínio por XXX.com** usando um prefixo da cadeia de caracteres constante na forma de um endereço de email.<br/><br/>aXX@XXXX.com |
| **Número aleatório** |**Método de mascaramento que gera um número aleatório** de acordo com os limites selecionados e os tipos de dados reais. Se os limites designados forem iguais, a função de mascaramento será um número constante.<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Texto personalizado** |**Método de mascaramento que expõe os primeiros e os últimos caracteres** e adiciona uma cadeia de caracteres de preenchimento personalizado no meio. Se a cadeia de caracteres original for menor do que o prefixo e o sufixo expostos, somente a cadeia de caracteres de preenchimento será usada. <br/>prefixo[preenchimento]sufixo<br/><br/>![Painel de navegação](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Campos recomendados para mascarar

O mecanismo de recomendações DDM sinaliza determinados campos do banco de dados como potencialmente confidenciais, que podem ser bons candidatos para mascaramento. Na folha Mascaramento de Dados Dinâmicos no portal, você verá as colunas recomendadas para seu banco de dados. Basta clicar em **Adicionar Máscara** para uma ou mais colunas e em **Salvar** para aplicar uma máscara a esses campos.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configurar a máscara de dados dinâmica para o banco de dados usando cmdlets do PowerShell

### <a name="data-masking-policy"></a>Política de mascaramento de dados

- [Get-AzSqlDatabaseDataMaskingPolicy](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingPolicy)
- [Set-AzSqlDatabaseDataMaskingPolicy](https://docs.microsoft.com/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingPolicy)

### <a name="data-masking-rules"></a>Regras de mascaramento dos dados

- [Get-AzSqlDatabaseDataMaskingRule](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingRule)
- [New-AzSqlDatabaseDataMaskingRule](https://docs.microsoft.com/powershell/module/az.sql/New-AzSqlDatabaseDataMaskingRule)
- [Remove-AzSqlDatabaseDataMaskingRule](https://docs.microsoft.com/powershell/module/az.sql/Remove-AzSqlDatabaseDataMaskingRule)
- [Set-AzSqlDatabaseDataMaskingRule](https://docs.microsoft.com/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingRule)

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configurar a máscara de dados dinâmica para o banco de dados usando a API REST

Você pode usar a API REST para gerenciar programaticamente a política e as regras de mascaramento de dados. A API REST publicada dá suporte às seguintes operações:

### <a name="data-masking-policy"></a>Política de mascaramento de dados

- [Criar ou Atualizar](https://docs.microsoft.com/rest/api/sql/datamaskingpolicies/createorupdate): Cria ou atualiza o rótulo de confidencialidade da coluna especificada.
- [Obter](https://docs.microsoft.com/rest/api/sql/datamaskingpolicies/get): Obtém uma política de mascaramento de dados de banco de dados. 

### <a name="data-masking-rules"></a>Regras de mascaramento dos dados

- [Criar ou Atualizar](https://docs.microsoft.com/rest/api/sql/datamaskingrules/createorupdate): Cria ou atualiza uma regra de mascaramento de dados de banco de dados.
- [Listar por banco de dados](https://docs.microsoft.com/rest/api/sql/datamaskingrules/listbydatabase): Obtém uma lista de regras de mascaramento de dados do banco de dados.
