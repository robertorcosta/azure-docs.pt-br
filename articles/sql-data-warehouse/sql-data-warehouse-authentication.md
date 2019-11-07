---
title: Autenticação
description: Saiba como autenticar no SQL Data Warehouse do Azure usando a autenticação do Microsoft Azure Active Directory ou do Microsoft SQL Server.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: fda29e432fbd952261893f3c32a4df7b9990ae66
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692924"
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>Autenticar no SQL Data Warehouse do Azure
Saiba como autenticar no SQL Data Warehouse do Azure usando a autenticação do Microsoft Azure Active Directory ou do Microsoft SQL Server.

Para se conectar ao SQL Data Warehouse, você precisa inserir credenciais de segurança para fins de autenticação. Após estabelecer uma conexão, determinadas configurações de conexão são definidas como parte do estabelecimento de sua sessão de consulta.  

Para saber mais sobre segurança e como habilitar conexões com seu data warehouse, confira [Proteger um banco de dados no SQL Data Warehouse][Secure a database in SQL Data Warehouse].

## <a name="sql-authentication"></a>Autenticação do SQL
Para se conectar ao SQL Data Warehouse, deve fornecer as seguintes informações:

* Nome totalmente qualificado do servidor
* Especificar a autenticação SQL
* Nome de Usuário
* Senha
* Banco de dados padrão (opcional)

Por padrão, a conexão se conecta ao banco de dados *mestre* e não ao banco de dados do usuário. Para se conectar ao banco de dados do usuário, você pode optar por fazer uma de duas opções:

* Especifique o banco de dados padrão ao registrar o servidor com o SQL Server Object Explorer no SSDT, SSMS ou em sua cadeia de conexão do aplicativo. Por exemplo, inclua o parâmetro InitialCatalog em uma conexão ODBC.
* Destaque o banco de dados do usuário antes de criar uma sessão no SSDT.

> [!NOTE]
> A instrução Transact-SQL **USE MyDatabase;** não tem suporte para alterar o banco de dados para uma conexão. Para obter as diretrizes de conexão do SQL Data Warehouse com SSDT, consulte o artigo [Consulta com o Visual Studio][Query with Visual Studio] .
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Autenticação do AAD (Azure Active Directory)
[Azure Active Directory][What is Azure Active Directory] autenticação é um mecanismo de conexão com o SQL data warehouse do Microsoft Azure usando identidades no Azure Active Directory (AD do Azure). Com a autenticação do Azure Active Directory, você pode gerenciar centralmente as identidades de usuários do banco de dados e outros serviços da Microsoft em uma única localização central. O gerenciamento central de ID fornece um único local para gerenciar usuários do SQL Data Warehouse e simplifica o gerenciamento de permissões. 

### <a name="benefits"></a>Benefícios
Os benefícios do Azure Active Directory incluem:

* Fornece uma alternativa à autenticação do SQL Server.
* Ajuda a interromper a proliferação de identidades de usuário entre os servidores de banco de dados.
* Permite o rodízio de senhas em um único lugar
* Gerencia as permissões de banco de dados usando grupos externos (AAD).
* Elimina o armazenamento de senhas permitindo a autenticação integrada do Windows e outras formas de autenticação compatíveis com o Azure Active Directory.
* Usa usuários de banco de dados independente para autenticar identidades no nível de banco de dados.
* Dá suporte à autenticação baseada em token para aplicativos que se conectam ao SQL Data Warehouse.
* Dá suporte à autenticação multifator por meio do Active Directory autenticação universal para várias ferramentas, incluindo [SQL Server Management Studio](../sql-database/sql-database-ssms-mfa-authentication.md) e [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json).

> [!NOTE]
> O Azure Active Directory ainda é relativamente novo e tem algumas limitações. Para garantir que o Azure Active Directory seja uma boa opção para seu ambiente, confira [Limitações e recursos do Azure AD][Azure AD features and limitations], especificamente as considerações adicionais.
> 
> 

### <a name="configuration-steps"></a>Etapas da configuração
Siga estas etapas para configurar a autenticação do Azure Active Directory.

1. Criar e popular um Azure Active Directory
2. Opcional: associar ou alterar o Active Directory que está associado atualmente à sua Assinatura do Azure
3. Criar um administrador do Azure Active Directory para o SQL Data Warehouse do Azure
4. Configurar os computadores cliente
5. Criar usuários de banco de dados independente em seu banco de dados, mapeados para identidades do AD do Azure
6. Conectar-se ao data warehouse usando identidades do Azure AD

Atualmente, os usuários do Azure Active Directory não são mostrados no Pesquisador de Objetos do SSDT. Como alternativa, exiba os usuários em [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Localização dos detalhes
* As etapas para configurar e usar a autenticação do Azure Active Directory são quase idênticas para o Banco de Dados SQL do Azure e o SQL Data Warehouse do Azure. Siga as etapas detalhadas no tópico [Conexão ao Banco de Dados SQL ou ao SQL Data Warehouse usando a autenticação do Azure Active Directory](../sql-database/sql-database-aad-authentication.md).
* Crie funções de banco de dados personalizadas e adicione usuários às funções. Em seguida, conceda permissões granulares para as funções. Para obter mais informações, consulte o [Guia de introdução às Permissões do Mecanismo do Banco de Dados](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Próximas etapas
Para começar a consultar o data warehouse com o Visual Studio e outros aplicativos, consulte [Consulta com o Visual Studio][Query with Visual Studio].

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]:../active-directory/fundamentals/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
