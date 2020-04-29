---
title: Autenticação
description: Saiba como se autenticar no Azure Synapse Analytics usando o Azure Active Directory (Azure AD) ou a autenticação SQL Server.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: ed8dd902ac490b4e6a0f172029bf3ffa1d44acee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81251836"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Autenticar no Azure Synapse Analytics

Saiba como autenticar para análise de SQL no Azure Synapse usando a autenticação Azure Active Directory (AAD) ou SQL Server.

Para se conectar a um pool do SQL, você deve passar as credenciais de segurança para fins de autenticação. Após estabelecer uma conexão, determinadas configurações de conexão são definidas como parte do estabelecimento de sua sessão de consulta.  

Para obter mais informações sobre segurança e como habilitar conexões com seu data warehouse, consulte [Securing a Database Documentation](sql-data-warehouse-overview-manage-security.md).

## <a name="sql-authentication"></a>Autenticação SQL

Para se conectar ao pool do SQL, você deve fornecer as seguintes informações:

* Nome totalmente qualificado do servidor
* Especificar a autenticação SQL
* Nome de Usuário
* Senha
* Banco de dados padrão (opcional)

Por padrão, a conexão se conecta ao banco de dados *mestre* e não ao seu banco de dados de usuário. Para se conectar ao banco de dados do usuário, você pode optar por fazer uma de duas opções:

* Especifique o banco de dados padrão ao registrar o servidor com o SQL Server Object Explorer no SSDT, SSMS ou em sua cadeia de conexão do aplicativo. Por exemplo, inclua o parâmetro InitialCatalog em uma conexão ODBC.
* Destaque o banco de dados do usuário antes de criar uma sessão no SSDT.

> [!NOTE]
> A instrução Transact-SQL **USE MyDatabase;** não tem suporte para alterar o banco de dados para uma conexão. Para obter orientações sobre como se conectar a um pool SQL com SSDT, consulte o artigo [consulta com o Visual Studio](sql-data-warehouse-query-visual-studio.md) .

## <a name="azure-active-directory-aad-authentication"></a>Autenticação do AAD (Azure Active Directory)

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) autenticação é um mecanismo de conexão com o pool SQL usando identidades no Azure Active Directory (AD do Azure). Com a autenticação do Azure Active Directory, você pode gerenciar centralmente as identidades de usuários do banco de dados e outros serviços da Microsoft em uma única localização central. O gerenciamento de identificação central fornece um único local para gerenciar usuários do Azure Synapse e simplifica o gerenciamento de permissões.

### <a name="benefits"></a>Vantagens

Os benefícios do Azure Active Directory incluem:

* Fornece uma alternativa à autenticação do SQL Server.
* Ajuda a interromper a proliferação de identidades de usuário entre os servidores de banco de dados.
* Permite o rodízio de senhas em um único lugar
* Gerenciar permissões de banco de dados usando grupos externos (AD do Azure).
* Elimina o armazenamento de senhas permitindo a autenticação integrada do Windows e outras formas de autenticação compatíveis com o Azure Active Directory.
* Usa usuários de banco de dados independente para autenticar identidades no nível de banco de dados.
* Dá suporte à autenticação baseada em token para aplicativos que se conectam ao pool do SQL.
* Dá suporte à autenticação multifator por meio do Active Directory autenticação universal para várias ferramentas, incluindo [SQL Server Management Studio](../../sql-database/sql-database-ssms-mfa-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e [SQL Server Data Tools](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

> [!NOTE]
> O Azure Active Directory ainda é relativamente novo e tem algumas limitações. Para garantir que o Azure Active Directory seja uma boa opção para seu ambiente, confira [Limitações e recursos do Azure AD](../../sql-database/sql-database-aad-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations), especificamente as considerações adicionais.

### <a name="configuration-steps"></a>Etapas de configuração

Siga estas etapas para configurar a autenticação do Azure Active Directory.

1. Criar e popular um Azure Active Directory
2. Opcional: associar ou alterar o Active Directory que está associado atualmente à sua Assinatura do Azure
3. Criar um administrador de Azure Active Directory para o Azure Synapse
4. Configurar os computadores cliente
5. Criar usuários de banco de dados independente em seu banco de dados, mapeados para identidades do AD do Azure
6. Conectar-se ao seu pool SQL usando identidades do Azure AD

Atualmente, os usuários do Azure Active Directory não são mostrados no Pesquisador de Objetos do SSDT. Como alternativa, exiba os usuários em [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="find-the-details"></a>Localização dos detalhes

* As etapas para configurar e usar a autenticação Azure Active Directory são quase idênticas para o banco de dados SQL do Azure e a análise de SQL no Azure Synapse. Siga as etapas detalhadas no tópico [conectando-se ao banco de dados SQL ou ao pool do SQL usando Azure Active Directory autenticação](../../sql-database/sql-database-aad-authentication.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
* Crie funções de banco de dados personalizadas e adicione usuários às funções. Em seguida, conceda permissões granulares para as funções. Para obter mais informações, consulte o [Guia de introdução às Permissões do Mecanismo do Banco de Dados](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Próximas etapas

Para iniciar a consulta com o Visual Studio e outros aplicativos, consulte [consultar com o Visual Studio](sql-data-warehouse-query-visual-studio.md).
