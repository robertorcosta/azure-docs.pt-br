---
title: Fontes de dados com suporte no Azure Analysis Services | Microsoft Docs
description: Descreve as fontes de fonte de dados com suporte para modelos de dados no Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 79346f0bf80386fb83f55daccda8790652ff8541
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298630"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Fontes de dados com suporte no Azure Analysis Services

Fontes de dados e conectores mostrados no Get Data ou no Assistente de Importação no Visual Studio são mostrados para o Azure Analysis Services e o SQL Server Analysis Services. No entanto, nem todas as fontes de dados e conectores mostrados têm suporte no Azure Analysis Services. Os tipos de fontes de dados que você pode conectar dependem de vários fatores, como nível de compatibilidade do modelo, conectores de dados disponíveis, tipo de autenticação, provedores e suporte de gateway de dados local. 

## <a name="azure-data-sources"></a>Fontes de dados do Azure

|Fonte de dados  |Na memória  |DirectQuery  |
|---------|---------|---------|
|Banco de Dados SQL do Azure<sup>[2](#azsqlmanaged)</sup>     |   Sim      |    Sim      |
|SQL Data Warehouse do Azure     |   Sim      |   Sim       |
|Armazenamento de Blobs do Azure<sup>[1](#tab1400a)</sup>     |   Sim       |    Não      |
|Armazenamento de tabelas do Azure<sup>[1](#tab1400a)</sup>    |   Sim       |    Não      |
|Azure Cosmos DB<sup>[1](#tab1400a)</sup>     |  Sim        |  Não        |
|Azure Data Lake Store (Gen1)<sup>[1](#tab1400a)</sup>, <sup>[4](#gen2)</sup>      |   Sim       |    Não      |
|Azure HDInsight HDFS<sup>[1](#tab1400a)</sup>     |     Sim     |   Não       |
|Azure HDInsight Spark<sup>[1](#tab1400a)</sup>, <sup>[3](#databricks)</sup>     |   Sim       |   Não       |
||||

<a name="tab1400a">1</a> - Tabular 1.400 e modelos superiores somente.   
<a name="azsqlmanaged">2</a> - Há suporte para a Instância Gerenciada do Banco de Dados SQL do Azure. Como a instância gerenciada é executada na VNet do Azure com um endereço IP privado, o ponto de extremidade público deve ser habilitado na instância. Se não estiver habilitado, um gateway de dados local será necessário.    
<a name="databricks">3</a> - No momento, não há suporte para o Azure Databricks usando o conector do Spark.   
<a name="gen2">4</a> - No momento, não há suporte para o ADLS Gen2.


**Provedor**   
Modelos na memória e DirectQuery que se conectam a fontes de dados do Azure usam o .NET Framework Data Provider para SQL Server.

## <a name="other-data-sources"></a>Outras fontes de dados

Conectar a fontes de dados locais e ao servidor de AS do Azure requer um gateway local. Ao usar um gateway, serão necessários provedores de 64 bits.

### <a name="in-memory-and-directquery"></a>Na memória e DirectQuery

|Fonte de dados | Provedor na memória | Provedor do DirectQuery |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0, Provedor Microsoft OLE DB para SQL Server, Provedor de Dados .NET Framework para SQL Server | Provedor de Dados .NET Framework para SQL Server |
| SQL Server Data Warehouse |SQL Server Native Client 11.0, Provedor Microsoft OLE DB para SQL Server, Provedor de Dados .NET Framework para SQL Server | Provedor de Dados .NET Framework para SQL Server |
| Oracle | Provedor de OLE DB para Oracle, Oracle Provedor de Dados para .NET |Provedor de Dados Oracle para .NET |
| Teradata |Provedor OLE DB para Teradata, Provedor de Dados Teradata para .NET |Provedor de Dados Teradata para .NET |
| | | |

### <a name="in-memory-only"></a>Somente na memória

|Fonte de dados  |  
|---------|
|Banco de Dados do Access     |  
|Active Directory<sup>[1](#tab1400b)</sup>     |  
|Serviços de análise     |  
|Analytics Platform System     |  
|Arquivo CSV  |
|Dynamics CRM<sup>[1](#tab1400b)</sup>     |  
|Pasta de trabalho do Excel     |  
|Exchange<sup>[1](#tab1400b)</sup>     |  
|Pasta<sup>[1](#tab1400b)</sup>     |
|IBM Informix<sup>[1](#tab1400b)</sup> (Beta) |
|Documento JSON<sup>[1](#tab1400b)</sup>     |  
|Linhas de binário<sup>[1](#tab1400b)</sup>     | 
|Banco de Dados MySQL     | 
|OData Feed<sup>[1](#tab1400b)</sup>     |  
|Consulta ODBC     | 
|OLE DB     |   
|Banco de dados PostgreSQL<sup>[1](#tab1400b)</sup>    | 
|Objetos do Salesforce<sup>[1](#tab1400b)</sup> |  
|Relatórios do Salesforce<sup>[1](#tab1400b)</sup> |
|SAP HANA<sup>[1](#tab1400b)</sup>    |  
|SAP Business Warehouse<sup>[1](#tab1400b)</sup>    |  
|SharePoint List<sup>[1](#tab1400b)</sup>, <sup>[2](#filesSP)</sup>     |   
|Banco de dados Sybase     |  
|Arquivo TXT  |
|Tabela XML<sup>[1](#tab1400b)</sup>    |  
||
 
<a name="tab1400b">1</a> - Tabular 1.400 e modelos superiores somente.   
<a name="filesSP">2</a> -não há suporte para os arquivos no SharePoint local.

## <a name="specifying-a-different-provider"></a>Especificar um provedor diferente

Os modelos de dados no Azure Analysis Services podem exigir diferentes provedores de dados durante a conexão com certas fontes de dados. Em alguns casos, modelos de tabela que se conectam a fontes de dados usando provedores nativos, como o SQL Server Native Client (SQLNCLI11), podem retornar um erro. Se usar provedores nativos diferentes de SQLOLEDB, você poderá ver a mensagem de erro: **O provedor 'SQLNCLI11.1' não está registrado**. Ou, se tiver um modelo DirectQuery que se conecta a fontes de dados locais e usar provedores nativos, você verá a mensagem de erro: **Erro ao criar o conjunto de linhas do OLE DB. Sintaxe incorreta próxima a 'LIMIT'** .

Ao migrar um modelo de tabela local do SQL Server Analysis Services para o Azure Analysis Services, talvez seja necessário alterar o provedor.

**Para especificar um provedor**

1. No SSDT > **Gerenciador de Modelo de Tabela** > **Fontes de Dados**, clique com o botão direito em uma conexão de fonte de dados e, em seguida, clique em **Editar Fonte de Dados**.
2. Em **Editar Conexão**, clique em **Avançado** para abrir a janela Propriedades avançadas.
3. Em **Definir Propriedades Avançadas** > **Provedores**, selecione o provedor apropriado.

## <a name="impersonation"></a>Representação
Em alguns casos, pode ser necessário especificar uma conta de representação diferente. A conta de representação pode ser especificada no SSDT (Visual Studio) ou no SSMS.

Para fontes de dados locais:

* Se estiver usando a autenticação SQL, a representação deverá ser a Conta de serviço.
* Se estiver usando a autenticação do Windows, defina o usuário/senha do Windows. Para o SQL Server, há suporte para a autenticação do Windows com uma conta de representação específica apenas para modelos de dados na memória.

Para fontes de dados de nuvem:

* Se estiver usando a autenticação SQL, a representação deverá ser a Conta de serviço.

## <a name="oauth-credentials"></a>Credenciais do OAuth

Para modelos de tabela no nível de compatibilidade 1400 e superior, o banco de dados SQL do Azure, o Azure SQL Data Warehouse, o Dynamics 365 e a lista do SharePoint dão suporte a credenciais OAuth. Azure Analysis Services gerencia a atualização de token para fontes de dados OAuth para evitar tempos limite para operações de atualização de execução longa. Para gerar tokens válidos, defina as credenciais usando o SSMS.

## <a name="next-steps"></a>Próximas etapas
[Gateway Local](analysis-services-gateway.md)   
[Gerenciar seu serviço](analysis-services-manage.md)   

