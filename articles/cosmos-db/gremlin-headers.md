---
title: Cabeçalhos de resposta Azure Cosmos DB Gremlin
description: Documentação de referência para metadados de resposta do servidor que permite a solução adicional de problemas
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 95677f4c45c0213de5ffac5521bac1c6bf7294e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72755088"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Cabeçalhos de resposta do servidor Azure Cosmos DB Gremlin
Este artigo aborda os cabeçalhos que o servidor Gremlin do Cosmos DB retorna ao chamador mediante a execução da solicitação. Esses cabeçalhos são úteis para solucionar problemas de desempenho de solicitação, por meio da criação de um aplicativo que se integra nativamente ao serviço Cosmos DB e simplificação do atendimento ao cliente.

Tenha em mente que, tendo em vista a dependência desses cabeçalhos, você está limitando a portabilidade do seu aplicativo a outras implementações do Gremlin. Em troca, você está ganhando uma integração mais estreita com o Cosmos DB Gremlin. Estes cabeçalhos não são um padrão TinkerPop.

## <a name="headers"></a>headers

| Cabeçalho | Type | Valor de exemplo | Quando incluído | Explicação |
| --- | --- | --- | --- | --- |
| **x-ms-solicitação-charge** | double | 11.3243 | Êxito e Falha | Quantidade de cobrança ou throughput de banco de dados consumido em [unidades de solicitação (RU/s ou RUs)](request-units.md) para uma mensagem de resposta parcial. Este cabeçalho está presente em todas as continuações para solicitações que tenham vários pedaços. Reflete a carga de um pedaço de resposta particular. Somente para solicitações que consistam em um único pedaço de resposta este cabeçalho corresponde ao custo total da travessia. No entanto, para a maioria das travessias complexas esse valor representa um custo parcial. |
| **x-ms-total-solicitação-charge** | double | 423.987 | Êxito e Falha | Quantidade de cobrança ou throughput de banco de dados consumido em [unidades de solicitação (RU/s ou RUs)](request-units.md) para solicitação completa. Este cabeçalho está presente em todas as continuações para solicitações que tenham vários pedaços. Indica carga cumulativa desde o início da solicitação. O valor deste cabeçalho no último pedaço indica carga completa de solicitação. |
| **x-ms-servidor-time-ms** | double | 13.75 | Êxito e Falha | Este cabeçalho está incluído para fins de solução de problemas de latência. Ele indica a quantidade de tempo, em milissegundos, que o servidor Cosmos DB Gremlin levou para executar e produzir uma mensagem de resposta parcial. Usando o valor deste cabeçalho e comparando-o com aplicativos de latência de solicitação global pode calcular a sobrecarga de latência da rede. |
| **x-ms-total-servidor-tempo-ms** | double | 130.512 | Êxito e Falha | Tempo total, em milissegundos, que o servidor Cosmos DB Gremlin levou para executar toda a travessia. Este cabeçalho está incluído em todas as respostas parciais. Representa tempo de execução cumulativo desde o início da solicitação. A última resposta indica tempo total de execução. Este cabeçalho é útil para diferenciar entre cliente e servidor como fonte de latência. Você pode comparar o tempo de execução transversal no cliente com o valor deste cabeçalho. |
| **x-ms-status-code** | long | 200 | Êxito e Falha | O cabeçalho indica o motivo interno para a conclusão ou rescisão da solicitação. A aplicação é aconselhada a olhar para o valor deste cabeçalho e tomar medidas corretivas. |
| **x-ms-substatus-code** | long | 1003 | Somente falha | Cosmos DB é um banco de dados de vários modelos que é construído em cima da camada de armazenamento unificada. Este cabeçalho contém insights adicionais sobre a razão da falha quando a falha ocorre dentro de camadas inferiores de pilha de alta disponibilidade. O aplicativo é aconselhado a armazenar este cabeçalho e usá-lo ao entrar em contato com o suporte ao cliente cosmos DB. O valor deste cabeçalho é útil para o engenheiro Cosmos DB para solução rápida de problemas. |
| **x-ms-retry-after-ms** | string (TimeSpan) | "00:00:03.9500000" | Somente falha | Este cabeçalho é uma representação de seqüência de strings de um tipo .NET [TimeSpan.](https://docs.microsoft.com/dotnet/api/system.timespan) Esse valor só será incluído nas solicitações que não foram previstas no esgotamento do throughput provisionado. O aplicativo deve reenviar a travessia novamente após o período de tempo instruído. |
| **x-ms-activity-id** | corda (Guid) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Êxito e Falha | O cabeçalho contém um identificador exclusivo do lado do servidor de uma solicitação. Cada solicitação é atribuída a um identificador exclusivo pelo servidor para fins de rastreamento. Os aplicativos devem registrar identificadores de atividade retornados pelo servidor para solicitações sobre as quais os clientes podem querer entrar em contato com o suporte ao cliente. O pessoal de suporte do Cosmos DB pode encontrar solicitações específicas por esses identificadores na telemetria de serviço suigo do Cosmos DB. |

## <a name="status-codes"></a>Códigos de status

Os códigos de status mais comuns retornados pelo servidor estão listados abaixo.

| Status | Explicação |
| --- | --- |
| **401** | A `"Unauthorized: Invalid credentials provided"` mensagem de erro é retornada quando a senha de autenticação não corresponde à chave da conta Cosmos DB. Navegue até sua conta Cosmos DB Gremlin no portal Azure e confirme se a chave está correta.|
| **404** | Operações simultâneas que tentam excluir e atualizar a mesma borda ou vértice simultaneamente. A mensagem de erro `"Owner resource does not exist"` indica que a coleção ou o banco de dados especificado  está incorreto nos parâmetros de conexão no formato `/dbs/<database name>/colls/<collection or graph name>`.|
| **408** | `"Server timeout"`indica que a travessia levou mais de **30 segundos** e foi cancelada pelo servidor. Otimize suas travessias para funcionar rapidamente filtrando vértices ou bordas em cada salto de travessia para reduzir o escopo de pesquisa.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."`Isso geralmente acontece quando já existe um vértice ou uma borda com um identificador no gráfico.| 
| **412** | O código de status `"PreconditionFailedException": One of the specified pre-condition is not met`é complementado com mensagem de erro . Este erro é indicativo de uma violação otimista de controle de concorrência entre ler uma borda ou vértice e escrevê-lo de volta para a loja após a modificação. As situações mais comuns quando esse `g.V('identifier').property('name','value')`erro ocorre é a modificação de propriedade, por exemplo. O motor Gremlin lia o vértice, modificava-o e escrevia-o de volta. Se houver outra travessia em execução em paralelo tentando escrever o mesmo vértice ou uma borda, um deles receberá esse erro. A aplicação deve enviar a travessia para o servidor novamente.| 
| **429** | A solicitação foi limitada e deve ser repetida após o valor em **x-ms-retry-after-ms**| 
| **500** | A mensagem de erro que contém `"NotFoundException: Entity with the specified id does not exist in the system."` indica que um banco de dados e/ou coleção foi recriado com o mesmo nome. Este erro desaparecerá dentro de 5 minutos, à medida que a alteração se propaga e invalida os caches em diferentes componentes do Cosmos DB. Para evitar esse problema, use nomes de bancos de dados e coleções exclusivos sempre.| 
| **1000** | Este código de status é retornado quando o servidor analisou com sucesso uma mensagem, mas não foi capaz de executar. Geralmente indica um problema com a consulta.| 
| **1.001** | Esse código é devolvido quando o servidor completa a execução transversal, mas não consegue serializar a resposta de volta ao cliente. Esse erro pode acontecer quando a travessia gera resultados complexos, que são muito grandes ou não estão em conformidade com a especificação do protocolo TinkerPop. O aplicativo deve simplificar a travessia quando encontrar esse erro. | 
| **1.003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"`é devolvido quando a travessia excede o limite de memória permitido. O limite de memória é **de 2 GB** por travessia.| 
| **1.004** | Este código de status indica solicitação de gráfico malformado. A solicitação pode ser malformada quando falha na desserialização, o tipo de não valor está sendo desserializado como tipo de valor ou operação gremlin não suportada solicitada. A aplicação não deve tentar novamente a solicitação porque ela não será bem sucedida. | 
| **1.007** | Normalmente, esse código de `"Could not process request. Underlying connection has been closed."`status é retornado com mensagem de erro . Essa situação pode acontecer se o driver cliente tentar usar uma conexão que está sendo fechada pelo servidor. A aplicação deve tentar novamente a travessia em uma conexão diferente.
| **1.008** | O servidor Cosmos DB Gremlin pode encerrar conexões para reequilibrar o tráfego no cluster. Os drivers clientes devem lidar com essa situação e usar apenas conexões ao vivo para enviar solicitações ao servidor. Ocasionalmente, os motoristas clientes podem não detectar que a conexão foi fechada. Quando o aplicativo encontra `"Connection is too busy. Please retry after sometime or open more connections."` um erro, ele deve tentar novamente atravessar em uma conexão diferente.

## <a name="samples"></a>Exemplos

Um aplicativo cliente de exemplo com base em Gremlin.Net que lê um atributo de status:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Um exemplo que demonstra como ler o atributo de status do cliente Java Gremlin:

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>Próximas etapas
* [Códigos de status HTTP para Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Cabeçalhos de resposta Comuns Azure Cosmos DB REST](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [Requisitos do provedor de driver de gráfico TinkerPop]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
