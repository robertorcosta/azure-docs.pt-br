---
title: Fontes de dados com suporte no Azure Analysis Services | Microsoft Docs
description: Descreve fontes de dados e conectores suportados para modelos de dados tabular 1200 e mais altos nos Serviços de Análise do Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f65d8fa2c2e522c718c637e32defc4c56fca8364
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461650"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Fontes de dados com suporte no Azure Analysis Services

As fontes de dados e conectores mostrados no Get Data ou Table Import Wizard no Visual Studio com os projetos de Serviços de Análise são mostrados tanto para os Serviços de Análise do Azure quanto para os Serviços de Análise de Servidores SQL. No entanto, nem todas as fontes de dados e conectores mostrados são suportados nos Serviços de Análise do Azure. Os tipos de fontes de dados a que você pode se conectar dependem de muitos fatores, como nível de compatibilidade do modelo, conectores de dados disponíveis, tipo de autenticação e suporte a gateway de dados on-premises. As tabelas a seguir descrevem fontes de dados suportadas para os Serviços de Análise do Azure.

## <a name="azure-data-sources"></a>Fontes de dados do Azure

|Fonte de dados  |Na memória  |DirectQuery  |Observações |
|---------|---------|---------|---------|
|Banco de Dados SQL do Azure      |   Sim      |    Sim      |<sup>[2,](#azprovider)</sup> <sup> [3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL Data Warehouse)      |   Sim      |   Sim       |<sup>[2](#azprovider)</sup>|
|Armazenamento do Blobs do Azure      |   Sim       |    Não      | <sup>[1](#tab1400a)</sup> |
|Armazenamento de Tabelas do Azure     |   Sim       |    Não      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  Sim        |  Não        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   Sim       |    Não      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   Sim       |    Não      |<sup>[1,](#tab1400a)</sup> <sup> [5](#gen2)</sup>|
|Azure HDInsight HDFS    |     Sim     |   Não       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Sim       |   Não       |<sup>[1,](#tab1400a)</sup> <sup> [4](#databricks)</sup>|
||||

**Notas:**   
<a name="tab1400a">1</a> - Tabular 1.400 e modelos superiores somente.  
<a name="azprovider">2</a> - Quando especificado como fonte de dados *do provedor* em modelos tabular 1200 e superiores, ambos os modelos de memória e DirectQuery exigem o Microsoft OLE DB Driver para OQL Server MSOLEDBSQL (recomendado), SQL Server Native Client 11.0 ou .NET Framework Data Provider for SQL Server.    
<a name="azsqlmanaged">3</a> - A instância gerenciada do banco de dados SQL do Azure é suportada. Como a instância gerenciada é executada dentro do Azure VNet com um endereço IP privado, o ponto final público deve ser ativado na instância. Se não estiver habilitado, é necessário um [gateway de dados on-premises.](analysis-services-gateway.md)    
<a name="databricks">4</a> - Os tijolos de dados do Azure usando o conector Spark não são suportados no momento.   
<a name="gen2">5</a> - O conector ADLS Gen2 não é suportado no momento, no entanto, o conector Azure Blob Storage pode ser usado com uma fonte de dados ADLS Gen2.   

## <a name="other-data-sources"></a>Outras fontes de dados

|Fonte de dados | Na memória | DirectQuery |Observações   |
|  --- | --- | --- | --- |
|Banco de dados do Access     |  Sim | Não |  |
|Active Directory     |  Sim | Não | <sup>[6](#tab1400b)</sup>  |
|Serviços de análise     |  Sim | Não |  |
|Sistema de plataforma de análise     |  Sim | Não |  |
|Arquivo CSV  |Sim | Não |  |
|Dynamics 365     |  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Pasta de trabalho do Excel     |  Sim | Não |  |
|Exchange      |  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Pasta      |Sim | Não | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |Sim | Não |  |
|Documento JSON      |  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Linhas de binário      | Sim | Não | <sup>[6](#tab1400b)</sup> |
|Banco de dados MySQL     | Sim | Não |  |
|Feed OData      |  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Consulta ODBC     | Sim | Não |  |
|OLE DB     |   Sim | Não |  |
|Oracle  | Sim  |Sim  | <sup>[9](#oracle)</sup> |
|Banco de dados PostgreSQL   | Sim | Não | <sup>[6](#tab1400b)</sup> |
|Objetos do Salesforce|  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Relatórios do Salesforce |Sim | Não | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  Sim | Não |  |
|SAP Business Warehouse    |  Sim | Não | <sup>[6](#tab1400b)</sup> |
|Lista do SharePoint      |   Sim | Não | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|SQL Server |Sim   | Sim  | <sup>[7,](#sqlim)</sup> <sup> [8](#instgw)</sup> | 
|SQL Server Data Warehouse |Sim   | Sim  | <sup>[7,](#sqlim)</sup> <sup> [8](#instgw)</sup> |
|Banco de dados Sybase     |  Sim | Não |  |
|Teradata | Sim  | Sim  | <sup>[10](#teradata)</sup> |
|Arquivo TXT  |Sim | Não |  |
|Tabela XML    |  Sim | Não | <sup>[6](#tab1400b)</sup> |
| | | |

**Notas:**   
<a name="tab1400b">6</a> - Tabular 1400 e somente modelos superiores.  
<a name="sqlim">7</a> - Quando especificado como fonte de dados *do provedor* em modelos tabular 1200 e superiores, especifique o Driver Microsoft OLE DB para SQL Server MSOLEDBSQL (recomendado), SQL Server Native Client 11.0 ou .NET Framework Data Provider for SQL Server.  
<a name="instgw">8</a> - Se especificar o MSOLEDBSQL como provedor de dados, pode ser necessário baixar e instalar o [Microsoft OLE DB Driver for SQL Server](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) no mesmo computador do gateway de dados on-premises.  
<a name="oracle">9</a> - Para modelos tabulares 1200, ou como fonte de dados *do provedor* em modelos tabular 1400+, especifique o Oracle Data Provider para .NET.  
<a name="teradata">10</a> - Para modelos tabulares 1200, ou como fonte de dados *do provedor* em modelos tabular 1400+, especifique Teradata Data Provider para .NET.   
<a name="filesSP">11</a> - Os arquivos no SharePoint no local não são suportados.

A conexão com fontes de dados locais de um servidor do Azure Analysis Services requer um [gateway on-premises](analysis-services-gateway.md). Ao usar um gateway, serão necessários provedores de 64 bits. 

## <a name="understanding-providers"></a>Entendendo os provedores

Ao criar projetos tabulares 1400 e modelos superiores no Visual Studio, por padrão, você não especifica um provedor de dados ao se conectar a uma fonte de dados usando **Get Data**. Os modelos tabular 1400 e superiores usam conectores [de consulta de energia](/power-query/power-query-what-is-power-query) para gerenciar conexões, consultas de dados e mashups entre a fonte de dados e os Serviços de Análise. Essas são às vezes referidas como conexões *estruturadas* de origem de dados em que as configurações de propriedade de conexão são definidas para você. No entanto, você pode habilitar fontes de dados legados. Quando ativado, você pode usar o **Table Import Wizard** para se conectar a determinadas fontes de dados tradicionalmente suportadas em modelos tabular 1200 e modelos inferiores como fontes de dados *legados*ou *provedores.* Quando especificado como uma fonte de dados do provedor, você pode especificar um provedor de dados específico e outras propriedades avançadas de conexão. Por exemplo, você pode se conectar a um SQL Server Data Warehouse no local ou até mesmo a um banco de dados SQL do Azure como uma fonte de dados legado. Em seguida, você pode selecionar o Driver OLE DB para provedor de dados SQL Server MSOLEDBSQL. Neste caso, a seleção de um provedor de dados OLE DB pode fornecer um desempenho melhor sobre o conector Power Query. 

Ao usar o Assistente de Importação de Tabela no Visual Studio, as conexões com qualquer fonte de dados requerem um provedor de dados. Um provedor de dados padrão é selecionado para você. Você pode alterar o provedor de dados se necessário. O tipo de provedor que você escolhe pode depender do desempenho, se o modelo está usando ou não o armazenamento na memória ou o DirectQuery, e para qual plataforma de Serviços de Análise você implanta seu modelo.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Especificar fontes de dados do provedor em projetos tabular 1400 e modelos mais altos

Para habilitar as fontes de dados do provedor, no Visual Studio, clique em **Ferramentas** > **Serviços de** > análise**de opções Tabular** > **Data Import,** selecione **Habilitar fontes de dados legados**.

![Habilite fontes de dados legados](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Com fontes de dados herdadas habilitadas, no **Tabular Model Explorer,** clique com o botão direito do mouse **Sobre a** > importação de fontes**de dados da fonte de dados (Legacy)**.

![Fontes de dados legados no Tabular Model Explorer](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Assim como em projetos de modelo tabular 1200, use **table import wizard** para se conectar a uma fonte de dados. Na página de conexão, clique em **Avançado**. Especifique o provedor de dados e outras configurações de conexão no **Set Advanced Properties**.

![Fontes de dados legados Propriedades avançadas](media/analysis-services-datasource/aas-import-legacy-advanced.png)


## <a name="impersonation"></a>Representação
Em alguns casos, pode ser necessário especificar uma conta de representação diferente. A conta de personificação pode ser especificada no Visual Studio ou SSMS.

Para fontes de dados locais:

* Se estiver usando a autenticação SQL, a representação deverá ser a Conta de serviço.
* Se estiver usando a autenticação do Windows, defina o usuário/senha do Windows. Para o SQL Server, há suporte para a autenticação do Windows com uma conta de representação específica apenas para modelos de dados na memória.

Para fontes de dados de nuvem:

* Se estiver usando a autenticação SQL, a representação deverá ser a Conta de serviço.

## <a name="oauth-credentials"></a>Credenciais OAuth

Para modelos tabulares no nível de compatibilidade 1400 e maior que o uso do modo de memória, o Azure SQL Database, o Azure Synapse Analytics (SQL Data Warehouse), o Dynamics 365 e o SharePoint List suportam credenciais OAuth. O Azure Analysis Services gerencia a atualização de tokens para fontes de dados OAuth para evitar intervalos para operações de atualização de longa duração. Para gerar tokens válidos, defina credenciais usando SSMS.

O modo consulta direta não é suportado com credenciais OAuth.

## <a name="next-steps"></a>Próximas etapas
[Gateway no local](analysis-services-gateway.md)   
[Gerenciar seu serviço](analysis-services-manage.md)   

