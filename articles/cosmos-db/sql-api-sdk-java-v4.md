---
title: 'SDK do Java v4 do Azure Cosmos DB para a API do SQL: notas de versão e recursos'
description: Saiba tudo sobre o SDK do Java v4 do Azure Cosmos DB para API e SDK do SQL, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK do Java Assíncrono do Azure Cosmos DB SQL.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: ccc872b24c78fbdf6e55673f9d1f78efc0647895
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537877"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>SDK do Java v4 do Azure Cosmos DB para API do Core (SQL): notas de versão e recursos
> [!div class="op_single_selector"]
> * [SDK v3 do .NET](sql-api-sdk-dotnet-standard.md)
> * [SDK do .NET v2](sql-api-sdk-dotnet.md)
> * [SDK v2 do .NET Core](sql-api-sdk-dotnet-core.md)
> * [SDK v2 do feed de alterações do .NET](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK do Java v4](sql-api-sdk-java-v4.md)
> * [SDK do Java Assíncrono v2](sql-api-sdk-async-java.md)
> * [SDK do Java Síncrono v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provedor de recursos REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor em massa-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa – Java](sql-api-sdk-bulk-executor-java.md)

O SDK do Java v4 do Azure Cosmos DB para Core (SQL) combina uma API assíncrona e uma API síncrona em um artefato Maven. O SDK da v4 oferece desempenho aprimorado, novos recursos de API e suporte a Async com base no Project Reactor e na [biblioteca Netty](https://netty.io/). Os usuários podem esperar um desempenho aprimorado com o SKD do Java v4 do Azure Cosmos DB em comparação com o [SDK do Java Assíncrono v2 do Azure Cosmos DB](sql-api-sdk-async-java.md) e o [SDK do Java Síncrono v2 do Azure Cosmos DB](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Essas notas de versão são apenas para o SDK do Java v4 do Azure Cosmos DB. Se você estiver usando uma versão mais antiga do que a v4, confira o guia [Migrar para o SDK do Java v4 do Azure Cosmos DB](migrate-java-v4-sdk.md) para obter ajuda na atualização da versão 4.
>
> Aqui estão três etapas para começar rápido!
> 1. Instale o [runtime mínimo com suporte para Java, JDK 8](/java/azure/jdk/?view=azure-java-stable) para poder usar o SDK.
> 2. Trabalhe no [Guia de Início Rápido do SDK do Java v4 do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java), que fornece acesso ao artefato Maven, e percorre as solicitações básicas do Azure Cosmos DB.
> 3. Leia as [dicas de desempenho](performance-tips-java-sdk-v4-sql.md) e os guias de [solução de problemas](troubleshoot-java-sdk-v4-sql.md) do SDK do Java v4 do Azure Cosmos DB para otimizar o SDK para seu aplicativo.
>
> Os [workshops e laboratórios do Azure Cosmos DB](https://aka.ms/cosmosworkshop) são outro ótimo recurso para aprender como usar o SDK do Java v4 do Azure Cosmos DB!
>

| |  |
|---|---|
| **Baixe o SDK** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Documentação da API** | [Documentação de referência de API Java](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable) |
|**Contribuir para o SDK** | [SDK do Azure para repositório central do Java no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**Introdução** | [Início Rápido: crie um aplicativo Java para gerenciar dados da API do SQL do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) · [Repositório do GitHub com código de início rápido](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Amostras de código básico** | [Banco de dados do Azure Cosmos DB: Exemplos em Java para a API do SQL](sql-api-java-sdk-samples.md) · [Repositório GitHub com código de exemplo](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Aplicativo de console com feed de alterações**| [Feed de alterações – amostra do SDK do Java v4](create-sql-api-java-changefeed.md) · [Repositório do GitHub com código de amostra](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Exemplo de aplicativo Web**| [Compilar um aplicativo Web com o SDK do Java v4](sql-api-java-application.md) · [Repositório GitHub com código de exemplo](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Dicas de desempenho**| [Dicas de desempenho para o SDK do Java v4](performance-tips-java-sdk-v4-sql.md)| 
| **Solução de problemas** | [Solucionar problemas do SDK do Java v4](troubleshoot-java-sdk-v4-sql.md) |
| **Migrar para v4 de um SDK mais antigo** | [Migrar para o SDK do Java V4](migrate-java-v4-sdk.md) |
| **runtime mínimo com suporte**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Workshops e laboratórios do Azure Cosmos DB** |[Home page dos workshops do Cosmos DB](https://aka.ms/cosmosworkshop)

## <a name="release-history"></a>Histórico de versões

### <a name="420-2020-07-14"></a>4.2.0 (2020-07-14)
* API habilitada para log de script adicionada ao `CosmosStoredProcedureRequestOptions` .
* Atualizado o `DirectConnectionConfig` padrão `idleEndpointTimeout` para 1h e o padrão `connectTimeout` para 5s.
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Corrigido o problema em que o `GatewayConnectionConfig` `idleConnectionTimeout` estava substituindo `DirectConnectionConfig` `idleConnectionTimeout` .
* Corrigidas as `responseContinuationTokenLimitInKb` APIs Get e Set no `CosmosQueryRequestOptions` .
* Corrigido o problema na consulta e no feed de alterações ao recriar a coleção com o mesmo nome.
* Corrigido o problema com a primeira consulta que gera ClassCastException.
* Corrigido o problema com a consulta order by lançando NullPointerException.
* Corrigido o problema na manipulação de solicitações canceladas no modo direto, fazendo com que o reator `onErrorDropped` seja chamado. 

### <a name="410-2020-06-25"></a>4.1.0 (2020-06-25)
#### <a name="new-features"></a>Novos recursos
* Adicionado suporte para `GROUP BY` consulta.
* Aumentou o valor padrão de maxConnectionsPerEndpoint para 130 em DirectConnectionConfig.
* Aumentou o valor padrão de maxRequestsPerConnection para 30 em DirectConnectionConfig.
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Correção de problemas com a consulta order by retornando resultados duplicados ao retomar usando o token de continuação. 
* Problemas corrigidos com a consulta de valor retornando valores nulos para o objeto aninhado.
* Corrigida a exceção de ponteiro NULL no Gerenciador de solicitações em RntbdClientChannelPool.

### <a name="401-2020-06-10"></a>4.0.1 (2020-06-10)
#### <a name="new-features"></a>Novos recursos
* `QueryRequestOptions` renomeado para `CosmosQueryRequestOptions`.
* Atualizado `ChangeFeedProcessorBuilder` para o padrão do construtor.
* Atualizado `CosmosPermissionProperties` com novas APIs de nome de contêiner e recursos filho.
* Foram adicionadas mais amostras & documentos aprimorados para o `CosmosClientBuilder` . 
* APIs atualizadas `CosmosDatabase`  &  `CosmosContainer` com taxa de transferência para o suporte a autoescala/piloto. 
* `CosmosClientException` renomeado para `CosmosException`. 
* Substituído `AccessCondition`  &  `AccessConditionType` por `ifMatchETag()`  &  `ifNoneMatchETag()` APIs. 
* Mescla todos os `Cosmos*AsyncResponse`  &  `CosmosResponse` tipos a um único `CosmosResponse` tipo.
* `CosmosResponseDiagnostics` renomeado para `CosmosDiagnostics`.  
* Encapsulado `FeedResponseDiagnostics` `CosmosDiagnostics` . 
* Dependência removida `jackson` do Azure-cosmos & contando com o Azure-Core. 
* Substituído `CosmosKeyCredential` pelo `AzureKeyCredential` tipo. 
* APIs adicionadas `ProxyOptions` ao `GatewayConnectionConfig` . 
* Atualizado o SDK para usar o `Instant` tipo em vez de `OffsetDateTime` . 
* Novo tipo de enumeração adicionado `OperationKind` . 
* `FeedOptions` renomeado para `QueryRequestOptions`. 
* APIs adicionadas `getETag()`  &  `getTimestamp()` a `Cosmos*Properties` tipos. 
* Informações adicionadas `userAgent` em `CosmosException`  &  `CosmosDiagnostics` . 
* Novo caractere de linha atualizado em `Diagnostics` para o novo caractere de linha do sistema. 
* APIs removidas `readAll*` , usar consulta Selecione todas as APIs em vez disso.
* `ChangeFeedProcessor`API de latência de estimativa adicionada.   
* Adicionado suporte de provisionamento de taxa de transferência de autoobjetivo/AutoPilot ao SDK.  
* Substituído `ConnectionPolicy` por novas configurações de conexão. APIs expostas `DirectConnectionConfig`  &  `GatewayConnectionConfig` por meio `CosmosClientBuilder` do para configurações de conexão do modo de gateway & direta.
* Movido `JsonSerializable`  &  `Resource` para o pacote de implementação. 
* `contentResponseOnWriteEnabled`API adicionada para CosmosClientBuilder que desabilita o conteúdo completo de resposta em operações de gravação.
* APIs expostas `getETag()` em tipos de resposta.
* Movido `CosmosAuthorizationTokenResolver` para a implementação. 
* API renomeada `preferredLocations`  &  `multipleWriteLocations` para `preferredRegions`  &  `multipleWriteRegions` . 
* Atualizado `reactor-core` para 3.3.5. Release, `reactor-netty` para 0.9.7. Release & `netty` para as versões 4.1.49. final. 
* Adicionado suporte para `analyticalStoreTimeToLive` no SDK.     
* `CosmosClientException`estende `AzureException` . 
* Removidas `maxItemCount`  &  `requestContinuationToken` APIs do `FeedOptions` em vez disso usando `byPage()` APIs do `CosmosPagedFlux`  &  `CosmosPagedIterable` .
* Introduzido `CosmosPermissionProperties` na superfície pública para `Permission` APIs.
* `SqlParameterList`Tipo removido & substituído por`List`
* Corrigidos vários vazamentos de memória no cliente TCP direto. 
* Suporte adicionado para `DISTINCT` consultas. 
* Dependências externas removidas em `fasterxml.uuid, guava, commons-io, commons-collection4, commons-text` .  
* Movido `CosmosPagedFlux`  &  `CosmosPagedIterable` para o `utils` pacote. 
* Uma sub-rede atualizada para 4.1.45. final & reator de projeto para a versão 3.3.3.
* Contratos REST públicos atualizados para `Final` classes.
* Suporte adicionado para diagnóstico avançado para operações de ponto.
* Pacote atualizado para`com.azure.cosmos`
* `models`Pacote adicionado para contratos de modelo/REST
* `utils`Pacote adicionado para `CosmosPagedFlux`  &  `CosmosPagedIterable` tipos. 
* APIs públicas atualizadas para usar `Duration` em todo o SDK.
* Adicionados todos os contratos REST ao `models` pacote.
* `RetryOptions` foi renomeado para `ThrottlingRetryOptions`.
* `CosmosPagedFlux`  &  `CosmosPagedIterable` Tipos de paginação adicionados para APIs de consulta. 
* Adicionado suporte para compartilhamento de TransportClient em várias instâncias do CosmosClients usando uma nova API no`CosmosClientBuilder#connectionSharingAcrossClientsEnabled(true)`
* Otimizações de consulta removendo serialização/desserialização duplas. 
* Otimizações de cabeçalhos de resposta removendo a cópia desnecessária de volta e para trás. 
* Serialização otimizada `ByteBuffer` /desserialização removendo instanciações de cadeia de caracteres intermediárias.
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* ConnectionPolicy corrigida `toString()` exceção de ponteiro nulo.
* Corrigido o problema com a análise de resultados da consulta em caso de consultas de ordem de valor por. 
* Correção de problemas de vazamento de soquete com o cliente TCP direto.
* Corrigido `orderByQuery` com bug de token de continuação.
* `ChangeFeedProcessor`correção de bug para tratamento de divisões de partição & quando a partição não foi encontrada.
* `ChangeFeedProcessor`correção de bug ao sincronizar atualizações de concessão em threads diferentes.
* Correção da condição de corrida causando `ArrayIndexOutOfBound` exceção em StoreReader

## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Confira também
Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).