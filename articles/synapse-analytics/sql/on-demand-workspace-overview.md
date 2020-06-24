---
title: SQL sob demanda (versão prévia)
description: Saiba mais sobre o Synapse SQL sob demanda no Azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 3f020abd4d207a49068ccffb9bb7f57b88bc87df
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85206386"
---
# <a name="sql-on-demand-preview-in-azure-synapse-analytics"></a>SQL sob demanda (versão prévia) no Azure Synapse Analytics 

Todo workspace do Azure Synapse Analytics (versão prévia) é fornecido com pontos de extremidade do SQL sob demanda (versão prévia) que você pode usar para consultar dados no lake.

O SQL sob demanda é um serviço de consulta dos dados no data lake. Ele permite que você acesse os dados por meio das seguintes funcionalidades:
 
- Uma sintaxe de T-SQL familiar para consultar dados no local sem a necessidade de copiar nem carregar dados em um repositório especializado. 
- Conectividade integrada por meio da interface T-SQL que oferece uma ampla variedade de ferramentas de consulta ad hoc e de business intelligence, incluindo os drivers mais populares. 

O SQL sob demanda é um sistema de processamento de dados distribuído, criado para dados e computação em grande escala. O SQL sob demanda permite que você analise Big Data em segundos ou minutos, dependendo da carga de trabalho. Graças à tolerância interna a falhas de execução de consulta, o sistema oferece confiabilidade e taxas de êxito altas, até mesmo para consultas de longa execução envolvendo grandes conjuntos de dados.

O SQL sob demanda é sem servidor, portanto, não há infraestrutura a ser instalada nem clusters a serem mantidos. Um ponto de extremidade padrão para esse serviço é fornecido em todos os workspaces do Azure Synapse, de modo que você pode iniciar a consulta de dados assim que o workspace é criado. Não há nenhum encargo referente aos recursos reservados, você está sendo cobrado apenas pelos dados verificados por consultas que você executa, portanto, esse é verdadeiramente um modelo de pagamento conforme o uso.  

Se usar o Apache Spark para o Azure Synapse em seu pipeline de dados para preparação, limpeza ou enriquecimento de dados, você poderá [consultar as tabelas externas do Spark](develop-storage-files-spark-tables.md) que criou no processo, diretamente do SQL sob demanda. Use o [Link Privado](../security/how-to-connect-to-workspace-with-private-links.md) para trazer o ponto de extremidade do SQL sob demanda para dentro da [VNet do workspace gerenciado](../security/synapse-workspace-managed-vnet.md).  

## <a name="who-is-sql-on-demand-for"></a>A quem o SQL sob demanda se destina

Se você precisar explorar dados no data lake, obter insights desses dados ou otimizar o pipeline de transformação de dados existente, poderá se beneficiar do uso do SQL sob demanda. Ele é adequado para os seguintes cenários:

- Descoberta básica e exploração – tire conclusões rapidamente sobre os dados em vários formatos (Parquet, CSV, JSON) no data lake, permitindo que você planeje a melhor maneira de extrair insights deles.
- Data warehouse lógico – forneça uma abstração relacional sobre dados brutos ou díspares sem realocá-los nem transformá-los, permitindo a exibição sempre atualizada dos dados.
- Transformação de dados – maneira simples, escalonável e de alto desempenho para transformar dados no Lake usando o T-SQL, de modo que ele pode ser alimentado para BI e outras ferramentas ou então carregado em um armazenamento de dados relacional (bancos de dados SQL do Synapse, Banco de Dados SQL do Azure etc).

Diferentes funções profissionais podem se beneficiar do SQL sob demanda:

