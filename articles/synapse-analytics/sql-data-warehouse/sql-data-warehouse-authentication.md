---
title: Autenticação
description: Saiba como fazer a autenticação no Azure Synapse Analytics usando a autenticação do Azure AD (Azure Active Directory) ou do SQL Server.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: 29709dc03ee3a06bdf2aec2587909a08ee13504e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85206718"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Autenticar no Azure Synapse Analytics

Saiba como autenticar no SQL do Synapse no Azure Synapse usando a autenticação do AAD (Azure Active Directory) ou do SQL Server.

Para se conectar ao pool de SQL, você precisa inserir credenciais de segurança para fins de autenticação. Após estabelecer uma conexão, determinadas configurações de conexão são definidas como parte do estabelecimento de sua sessão de consulta.  

Para saber mais sobre segurança e como habilitar conexões ao data warehouse em [proteger a documentação de um banco de dados](sql-data-warehouse-overview-manage-security.md).

## <a name="sql-authentication"></a>Autenticação SQL

Para se conectar ao pool de SQL, é necessário fornecer as seguintes informações:

* Nome totalmente qualificado do servidor
* Especificar a autenticação SQL
* Nome de Usuário
* Senha
* Banco de dados padrão (opcional)

Por padrão, a conexão é feita com o banco de dados *mestre* e não com o banco de dados do usuário. Para se conectar ao banco de dados do usuário, você pode optar por fazer uma de duas opções:

* Especifique o banco de dados padrão ao registrar o servidor com o SQL Server Object Explorer no SSDT, SSMS ou em sua cadeia de conexão do aplicativo. Por exemplo, inclua o parâmetro InitialCatalog em uma conexão ODBC.
* Destaque o banco de dados do usuário antes de criar uma sessão no SSDT.

> [!NOTE]
> A instrução Transact-SQL **USE MyDatabase;** não tem suporte para alterar o banco de dados para uma conexão. Para obter as diretrizes de conexão ao pool de SQL com o SSDT, confira o artigo [Consultar com o Visual Studio](sql-data-warehouse-query-visual-studio.md).

## <a name="azure-active-directory-aad-authentication"></a>Autenticação do AAD (Azure Active Directory)

A autenticação do [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) é um mecanismo para se conectar ao pool de SQL usando identidades no Azure AD (Azure Active Directory). Com a autenticação do Azure Active Directory, você pode gerenciar centralmente as identidades de usuários do banco de dados e outros serviços da Microsoft em uma única localização central. O gerenciamento central de IDs fornece um único local para gerenciar os usuários do Azure Synapse e simplifica o gerenciamento de permissões.

### <a name="benefits"></a>Benefícios

Os benefícios do Azure Active Directory incluem:

* Fornece uma alternativa à autenticação do SQL Server.
* Ajuda a impedir a proliferação de identidades de usuário entre servidores.
* Permite o rodízio de senhas em um único lugar
* Gerencia as permissões de banco de dados usando grupos externos (Azure AD).
* Elimina o armazenamento de senhas por meio da habilitação da autenticação integrada do Windows e das formas de autenticação com suporte pelo Azure Active Directory.
* Usa usuários de banco de dados independente para autenticar identidades no nível de banco de dados.
* Dá suporte à autenticação baseada em token em aplicativos que se conectam ao pool de SQL.
* Dá suporte à Autenticação Multifator por meio da Autenticação Universal para várias ferramentas, incluindo o [SQL Server Management Studio](../../azure-sql/database/authentication-mfa-ssms-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e [SQL Server Data Tools](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

> [!NOTE]
> O Azure Active Directory ainda é relativamente novo e tem algumas limitações. Para garantir que o Azure Active Directory seja uma boa opção para seu ambiente, confira [Limitações e recursos do Azure AD](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations), especificamente as considerações adicionais.

### <a name="configuration-steps"></a>Etapas de configuração

Siga estas etapas para configurar a autenticação do Azure Active Directory.

1. Criar e popular um Azure Active Directory
2. Opcional: Associar ou alterar o Active Directory que está associado atualmente à sua Assinatura do Azure
3. Criar um administrador do Azure Active Directory para o Azure Synapse
4. Configurar os computadores cliente
5. Criar usuários de banco de dados independente em seu banco de dados, mapeados para identidades do AD do Azure
6. Conectar ao pool de SQL usando identidades do Azure AD

Atualmente, os usuários do Azure Active Directory não são mostrados no Pesquisador de Objetos do SSDT. Como alternativa, exiba os usuários em [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="find-the-details"></a>Localização dos detalhes

* As etapas para configurar e usar a autenticação do Azure Active Directory são quase idênticas para o Banco de Dados SQL do Azure e o SQL do Synapse no Azure Synapse. Siga as etapas detalhadas no tópico [Conexão ao Banco de Dados SQL ou ao Pool de SQL usando a autenticação do Azure Active Directory](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
* Crie funções de banco de dados personalizadas e adicione usuários às funções. Em seguida, conceda permissões granulares para as funções. Para obter mais informações, consulte o [Guia de introdução às Permissões do Mecanismo do Banco de Dados](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Próximas etapas

Para começar a fazer consultas com o Visual Studio e outros aplicativos, confira [Consulta com o Visual Studio](sql-data-warehouse-query-visual-studio.md).
