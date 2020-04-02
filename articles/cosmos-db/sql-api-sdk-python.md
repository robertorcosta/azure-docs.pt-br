---
title: 'Azure Cosmos DB: API, SDK & recursos do SQL Python'
description: Saiba tudo sobre o SDK e a API do SQL Python, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK do Python para o Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: 090446d286b38a0bfcbde4ed9e77235050b36417
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547713"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>SDK do Python do Azure Cosmos DB para a API do SQL: notas de versão e recursos
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provedor de Recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor a granel - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor a granel - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Baixar o SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Documentação de API**|[Documentação de referência da API do Python](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**Instruções de instalação do SDK**|[Instruções de instalação do SDK do Python](https://github.com/Azure/azure-cosmos-python)|
|**Contribuir para o SDK**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Introdução**|[Introdução ao SDK do Python](sql-api-python-application.md)|
|**Plataforma atual com suporte**|[Python 2.7](https://www.python.org/downloads/) e [Python 3.5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Notas de versão

### <a name="302"></a><a name="3.0.2"/>3.0.2
* Adição de suporte para o tipo de dados MultiPolygon
* Correção de bug na sessão de leitura da política de repetição
* Correção de bug de problemas de preenchimento incorreto durante a decodificação de cadeias de caracteres de base 64

### <a name="301"></a><a name="3.0.1"/>3.0.1
* Correção de bug em LocationCache
* Correção de bug na lógica de repetição de ponto de extremidade
* Correção na documentação

### <a name="300"></a><a name="3.0.0"/>3.0.0
* Suporte para gravações de várias regiões.
* O namespace foi alterado para azure.cosmos.
* Conceitos de coleção e documento renomeados para contêiner e item; document_client renomeado para cosmos_client. 

### <a name="233"></a><a name="2.3.3"/>2.3.3
* Adicionado suporte para proxy
* Adicionado suporte para leitura do feed de alterações
* Adicionado suporte para cabeçalhos de cota de coleção
* Correção de bug de problema de tokens de sessão grandes
* Correção de bug para a API ReadMedia
* Correção de bug no cache de intervalo de chaves de partição

### <a name="232"></a><a name="2.3.2"/>2.3.2
* Adicionado suporte para repetições padrão em problemas de conexão.

### <a name="231"></a><a name="2.3.1"/>2.3.1
* Documentação atualizada para fazer referência ao Azure Cosmos DB em vez do Azure DocumentDB.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Esta versão do SDK requer a versão mais recente do Emulador do Azure Cosmos DB disponível para fazer o download em https://aka.ms/cosmosdb-emulator.

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Correção de bug de dicionário de agregação.
* Correção de bug de barras de filtragem no link de recursos.
* Testes adicionados para codificação Unicode.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Adição de suporte a um novo nível de consistência chamado ConsistentPrefix.


### <a name="210"></a><a name="2.1.0"/>2.1.0
* Suporte adicionado para consultas de agregação (COUNT, MIN, MAX, SUM e AVG).
* Adicionada uma opção para desativar a verificação TLS ao executar contra o Cosmos DB Emulador.
* Removida a restrição do módulo de solicitações dependentes para serem exatamente 2.10.0.
* Taxa de transferência mínima reduzida em coleções particionadas de 10.100 RU/s a 2500 RU/s.
* Adicionado suporte para habilitar o registro em log de script durante a execução do procedimento armazenado.
* Versão da API REST aumentado para '2017-01-19' nesta versão.

### <a name="201"></a><a name="2.0.1"/>2.0.1
* Alterações editoriais feitas a comentários de documentação.

### <a name="200"></a><a name="2.0.0"/>2.0.0
* Suporte ao Python 3.5 adicionado.
* Suporte ao pool de conexões usando um módulo de solicitações adicionado.
* Suporte à consistência da sessão adicionado.
* Suporte às consultas TOP/ORDERBY de coleções particionadas adicionado.

### <a name="190"></a><a name="1.9.0"/>1.9.0
* Suporte à política de repetições para solicitações limitadas adicionado. (As solicitações estranguladas recebem uma taxa de solicitação muito grande exceção, código de erro 429.) Por padrão, o Azure Cosmos DB tenta novamente nove vezes para cada solicitação quando o código de erro 429 é encontrado, honrando a repetiçãoApós o tempo no cabeçalho de resposta. Um intervalo de repetição fixo agora poderá ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy, se você quiser ignorar o tempo retryAfter retornado pelo servidor entre as repetições. O Azure Cosmos DB agora aguarda um período máximo de 30 segundos para cada solicitação que está sendo limitada (independentemente da contagem de repetições) e retorna a resposta com o código de erro 429. Esse tempo também pode ser substituído na propriedade RetryOptions, no objeto ConnectionPolicy.
* O Cosmos DB agora retorna x-ms-throttle-retry-count e x-ms-throttle-retry-wait-time-ms como os cabeçalhos de resposta em cada solicitação para indicar a contagem de repetições restritas e o tempo cumulativo que a solicitação aguardou entre as tentativas.
* A classe RetryPolicy foi removida e a propriedade correspondente (retry_policy) foi exposta na classe document_client. Como alternativa, foi introduzida uma classe RetryOptions, expondo a propriedade RetryOptions na classe ConnectionPolicy, que pode ser usada para substituir algumas das opções de repetição padrão.

### <a name="180"></a><a name="1.8.0"/>1.8.0
* Suporte adicionado para contas de banco de dados de várias regiões.

### <a name="170"></a><a name="1.7.0"/>1.7.0
* Adicionado o suporte para o recurso TTL (tempo de vida) para documentos.

### <a name="161"></a><a name="1.6.1"/>1.6.1
* Correções de bugs relacionadas ao particionamento no lado do servidor a fim de permitir caracteres especiais no caminho de partitionkey.

### <a name="160"></a><a name="1.6.0"/>1.6.0
* Implementação de [coleções particionadas](partition-data.md) e [níveis de desempenho definidos pelo usuário](performance-levels.md). 

### <a name="150"></a><a name="1.5.0"/>1.5.0
* Adicionar resolvedores de hash e intervalo para ajudar com a fragmentação de arquivos em várias partições.

### <a name="142"></a><a name="1.4.2"/>1.4.2
* Implementar o Upsert. Novos métodos UpsertXXX adicionados para dar suporte ao recurso Upsert.
* Implementar o roteamento com base em ID. Nenhuma alteração pública de API, todas as alterações são internas.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Oferece suporte ao índice Geoespacial.
* Valida a propriedade de ID de todos os recursos. As IDs de recursos não podem conter caracteres ?, /, #, \, ou terminar com um espaço.
* Adiciona o novo cabeçalho "andamento de transformação do índice" ao ResourceResponse.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Implementa a política de indexação V2.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Oferece suporte à conexão de proxy.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* SDK DO GA.

## <a name="release--retirement-dates"></a>Datas de lançamento e de baixa
A Microsoft notifica pelo menos **12 meses** antes de desativar um SDK, a fim de realizar uma transição tranquila para uma versão mais recente/com suporte.

Os novos recursos, funcionalidades e otimizações são adicionados apenas ao SDK atual. Portanto, recomendamos que você atualize sempre que possível para a versão do SDK mais recente. 

Qualquer solicitação feita ao Cosmos DB com o uso de um SDK desativado é rejeitada pelo serviço.

> [!WARNING]
> Todas as versões do Python SDK para API SQL antes da versão **1.0.0** foram retiradas em 29 de fevereiro de **2016**. 
> 
> 

> [!WARNING]
> Todas as versões 1.x e 2.x do Python SDK para API SQL serão retiradas em 30 de agosto de **2020**. 
> 
> 

<br/>

| Versão | Data de lançamento | Data de desativação |
| --- | --- | --- |
| [3.0.2](#3.0.2) |15 de novembro de 2018 |--- |
| [3.0.1](#3.0.1) |4 de outubro de 2018 |--- |
| [2.3.3](#2.3.3) |8 de setembro de 2018 |30 de agosto de 2020 |
| [2.3.2](#2.3.2) |08 de maio de 2018 |30 de agosto de 2020 |
| [2.3.1](#2.3.1) |21 de dezembro de 2017 |30 de agosto de 2020 |
| [2.3.0](#2.3.0) |10 de novembro, 2017 |30 de agosto de 2020 |
| [2.2.1](#2.2.1) |29 de setembro de 2017 |30 de agosto de 2020 |
| [2.2.0](#2.2.0) |10 de maio de 2017 |30 de agosto de 2020 |
| [2.1.0](#2.1.0) |1º de maio de 2017 |30 de agosto de 2020 |
| [2.0.1](#2.0.1) |30 de outubro de 2016 |30 de agosto de 2020 |
| [2.0.0](#2.0.0) |29 de setembro de 2016 |30 de agosto de 2020 |
| [1.9.0](#1.9.0) |07 de julho de 2016 |30 de agosto de 2020 |
| [1.8.0](#1.8.0) |14 de junho de 2016 |30 de agosto de 2020 |
| [1.7.0](#1.7.0) |26 de abril de 2016 |30 de agosto de 2020 |
| [1.6.1](#1.6.1) |08 de abril de 2016 |30 de agosto de 2020 |
| [1.6.0](#1.6.0) |29 de março de 2016 |30 de agosto de 2020 |
| [1.5.0](#1.5.0) |03 de janeiro de 2016 |30 de agosto de 2020 |
| [1.4.2](#1.4.2) |06 de outubro de 2015 |30 de agosto de 2020 |
| 1.4.1 |06 de outubro de 2015 |30 de agosto de 2020 |
| [1.2.0](#1.2.0) |06 de agosto de 2015 |30 de agosto de 2020 |
| [1.1.0](#1.1.0) |9 de julho de 2015 |30 de agosto de 2020 |
| [1.0.1](#1.0.1) |25 de maio de 2015 |30 de agosto de 2020 |
| [1.0.0](#1.0.0) |7 de abril de 2015 |30 de agosto de 2020 |
| 0.9.4-prelease |14 de janeiro de 2015 |29 de fevereiro de 2016 |
| 0.9.4-prelease |09 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.2-prelease |25 de novembro de 2014 |29 de fevereiro de 2016 |
| 0.9.1-prelease |23 de setembro de 2014 |29 de fevereiro de 2016 |
| 0.9.0-prelease |21 de agosto de 2014 |29 de fevereiro de 2016 |

## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Confira também
Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

