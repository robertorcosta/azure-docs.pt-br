---
title: Recursos e SDK do .NET Standard da API de Tabela do Azure Cosmos DB
description: Saiba tudo sobre a API de Tabela do Azure Cosmos DB e o SDK do .NET Standard, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: 48fd85e27feb3d0c7f7f722dbbd502be55684385
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76771585"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>API do .NET Standard de Tabela do Azure Cosmos DB: download e notas de versão
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Download do SDK**|[Nuget](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Amostra**|[Amostra de API da tabela Cosmos DB .NET](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Quickstart**|[Quickstart](create-table-dotnet.md)|
|**Tutorial**|[Tutorial](tutorial-develop-table-dotnet.md)|
|**Framework atualmente com suporte**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|
|**Relatar Problema**|[Relatar Problema](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>Notas de lançamento para a série 2.0.0
A série 2.0.0 leva a dependência do [Microsoft.Azure.Cosmos,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)com melhorias de desempenho e consolidação de namespace ao ponto final do Cosmos DB.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview
* visualização inicial do 2.0.0 Table SDK que leva a dependência do [Microsoft.Azure.Cosmos,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)com melhorias de desempenho e consolidação de namespace para o ponto final do Cosmos DB. A API pública continua a mesma.

## <a name="release-notes-for-100-series"></a>Notas de lançamento para a série 1.0.0
A série 1.0.0 tem a dependência do [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/).

### <a name="105"></a><a name="1.0.5"/>1.0.5
* Introduzir nova configuração em TableClientConfiguration para usar o Executor de Descanso para se comunicar com a API da tabela Cosmos DB

### <a name="105-preview"></a><a name="1.0.5-preview"/>1.0.5-visualização
* Correções de bug

### <a name="104"></a><a name="1.0.4"/>1.0.4
* Correções de bug
* Forneça a opção HttpClientTimeout para RestExecutorConfiguration.

### <a name="104-preview"></a><a name="1.0.4-preview"/>1.0.4-visualização
* Correções de bug
* Forneça a opção HttpClientTimeout para RestExecutorConfiguration.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Correções de bug

### <a name="100"></a><a name="1.0.0"/>1.0.0
* Versão de disponibilidade geral

### <a name="0110-preview"></a><a name="0.11.0-preview"/>0.11.0-visualização
* Foram feitas alterações na forma como o CloudTableClient pode ser configurado. Ele agora leva um objeto TableClientConfiguration durante a construção. TableClientConfiguration fornece diferentes propriedades para configurar o comportamento do cliente, dependendo se o ponto final de destino é a API da tabela Cosmos DB ou a API da Tabela de Armazenamento Do Azure.
* Adicionado suporte ao TableQuery para retornar resultados em ordem ordenada em uma coluna personalizada. Esse recurso só é suportado em pontos finais da Tabela Cosmos DB.
* Suporte adicionado para expor RequestCharges em vários tipos de resultados. Esse recurso só é suportado em pontos finais da Tabela Cosmos DB.

### <a name="0101-preview"></a><a name="0.10.1-preview"/>0.10.1-preview
* Adicione suporte para o token SAS, operações de TablePermissions, ServiceProperties e ServiceStats nos pontos de extremidade da tabela de Armazenamento do Azure. 
   > [!NOTE]
   > Ainda não há suporte para algumas funcionalidades em SDKs anteriores de tabela do Armazenamento do Azure, como a criptografia do lado do cliente.

### <a name="0100-preview"></a><a name="0.10.0-preview"/>0.10.0-preview
* Adicione suporte para as principais operações CRUD, lote e consulta nos pontos de extremidade de tabela do Armazenamento do Azure. 
   > [!NOTE]
   > Ainda não há suporte para algumas funcionalidades em SDKs anteriores de tabela do Armazenamento do Azure, como a criptografia do lado do cliente.

### <a name="091-preview"></a><a name="0.9.1-preview"/>0.9.1-preview
* O SDK do .NET Standard da Tabela do Azure Cosmos DB é uma biblioteca .NET de plataforma cruzada que fornece acesso eficiente ao modelo de dados Tabela no Cosmos DB. A versão inicial dá suporte ao conjunto completo de funcionalidades de CRUD de Tabela e Entidade + Consulta com APIs semelhantes aos do [SDK da Tabela do Cosmos DB para .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Os pontos de extremidade da Tabela de Armazenamento do Azure ainda não têm suporte na versão 0.9.1-preview.

## <a name="release-and-retirement-dates"></a>Datas de lançamento e desativação
A Microsoft notifica pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Esta biblioteca multiplataforma .NET Standard [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) substituirá a biblioteca .NET Framework [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

### <a name="200-series"></a>Série 2.0.0
| Versão | Data de lançamento | Data de desativação |
| --- | --- | --- |
| [2.0.0-preview](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>Série 1.0.0
| Versão | Data de lançamento | Data de desativação |
| --- | --- | --- |
| [1.0.5](#1.0.5) |13 de setembro de 2019 |--- |
| [1.0.5-visualização](#1.0.5-preview) |Auguest 20, 2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4-visualização](#1.0.4-preview) |26 de julho de 2019 |--- |
| 1.0.2-visualização |2 de maio de 2019 |--- |
| [1.0.1](#1.0.1) |19 de abril de 2019 |--- |
| [1.0.0](#1.0.0) |13 de março de 2019 |--- |
| [0.11.0-visualização](#0.11.0-preview) |5 de março de 2019 |--- |
| [0.10.1-preview](#0.10.1-preview) |22 janeiro de 2019 |--- |
| [0.10.0-preview](#0.10.0-preview) |18 de dezembro de 2018 |--- |
| [0.9.1-visualização](#0.9.1-preview) |18 de outubro de 2018 |--- |


## <a name="faq"></a>Perguntas frequentes

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Confira também
Para saber mais sobre a API de Tabela do Azure CosmosDB, consulte [Introdução ao Azure Cosmos DB: API de Tabela](table-introduction.md).