- Os engenheiros de dados podem explorar o lake, transformar e preparar dados usando esse serviço e simplificar os próprios pipelines de transformação de dados. Para obter mais informações, confira este [tutorial](tutorial-data-analyst.md).
- Os cientistas de dados podem tirar conclusões rapidamente com base no conteúdo e na estrutura dos dados no lake, graças a recursos como o OPENROWSET e a inferência automática de esquemas.
- Os analistas de dados podem [explorar dados e tabelas externas do Spark](develop-storage-files-spark-tables.md) criadas por cientistas de dados ou engenheiros de dados usando uma linguagem T-SQL familiar ou as ferramentas favoritas deles, que podem se conectar ao SQL sob demanda.
- Os profissionais de BI podem rapidamente [criar relatórios do Power BI com base nos dados no lake](tutorial-connect-power-bi-desktop.md) e em tabelas do Spark.

## <a name="what-do-i-need-to-do-to-start-using-it"></a>O que preciso fazer para começar a usá-lo?

O ponto de extremidade sob demanda do SQL é fornecido em todos os workspaces do Azure Synapse. Você pode criar um workspace e começar a consultar dados instantaneamente usando ferramentas com as quais você está familiarizado.

## <a name="client-tools"></a>Ferramentas de cliente

O SQL sob demanda permite que as ferramentas existentes de consulta ad hoc e de business intelligence do SQL aproveitem o data lake. Já que ele fornece uma sintaxe de T-SQL familiar, qualquer ferramenta capaz de estabelecer ofertas de conexão TDS pode [se conectar ao SQL do Synapse e consultá-lo](connect-overview.md) sob demanda. Você pode se conectar com Azure Data Studio e executar consultas ad hoc ou conectar-se com Power BI para obter insights em questão de minutos.

## <a name="is-full-t-sql-supported"></a>O T-SQL completo é compatível?

O SQL sob demanda oferece uma área de superfície de consulta T-SQL, que é ligeiramente aprimorada/estendida em alguns aspectos para permitir experiências relacionadas à consulta de dados semiestruturados e não estruturados. Além disso, alguns aspectos da linguagem T-SQL não são compatíveis devido ao design do SQL sob demanda. Por exemplo, atualmente, a funcionalidade DML não é compatível.

- A carga de trabalho pode ser organizada usando conceitos familiares:
- Bancos de dados: o ponto de extremidade sob demanda do SQL pode ter vários bancos de dados.
- Esquemas: dentro de um banco de dados, pode haver um ou vários grupos de propriedade de objeto denominados esquemas.
- Exibições
- Recursos externos: fontes de dados, formatos de arquivo e tabelas

A segurança pode ser imposta usando:

- Logons e usuários
- Credenciais para controlar o acesso a contas de armazenamento
- Concessão, negação e revogação de permissões por nível de objeto
- Integração do Microsoft Azure Active Directory

T-SQL compatível:

- A área de superfície de [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) completa é compatível, incluindo a maioria das funções SQL
- CETAS – CREATE EXTERNAL TABLE AS SELECT
- Instruções DDL relacionadas somente a exibições e segurança

O SQL sob demanda não tem armazenamento local, somente objetos de metadados são armazenados em bancos de dados. Portanto, o T-SQL relacionado aos seguintes conceitos não é compatível:

- Tabelas
- Gatilhos
- Exibições materializadas
- Instruções DDL diferentes daquelas relacionadas a exibições e segurança
- Instruções DML

### <a name="extensions"></a>Extensões

Para permitir uma experiência tranquila para a consulta no local de dados que residem em arquivos no data lake, o SQL sob demanda estende a função [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) existente adicionando as seguintes funcionalidades:

