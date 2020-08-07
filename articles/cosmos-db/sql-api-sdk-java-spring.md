---
title: Recursos e notas de versão do Spring data Azure Cosmos DB para API do SQL
description: Saiba tudo sobre o Azure Cosmos DB de dados Spring para a API do SQL, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK do Java assíncrono do SQL Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/05/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: f94ea37966c4942f72ae2d7b701e742891fa6cd2
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854179"
---
# <a name="spring-data-azure-cosmos-db-for-core-sql-api-release-notes-and-resources"></a>API do Spring data Azure Cosmos DB para núcleo (SQL): notas de versão e recursos
> [!div class="op_single_selector"]
> * [SDK v3 do .NET](sql-api-sdk-dotnet-standard.md)
> * [SDK do .NET v2](sql-api-sdk-dotnet.md)
> * [SDK v2 do .NET Core](sql-api-sdk-dotnet-core.md)
> * [SDK v2 do feed de alterações do .NET](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK do Java v4](sql-api-sdk-java-v4.md)
> * [SDK do Java Assíncrono v2](sql-api-sdk-async-java.md)
> * [SDK do Java Síncrono v2](sql-api-sdk-java.md)
> * [Spring Data](sql-api-sdk-java-spring.md)
> * [Conector do Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provedor de recursos REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor em massa-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa – Java](sql-api-sdk-bulk-executor-java.md)

O Spring data Azure Cosmos DB for Core (SQL) permite que os desenvolvedores utilizem Azure Cosmos DB em aplicativos Spring. O Spring data Azure Cosmos DB expõe a interface Spring data para manipular bancos de dados e coleções, trabalhar com documentos e emitir consultas. As APIs de sincronização e assíncrona (reativa) têm suporte no mesmo artefato Maven. 

