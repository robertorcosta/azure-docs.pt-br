---
title: Autenticação
description: Aprenda a autenticar o Azure Synapse Analytics usando a autenticação do Azure Active Directory (Azure AD) ou do SQL Server.
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
ms.openlocfilehash: ccc5db828a03c37d3fc4f49b13883ac3eeda2368
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584222"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Autenticação para Azure Synapse Analytics

Aprenda a autenticar o Synapse SQL poool no Azure Synapse usando o Azure Active Directory (Azure AD) ou a autenticação do SQL Server.

Para se conectar a um pool SQL, você deve passar em credenciais de segurança para fins de autenticação. Após estabelecer uma conexão, determinadas configurações de conexão são definidas como parte do estabelecimento de sua sessão de consulta.  

Para obter mais informações sobre segurança e como habilitar conexões ao seu data warehouse, consulte [a proteção de uma documentação de banco de dados](sql-data-warehouse-overview-manage-security.md).

## <a name="sql-authentication"></a>Autenticação SQL

Para se conectar ao pool SQL, você deve fornecer as seguintes informações:

* Nome totalmente qualificado do servidor
* Especificar a autenticação SQL
* Nome de Usuário
* Senha
* Banco de dados padrão (opcional)

Por padrão, sua conexão se conecta ao banco de dados *mestre* e não ao seu banco de dados de usuários. Para se conectar ao banco de dados do usuário, você pode optar por fazer uma de duas opções:

* Especifique o banco de dados padrão ao registrar o servidor com o SQL Server Object Explorer no SSDT, SSMS ou em sua cadeia de conexão do aplicativo. Por exemplo, inclua o parâmetro InitialCatalog em uma conexão ODBC.
* Destaque o banco de dados do usuário antes de criar uma sessão no SSDT.

> [!NOTE]
> A instrução Transact-SQL **USE MyDatabase;** não tem suporte para alterar o banco de dados para uma conexão. Para obter orientações sobre a conexão a um pool SQL com SSDT, consulte o [artigo Consulta com visual Studio.](sql-data-warehouse-query-visual-studio.md)
> 
> 

## <a name="azure-active-directory-azure-ad-authentication"></a>Autenticação do Azure Active Directory (Azure AD)

A autenticação [do Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) é um mecanismo de conexão ao pool SQL usando identidades no Azure Active Directory (Azure AD). Com a autenticação do Azure Active Directory, você pode gerenciar centralmente as identidades de usuários do banco de dados e outros serviços da Microsoft em uma única localização central. O gerenciamento central de ID fornece um único lugar para gerenciar usuários do Azure Synapse e simplifica o gerenciamento de permissões. 

### <a name="benefits"></a>Benefícios

Os benefícios do Azure Active Directory incluem:

* Fornece uma alternativa à autenticação do SQL Server.
* Ajuda a interromper a proliferação de identidades de usuário entre os servidores de banco de dados.
* Permite o rodízio de senhas em um único lugar
* Gerenciar permissões de banco de dados usando grupos externos (Azure AD).
* Elimina o armazenamento de senhas permitindo a autenticação integrada do Windows e outras formas de autenticação compatíveis com o Azure Active Directory.
* Usa usuários de banco de dados independente para autenticar identidades no nível de banco de dados.
* Suporta autenticação baseada em token para aplicativos conectados ao pool SQL.
* Suporta autenticação multifatorial através da autenticação universal do Active Directory para várias ferramentas, incluindo [o SQL Server Management Studio](../../sql-database/sql-database-ssms-mfa-authentication.md) e as Ferramentas de Dados do Servidor [SQL](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json).

> [!NOTE]
> O Azure Active Directory ainda é relativamente novo e tem algumas limitações. Para garantir que o Azure Active Directory seja uma boa opção para seu ambiente, confira [Limitações e recursos do Azure AD](../../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations), especificamente as considerações adicionais.
> 
> 

### <a name="configuration-steps"></a>Etapas de configuração

Siga estas etapas para configurar a autenticação do Azure Active Directory.

1. Criar e popular um Azure Active Directory
2. Opcional: associar ou alterar o Active Directory que está associado atualmente à sua Assinatura do Azure
3. Crie um administrador de diretório ativo do Azure para o Azure Synapse
4. Configurar os computadores cliente
5. Criar usuários de banco de dados independente em seu banco de dados, mapeados para identidades do AD do Azure
6. Conecte-se ao seu pool SQL usando identidades Azure AD

Atualmente, os usuários do Azure Active Directory não são mostrados no Pesquisador de Objetos do SSDT. Como alternativa, exiba os usuários em [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Localização dos detalhes

* As etapas para configurar e usar a autenticação do Azure Active Directory são quase idênticas para o azure SQL Database e o pool Synapse SQL no Azure Synapse. Siga as etapas detalhadas no tópico [Conectando-se ao Banco de Dados SQL ou ao Pool SQL usando a autenticação do diretório ativo do Azure](../../sql-database/sql-database-aad-authentication.md).
* Crie funções de banco de dados personalizadas e adicione usuários às funções. Em seguida, conceda permissões granulares para as funções. Para obter mais informações, consulte o [Guia de introdução às Permissões do Mecanismo do Banco de Dados](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Próximas etapas

Para iniciar a consulta com o Visual Studio e outros aplicativos, consulte [Consulta com o Visual Studio](sql-data-warehouse-query-visual-studio.md).