[Consultar vários arquivos ou pastas](develop-storage-files-overview.md#query-multiple-files-or-folders)

[Formato de arquivo PARQUET](develop-storage-files-overview.md#parquet-file-format)

[Opções adicionais para trabalhar com texto delimitado (terminador de campo, terminador de linha, caractere de escape)](develop-storage-files-overview.md#additional-options-for-working-with-delimited-text)

[Ler um subconjunto escolhido de colunas](develop-storage-files-overview.md#read-a-chosen-subset-of-columns)

[Inferência de esquema](develop-storage-files-overview.md#schema-inference)

[Função filename](develop-storage-files-overview.md#filename-function)

[Função filepath](develop-storage-files-overview.md#filepath-function)

[Trabalhar com tipos complexos e estruturas de dados aninhadas ou repetidas](develop-storage-files-overview.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Segurança

O SQL sob demanda oferece mecanismos para proteger o acesso aos seus dados.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e autenticação multifator

O SQL sob demanda permite gerenciar centralmente as identidades de usuário do banco de dados e de outros serviços da Microsoft com a [integração do Azure Active Directory](../../azure-sql/database/authentication-aad-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Esse recurso simplifica o gerenciamento de permissão e aprimora a segurança. O Azure AD (Azure Active Directory) é compatível com [MFA](../../azure-sql/database/authentication-mfa-ssms-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (autenticação multifator) para aumentar a segurança de aplicativos e dados e dá suporte a um processo de logon único.

#### <a name="authentication"></a>Autenticação

A autenticação do SQL sob demanda refere-se a como os usuários comprovam a identidade ao conectarem-se ao ponto de extremidade. Os tipos de autenticação abaixo são compatíveis:

- **Autenticação do SQL**

  Este método de autenticação usa um nome de usuário e senha.

- **Autenticação do Active Directory do Azure**:

  Esse método de autenticação usa identidades gerenciadas pelo Azure Active Directory. Para usuários do Azure AD, a autenticação multifator pode ser habilitada. Use autenticação do Active Directory (segurança integrada) [sempre que possível](/sql/relational-databases/security/choose-an-authentication-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

#### <a name="authorization"></a>Autorização

Autorização refere-se ao que um usuário pode fazer em um banco de dados SQL sob demanda e é controlado pelas associações a uma função de banco de dados e pelas permissões no nível de objeto da conta de usuário.

Se a autenticação do SQL for usada, o usuário do SQL existirá somente no SQL sob demanda e o escopo das permissões será delimitado aos objetos no SQL sob demanda. O acesso a objetos protegíveis em outros serviços (como o Armazenamento do Azure) não pode ser concedido ao usuário do SQL diretamente, pois ele só existe no escopo do SQL sob demanda. O usuário do SQL precisa usar um dos [tipos de autorização compatíveis](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) para acessar os arquivos.

Se a autenticação do Azure AD for usada, um usuário poderá entrar no SQL sob demanda e em outros serviços, como o Armazenamento do Azure, e poderá conceder permissões ao usuário do Azure AD.

### <a name="access-to-storage-accounts"></a>Acesso às contas de armazenamento

Um usuário que fez logon no serviço de SQL sob demanda precisa estar autorizado a acessar e consultar os arquivos no Armazenamento do Azure. O SQL sob demanda dá suporte aos seguintes tipos de autorização:

- A **SAS (Assinatura de Acesso Compartilhado)** fornece acesso delegado aos recursos em uma conta de armazenamento. Com uma SAS, você pode conceder aos clientes acesso aos recursos na conta de armazenamento, sem compartilhar as chaves de conta. Uma SAS oferece controle granular sobre o tipo de acesso que você concede aos clientes que têm uma SAS: intervalo de validade, permissões concedidas, intervalo de endereços IP aceitáveis e protocolo aceitável (HTTPS/HTTP).

- A **Identidade do Usuário**, também conhecida como "passagem", é um tipo de autorização em que a identidade do usuário do Azure AD que fez logon no SQL sob demanda é usada para autorizar o acesso aos dados. Antes de acessar os dados, o administrador do Armazenamento do Azure deve conceder permissões ao usuário do Azure AD a fim de acessar os dados. Esse tipo de autorização usa o usuário do Azure AD que fez logon no SQL sob demanda, portanto, ela não é compatível com tipos de usuário do SQL.

## <a name="next-steps"></a>Próximas etapas
Informações adicionais sobre conexão de ponto de extremidade e consulta de arquivos podem ser encontradas nos seguintes artigos: 
- [Conectar-se ao seu ponto de extremidade](connect-overview.md)
- [Consultar seus arquivos](develop-storage-files-overview.md)