O [Spring Framework](https://spring.io/projects/spring-framework) é um modelo de programação e configuração que simplifica o desenvolvimento de aplicativos Java. Para citar o site da organização, a primavera simplifica o "encanamento" de aplicativos usando a injeção de dependência. Muitos desenvolvedores, como o Spring, porque a criação e o teste de aplicativos se tornam mais simples. O [Spring boot](https://spring.io/projects/spring-boot) estende essa ideia de lidar com o direcionamento com um olho voltado para o desenvolvimento de aplicativos Web e de microserviços. O [Spring data](https://spring.io/projects/spring-data) é um modelo de programação para acessar armazenamentos de dados, como Azure Cosmos DB do contexto de um aplicativo Spring ou Spring boot. 

Você pode usar o Spring data Azure Cosmos DB em seus aplicativos de [nuvem Spring do Azure](https://azure.microsoft.com/services/spring-cloud/) .

> [!IMPORTANT]  
> Estas notas de versão são para Azure Cosmos DB API do SQL somente. 
>
> Os guias a seguir oferecem suporte a Spring data em outras APIs de Azure Cosmos DB:
> * [Spring data para Apache Cassandra com Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring data MongoDB com Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Gremlin de Spring data com Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Quer começar a usar rapidamente?
> 1. Instale o [runtime mínimo com suporte para Java, JDK 8](/java/azure/jdk/?view=azure-java-stable) para poder usar o SDK.
> 2. Criar um aplicativo Spring data Azure Cosmos DB usando o iniciador – [é fácil](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)!
> 3. Trabalhe com o [Guia de desenvolvedores do Spring Data Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) que percorre as solicitações de Azure Cosmos DB básicas.
>
> Você pode criar aplicativos de inicialização do Spring boot rapidamente com o [Spring Initializr](https://start.spring.io/)!
>

## <a name="helpful-content"></a>Conteúdo útil

| Conteúdo | Link |
|---|---|
| **Baixe o SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**Documentação da API** | [Documentação de referência do Spring data Azure Cosmos DB]() |
|**Contribuir para o SDK** | [Repositório de Azure Cosmos DB de dados Spring no GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Iniciador do Spring boot**| [Biblioteca de cliente inicial do Spring boot do Azure Cosmos DB para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Exemplo de aplicativo de TODO o Spring com Azure Cosmos DB**| [Experiência Java de ponta a ponta no serviço de aplicativo Linux (parte 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Guia de desenvolvedores** | [Guia de desenvolvedores do Spring data Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Guia para usar o Starter** | [Como usar o Inicializador do Spring Boot com a API SQL do Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [Repositório GitHub para o iniciador do Azure Spring boot Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Exemplo com serviços de aplicativos** | [Como usar o Spring e o Cosmos DB com o Serviço de Aplicativo no Linux](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Exemplo de aplicativo TODO](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Histórico de versões

### <a name="230-2020-05-21"></a>2.3.0 (2020-05-21)
#### <a name="new-features"></a>Novos recursos
* Atualizar a versão do Spring boot para 2.3.0 
#### <a name="key-bug-fixes"></a>Correções de bug de chave

### <a name="225-2020-05-19"></a>2.2.5 (2020-05-19)
#### <a name="new-features"></a>Novos recursos
* Versão do Azure Cosmos DB atualizada para v 3.7.3
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Contém correções de vazamento de memória e atualizações de versão de sub-rede do cosmos SDK v 3.7.3 

### <a name="224-2020-04-06"></a>2.2.4 (2020-04-06)
#### <a name="new-features"></a>Novos recursos
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Corrigido o sinalizador allowTelemetry para levar em conta de CosmosDbConfig
* Propriedade de TTL fixa no contêiner

### <a name="223-2020-02-25"></a>2.2.3 (2020-02-25)
#### <a name="new-features"></a>Novos recursos
* Adicionado novo findAll por API de chave de partição
* Versão atualizada do Azure-Cosmos para 3.7.0
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* CollectionName fixo-> ContainerName
* EntityClass corrigidos & domainClass-> DomainType
* Corrigido "coleção de entidades de retorno salva pelo repositório em vez da entidade de entrada"

### <a name="2110-2020-02-25"></a>2.1.10 (2020-02-25)
#### <a name="new-features"></a>Novos recursos
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Correção reportada para "coleção de entidade de retorno salva pelo repositório em vez da entidade de entrada"

### <a name="222-2020-01-15"></a>2.2.2 (2020-01-15)
#### <a name="new-features"></a>Novos recursos
* Versão atualizada do Azure-Cosmos para v 3.6.0
#### <a name="key-bug-fixes"></a>Correções de bug de chave

### <a name="221-2019-12-31"></a>2.2.1 (2019-12-31)
#### <a name="new-features"></a>Novos recursos
* Atualizada a versão do SDK do Cosmos DB para o 3.5.0
* Campo de anotação adicionado para habilitar/desabilitar a coleta de criação automática
* Melhor tratamento de exceção, exposto CosmosClientException por meio de CosmosDBAccessException
* Exposto requestCharge e ActivityId por meio de ResponseDiagnostics
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Correções de atualização do SDK 3.5.0 "quando Cosmos DB cabeçalho de resposta HTTP é maior que 8192 bytes", "ConsistencyPolicy. defaultConsistencyLevel () falha na desatualização limitada e no prefixo consistente"
* Correção de comportamento de APIs findById, retorno vazio não encontrado, em vez de lançar exceção
* Correção de um bug em que a classificação não foi aplicada na próxima página ao usar CosmosPageRequest

### <a name="219-2019-12-26"></a>2.1.9 (2019-12-26)
#### <a name="new-features"></a>Novos recursos
* Campo de anotação adicionado para habilitar/desabilitar a coleta de criação automática
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Correção de comportamento de APIs findById, retorno vazio não encontrado, em vez de lançar exceção

### <a name="220-2019-10-21"></a>2.2.0 (2019-10-21)
#### <a name="new-features"></a>Novos recursos
* Concluir o suporte ao repositório Cosmos reativo
* Cosmos DB solicitar a cadeia de diagnóstico e suporte a métricas de consulta
* Cosmos DB atualização da versão do SDK para 3.3.1
* Atualização da versão do Spring Framework para o 5.2.0. RELEASE
* Atualização da versão do Spring data Commons para o 2.2.0. RELEASE
* FindByIdAndPartitionKey, APIs deleteByIdAndPartitionKey adicionadas
* Dependência removida do Azure-doumentdb
* Remarcando o DocumentDb para Cosmos
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Fixed "a classificação gera uma exceção quando pageSize é menor que o total de itens no repositório"

### <a name="218-2019-10-18"></a>2.1.8 (2019-10-18)
#### <a name="new-features"></a>Novos recursos
* Preterir APIs do banco de documentos
* Foram adicionadas findByIdAndPartitionKey, APIs deleteByIdAndPartitionKey.
* Adicionado bloqueio otimista com base em _etag
* Expressão SPeL habilitada para o nome da coleção de documentos
* Melhorias do objectmapper.
#### <a name="key-bug-fixes"></a>Correções de bug de chave

### <a name="217-2019-10-18"></a>2.1.7 (2019-10-18)
#### <a name="new-features"></a>Novos recursos
* Dependência do cosmos SDK v3 adicionada
* Repositório Cosmos reativo adicionado
* Implementação atualizada de DocumentDbTemplate para usar o SDK do cosmos v3.
* Outras alterações de configuração para o suporte ao repositório Cosmos reativo.
#### <a name="key-bug-fixes"></a>Correções de bug de chave

### <a name="212-2019-03-19"></a>2.1.2 (2019-03-19)
#### <a name="new-features"></a>Novos recursos
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Remover dependência de applicationInsights para
    * Risco potencial de dependências de poluição
    * Incompatibilidade do Java 11
    * Evitar o impacto potencial no desempenho na CPU e/ou na memória.

### <a name="207-2019-03-20"></a>2.0.7 (2019-03-20)
#### <a name="new-features"></a>Novos recursos
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Backport remove dependência de applicationInsights para
    * Risco potencial de dependências de poluição
    * Incompatibilidade do Java 11
    * Evitar o impacto potencial no desempenho na CPU e/ou na memória.

### <a name="211-2019-03-07"></a>2.1.1 (2019-03-07)
#### <a name="new-features"></a>Novos recursos
* Atualizar a versão mestre para 2.1.1
#### <a name="key-bug-fixes"></a>Correções de bug de chave

### <a name="206-2019-03-07"></a>2.0.6 (2019-03-07)
#### <a name="new-features"></a>Novos recursos
* Ignorar todas as exceções da telemetria
#### <a name="key-bug-fixes"></a>Correções de bug de chave

### <a name="210-2018-12-17"></a>2.1.0 (2018-12-17)
#### <a name="new-features"></a>Novos recursos
* Atualizar a versão para 2.1.0 para resolver o problema
#### <a name="key-bug-fixes"></a>Correções de bug de chave

### <a name="205-2018-09-13"></a>2.0.5 (2018-09-13)
#### <a name="new-features"></a>Novos recursos
* Adicionar palavra-chave Exists, startsWith
* Atualizar Leiame
#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Corrigir "não é possível chamar o autohref diretamente para a entidade"
* Corrigir "findAll falhará se a coleção não for criada"

### <a name="204-pre-release-2018-08-23"></a>2.0.4 (pré-lançamento) (2018-08-23)
#### <a name="new-features"></a>Novos recursos
* Renomeando pacote do documentdb para cosmosdb,
* Adicione um novo recurso de palavra-chave do método de consulta, 16 palavras-chave da API do SQL com suporte.
* Adicione um novo recurso de consulta com paginação e classificação.
* Simplifique a configuração do Spring-data-cosmosdb.
* Adicione a API deletecollection e deleteAll.

#### <a name="key-bug-fixes"></a>Correções de bug de chave
* Correção de bug e aprimoramento de defeito.

## <a name="faq"></a>Perguntas frequentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Cosmos DB, consulte a página de serviço do [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Para saber mais sobre o Spring Framework, confira o [projeto Home Page](https://spring.io/projects/spring-framework).

Para saber mais sobre o Spring boot, consulte o [projeto Home Page](https://spring.io/projects/spring-boot).

Para saber mais sobre o Spring data, consulte o [projeto Home Page](https://spring.io/projects/spring-data).