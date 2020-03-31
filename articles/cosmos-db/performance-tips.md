---
title: Dicas de desempenho do Microsoft Azure Cosmos DB para .NET
description: Aprenda as opções de configuração do cliente para melhorar o desempenho do Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: sngun
ms.openlocfilehash: 68a9a7d5f90831bb2e0c9284cb71ae4b92edffad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131416"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Dicas de desempenho para o Azure Cosmos DB e .NET

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

O Azure Cosmos DB é um banco de dados distribuído rápido e flexível que pode ser dimensionado perfeitamente com garantia de latência e produtividade. Você não precisa fazer grandes alterações de arquitetura ou escrever código complexo para dimensionar seu banco de dados com o Azure Cosmos DB. Aumentar e diminuir a escala é tão fácil quanto fazer uma única chamada de API. Para saber mais, veja [como provisionar a taxa de transferência do contêiner](how-to-provision-container-throughput.md) ou [como provisionar a taxa de transferência do banco de dados](how-to-provision-database-throughput.md). Mas como o Azure Cosmos DB é acessado por meio de chamadas de rede, existem otimizações do lado do cliente que você pode fazer para atingir o desempenho máximo quando você usa o [SQL .NET SDK](sql-api-sdk-dotnet-standard.md).

Então, se você está tentando melhorar o desempenho do seu banco de dados, considere essas opções:

## <a name="hosting-recommendations"></a>Recomendações de hospedagem

**Para obter cargas de trabalho intensivas em consulta, use o Windows 64 bits em vez do processamento de host de 32 bits do Linux ou Windows**

Recomendamos o processamento do host do Windows de 64 bits para melhorar o desempenho. O SQL SDK inclui um ServiceInterop.dll nativo para analisar e otimizar consultas localmente. ServiceInterop.dll é suportado apenas na plataforma Windows x64. Para Linux e outras plataformas não suportadas onde o ServiceInterop.dll não está disponível, uma chamada de rede adicional é feita para o gateway para obter a consulta otimizada. Os seguintes tipos de aplicativos usam processamento de host de 32 bits por padrão. Para alterar o processamento do host para processamento de 64 bits, siga estas etapas, com base no tipo de aplicativo:

- Para aplicativos executáveis, você pode alterar o processamento do host definindo o destino da [plataforma](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019) para **x64** na janela Propriedades do **Projeto,** na guia **Construir.**

- Para projetos de teste baseados em VSTest, você pode alterar o processamento do host selecionando **a** > arquitetura padrão do processador**de testes** > **como X64** no menu Teste do Estúdio Visual. **Test**

- Para aplicativos ASP.NET Web implantados localmente, você pode alterar o processamento de host selecionando **Use a versão de 64 bits do IIS Express para sites e projetos** em Projetos de**Opções de** >  **Ferramentas** > e**Projetos Web**de**Soluções** > .

- Para ASP.NET aplicativos web implantados no Azure, você pode alterar o processamento do host selecionando a plataforma **de 64 bits** nas **configurações de aplicativo** no portal Azure.

> [!NOTE] 
> Por padrão, novos projetos do Visual Studio são definidos **como Qualquer CPU**. Recomendamos que você defina seu projeto **como x64** para que ele não mude para **x86**. Um projeto definido para **qualquer CPU** pode facilmente mudar para **x86** se uma dependência somente x86 for adicionada.<br/>
> ServiceInterop.dll precisa estar na pasta da a que o DLL SDK está sendo executado. Isso só deve ser uma preocupação se você copiar manualmente DLLs ou tiver sistemas personalizados de compilação/implantação.
    
**Ativar a coleta de lixo do lado do servidor (GC)**

Reduzir a frequência da coleta de lixo pode ajudar em alguns casos. Em .NET, definir `true` [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) para .

**Dimensione a carga de trabalho do seu cliente**

Se você estiver testando em altos níveis de throughput (mais de 50.000 RU/s), o aplicativo cliente pode se tornar o gargalo devido à redução da cpu ou da rede da máquina. Se você chegar a este ponto, poderá continuar aumentando a conta do Azure Cosmos DB ainda mais distribuindo seus aplicativos cliente entre vários servidores.

> [!NOTE] 
> O alto uso da CPU pode causar maior latência e solicitar exceções de tempo limite.

## <a name="networking"></a>Rede
<a id="direct-connection"></a>

**Política de conexão: usar o modo de conexão direta**

Como um cliente se conecta ao Azure Cosmos DB tem implicações importantes de desempenho, especialmente para a latência observada do lado do cliente. Existem duas configurações principais disponíveis para configurar a política de conexão do cliente: o *modo* de conexão e o *protocolo*de conexão .  Os dois modos disponíveis são:

   * Modo de gateway
      
     O modo Gateway é suportado em todas as plataformas SDK e é o padrão configurado para o [Microsoft.Azure.DocumentDB SDK](sql-api-sdk-dotnet.md). Se o aplicativo for executado em uma rede corporativa com restrições rigorosas de firewall, o modo gateway é a melhor escolha porque usa a porta HTTPS padrão e um único ponto final. A troca de desempenho, no entanto, é que o modo gateway envolve um salto de rede adicional cada vez que os dados são lidos ou gravados no Azure Cosmos DB. Assim, o modo direto oferece melhor desempenho porque há menos saltos de rede. Também recomendamos o modo de conexão de gateway quando você executa aplicativos em ambientes que têm um número limitado de conexões de soquete.

     Quando você usar o SDK em Funções Azure, particularmente no [plano de consumo,](../azure-functions/functions-scale.md#consumption-plan)esteja ciente dos limites atuais [de conexões](../azure-functions/manage-connections.md). Nesse caso, o modo gateway pode ser melhor se você também estiver trabalhando com outros clientes baseados em HTTP dentro do aplicativo Azure Functions.

   * Modo direto

     O modo direto suporta a conectividade através do protocolo TCP e é o modo de conectividade padrão se você estiver usando o [Microsoft.Azure.Cosmos/.NET V3 SDK](sql-api-sdk-dotnet-standard.md).

No modo gateway, o Azure Cosmos DB usa as portas 443 e portas 10250, 10255 e 10256 quando você está usando a API Azure Cosmos DB para MongoDB. A porta 10250 mapeia uma instância padrão do MongoDB sem geo-replicação. Portas 10255 e 10256 mapa para a instância MongoDB que tem geo-replicação.
     
Quando você usa o TCP no modo direto, além das portas de gateway, você precisa garantir que o intervalo de portas entre 10000 e 20000 esteja aberto porque o Azure Cosmos DB usa portas TCP dinâmicas. Se essas portas não estiverem abertas e você tentar usar o TCP, você receberá um erro 503 Service Unavailable. Esta tabela mostra os modos de conectividade disponíveis para várias APIs e as portas de serviço usadas para cada API:

|Modo da conexão  |Protocolo com Suporte  |SDKs com suporte  |Porta/serviço de API  |
|---------|---------|---------|---------|
|Gateway  |   HTTPS    |  Todos os SDKs    |   SQL (443), MongoDB (10250, 10255, 10256), Tabela (443), Cassandra (10350), Gráfico (443)    |
|Direto    |     TCP    |  SDK .NET    | Portos na faixa de 10000 a 20000 |

O Azure Cosmos DB oferece um modelo de programação RESTful simples e aberto sobre HTTPS. Além disso, ele oferece um protocolo TCP eficiente que também é RESTful em seu modelo de comunicação e está disponível por meio do SDK do cliente .NET. O protocolo TCP usa SSL para autenticação inicial e tráfego criptografado. Para ter um melhor desempenho, use o protocolo TCP quando possível.

Para SDK V3, você configura o modo `CosmosClient` de `CosmosClientOptions`conexão ao criar a ocorrência, em . Lembre-se que o modo direto é o padrão.

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
CosmosClient client = new CosmosClient(serviceEndpoint, authKey,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

Para o Microsoft.Azure.DocumentDB SDK, você configura o modo `DocumentClient` de conexão durante a construção da instância usando o `ConnectionPolicy` parâmetro. Se você usar o modo direto, você também pode definir o `Protocol` usando o `ConnectionPolicy` parâmetro.

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

Como o TCP é suportado apenas no modo direto, se você usar o modo `Protocol` gateway, o protocolo HTTPS é sempre usado para se comunicar com o gateway e o valor é `ConnectionPolicy` ignorado.

![A política de conexão Azure Cosmos DB](./media/performance-tips/connection-policy.png)

**Chamar OpenAsync para evitar a latência de inicialização na primeira solicitação**

Por padrão, a primeira solicitação tem maior latência porque precisa buscar a tabela de roteamento de endereços. Quando você usar [o SDK V2](sql-api-sdk-dotnet.md), ligue `OpenAsync()` uma vez durante a inicialização para evitar essa latência de inicialização na primeira solicitação:

    await client.OpenAsync();

> [!NOTE] 
> `OpenAsync`gerará solicitações para obter a tabela de roteamento de endereços para todos os contêineres da conta. Para contas que possuem muitos contêineres, mas cujo `OpenAsync` aplicativo acessa um subconjunto deles, geraria uma quantidade desnecessária de tráfego, o que tornaria a inicialização lenta. Portanto, `OpenAsync` o uso pode não ser útil neste cenário porque diminui a velocidade da inicialização do aplicativo.

   <a id="same-region"></a>
**Para desempenho, colocem clientes na mesma região do Azure**

Quando possível, coloque quaisquer aplicativos que chamem Azure Cosmos DB na mesma região que o banco de dados Azure Cosmos DB. Aqui está uma comparação aproximada: chamadas para Azure Cosmos DB dentro da mesma região completa entre 1 ms a 2 ms, mas a latência entre a costa Oeste e Leste dos EUA é de mais de 50 ms. Essa latência pode variar de solicitação para solicitação, dependendo da rota tomada pela solicitação à medida que passa do cliente para o limite do datacenter do Azure. Você pode obter a menor latência possível, garantindo que o aplicativo de chamada esteja localizado dentro da mesma região do Azure que o ponto final do Azure Cosmos DB provisionado. Para obter uma lista de regiões disponíveis, consulte [Regiões do Azure](https://azure.microsoft.com/regions/#services).

![A política](./media/performance-tips/same-region.png) de conexão Azure Cosmos DB<a id="increase-threads"></a>

**Aumentar o número de threads/tarefas**

Como as chamadas para o Azure Cosmos DB são feitas pela rede, você pode precisar variar o grau de paralelismo de suas solicitações para que o aplicativo cliente passe o tempo mínimo esperando entre as solicitações. Por exemplo, se você estiver usando a [Biblioteca Paralela de Tarefas](https://msdn.microsoft.com//library/dd460717.aspx).NET, crie na ordem de centenas de tarefas que sejam lidas ou escrevam para o Azure Cosmos DB.

**Habilite o networking acelerado**
 
 Para reduzir a latência e o nervosismo da CPU, recomendamos que você habilite a rede acelerada em máquinas virtuais clientes. Veja [Criar uma máquina virtual do Windows com rede acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md) ou criar uma máquina virtual Linux com rede [acelerada](../virtual-network/create-vm-accelerated-networking-cli.md).

## <a name="sdk-usage"></a>Uso do SDK
**Instalar o SDK mais recente**

Os SDKs do Azure Cosmos DB estão constantemente sendo aprimorados para fornecer o melhor desempenho. Consulte as páginas do [SDK do Azure Cosmos DB](sql-api-sdk-dotnet-standard.md) para determinar o SDK mais recente e examinar as melhorias.

**Use APIs de fluxo**

[.NET SDK V3](sql-api-sdk-dotnet-standard.md) contém APIs de fluxo que podem receber e retornar dados sem serialização. 

Aplicativos intermediários que não consomem respostas diretamente do SDK, mas as retransmitem para outros níveis de aplicativos podem se beneficiar das APIs de fluxo. Consulte as amostras de gerenciamento de [itens](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) para exemplos de manuseio de fluxo.

**Use um cliente Singleton Azure Cosmos DB para a vida útil do seu aplicativo**

Cada `DocumentClient` `CosmosClient` instância é segura para threads e executa um gerenciamento eficiente de conexão e cache de endereço ao operar no modo direto. Para permitir um gerenciamento eficiente de conexão e melhor desempenho do `AppDomain` cliente SDK, recomendamos que você use uma única instância por vida útil do aplicativo.

   <a id="max-connection"></a>

**Aumente System.Net MaxConnections por host ao usar o modo gateway**

As solicitações do Azure Cosmos DB são feitas em HTTPS/REST quando você usa o modo gateway. Eles são submetidos ao limite de conexão padrão por nome de host ou endereço IP. Você pode precisar `MaxConnections` definir um valor mais alto (100 a 1.000) para que a biblioteca do cliente possa usar várias conexões simultâneas ao Azure Cosmos DB. Em .NET SDK 1.8.0 e posterior, o valor padrão para [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) é de 50. Para alterar o valor, você pode definir [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) para um valor mais alto.

**Ajuste consultas paralelas para coleções particionadas**

SQL .NET SDK 1.9.0 e posteriormente suporte consultas paralelas, que permitem consultar uma coleção particionada em paralelo. Para obter mais informações, consulte [exemplos de código](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) relacionados ao trabalho com os SDKs. As consultas paralelas são projetadas para fornecer melhor latência e throughput de consulta do que sua contraparte serial. As consultas paralelas fornecem dois parâmetros que você pode ajustar para atender às suas necessidades: 
- `MaxDegreeOfParallelism`controla o número máximo de partições que podem ser consultadas em paralelo. 
- `MaxBufferedItemCount`controla o número de resultados pré-buscados.

***Grau de sintonia do paralelismo***

A consulta paralela funciona consultando várias partições em paralelo. Mas os dados de uma partição individual são obtidos em série em relação à consulta. A `MaxDegreeOfParallelism` configuração no [SDK V2](sql-api-sdk-dotnet.md) ou `MaxConcurrency` no [SDK V3](sql-api-sdk-dotnet-standard.md) para o número de partições tem a melhor chance de alcançar a consulta mais performática, desde que todas as outras condições do sistema permaneçam as mesmas. Se você não sabe o número de partições, você pode definir o grau de paralelismo para um número alto. O sistema escolherá o mínimo (número de partições, entrada fornecida pelo usuário) como o grau de paralelismo.

Observe que as consultas paralelas produzem o maior benefício se os dados forem distribuídos uniformemente em todas as partições em relação à consulta. Se a coleção particionada for particionada de modo que todos ou a maioria dos dados retornados por uma consulta esteja concentrado em algumas partições (uma partição é o pior caso), essas partições irão estrangular o desempenho da consulta.

***Sintonia MaxBufferedItemCount***
    
A consulta paralela foi projetada para pré-buscar resultados enquanto o lote atual de resultados está sendo processado pelo cliente. Este pré-busca ajuda a melhorar a latência geral de uma consulta. O `MaxBufferedItemCount` parâmetro limita o número de resultados pré-buscados. Defina `MaxBufferedItemCount` para o número esperado de resultados retornados (ou um número maior) para permitir que a consulta receba o benefício máximo de pré-busca.

O pré-busca funciona da mesma maneira, independentemente do grau de paralelismo, e há um único buffer para os dados de todas as partições.  

**Implementar a retirada em intervalos de RetryAfter**

Durante os testes de desempenho, você deve aumentar a carga até que uma pequena taxa de solicitações seja estrangulada. Se as solicitações forem estranguladas, o aplicativo cliente deve recuar no acelerador para o intervalo de repetição especificado pelo servidor. Respeitar o backoff garante que você passe um tempo mínimo esperando entre as repetições. 

O suporte à política de repetição está incluído nestes SDKs:
- Versão 1.8.0 e posterior do [.NET SDK para SQL](sql-api-sdk-dotnet.md) e [Java SDK para SQL](sql-api-sdk-java.md)
- Versão 1.9.0 e posterior do [Node.js SDK para SQL](sql-api-sdk-node.md) e [Python SDK para SQL](sql-api-sdk-python.md)
- Todas as versões suportadas dos SDKs [do Núcleo .NET](sql-api-sdk-dotnet-core.md) 

Para obter mais informações, consulte [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
Na versão 1.19 e posterior do .NET SDK, há um mecanismo para registrar informações de diagnóstico adicionais e problemas de latência de solução de problemas, como mostrado na amostra a seguir. Você pode registrar a cadeia de caracteres de diagnóstico para solicitações que tenham uma latência de leitura mais alta. A seqüência de diagnóstico capturada ajudará você a entender quantas vezes você recebeu 429 erros para uma determinada solicitação.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**Armazenar em cache os URIs do documento para uma menor latência de leitura**

Armazene em cache os URIs do documento sempre que possível para ter o melhor desempenho de leitura. Você precisa definir a lógica para armazenar o ID de recurso ao criar um recurso. As análises baseadas em IDs de recursos são mais rápidas do que as aparências baseadas em nomes, então o cache desses valores melhora o desempenho.

   <a id="tune-page-size"></a>
**Ajuste o tamanho da página para consultas/feeds de leitura para obter um melhor desempenho**

Quando você faz uma leitura em massa de documentos `ReadDocumentFeedAsync`usando a funcionalidade do feed de leitura (por exemplo, ) ou quando você emite uma consulta SQL, os resultados são retornados de forma segmentada se o conjunto de resultados for muito grande. Por padrão, os resultados são retornados em blocos de 100 itens ou 1 MB, o limite que for atingido primeiro.

Para reduzir o número de viagens de ida e volta de rede necessárias para recuperar todos os resultados aplicáveis, você pode aumentar o tamanho da página usando [x-ms-max-item-contagem](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para solicitar até 1.000 cabeçalhos. Quando você precisa exibir apenas alguns resultados, por exemplo, se sua interface de usuário ou API do aplicativo retornar apenas 10 resultados por vez, você também pode diminuir o tamanho da página para 10 para reduzir o throughput consumido para leituras e consultas.

> [!NOTE] 
> A `maxItemCount` propriedade não deve ser usada apenas para paginação. Seu principal uso é melhorar o desempenho das consultas reduzindo o número máximo de itens retornados em uma única página.  

Você também pode definir o tamanho da página usando os SDKs Azure Cosmos DB disponíveis. A propriedade [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) `FeedOptions` permite definir o número máximo de itens a serem devolvidos na operação de enumeração. Quando `maxItemCount` é definido como -1, o SDK encontra automaticamente o valor ideal, dependendo do tamanho do documento. Por exemplo: 
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
Quando uma consulta é executada, os dados resultantes são enviados dentro de um pacote TCP. Se você especificar um `maxItemCount`valor muito baixo para , o número de viagens necessárias para enviar os dados dentro do pacote TCP é alto, o que afeta o desempenho. Portanto, se você não tem certeza de `maxItemCount` qual valor definir para a propriedade, é melhor defini-lo como -1 e deixar que o SDK escolha o valor padrão.

**Aumentar o número de threads/tarefas**

Consulte [Aumentar o número de threads/tarefas](#increase-threads) na seção Rede deste artigo.

## <a name="indexing-policy"></a>Política de indexação
 
**Exclua caminhos não utilizados da indexação para gravações mais rápidas**

A política de indexação do Azure Cosmos DB também permite especificar quais caminhos de documento incluir ou excluir da indexação usando caminhos de indexação (IndexingPolicy.IncludedPaths e IndexingPolicy.ExcludedPaths). Os caminhos de indexação podem melhorar o desempenho da gravação e reduzir o armazenamento de índices para cenários nos quais os padrões de consulta são conhecidos de antemão. Isso porque os custos de indexação se correlacionam diretamente com o número de caminhos únicos indexados. Por exemplo, este código mostra como excluir uma seção inteira dos documentos (uma subárvore) da indexação usando o curinga "*":

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
```

Para obter mais informações, consulte [Políticas de indexação do Azure Cosmos DB](index-policy.md).

## <a name="throughput"></a>Produtividade
<a id="measure-rus"></a>

**Medir e sintonizar para unidades de solicitação mais baixas/segundo uso**

O Azure Cosmos DB oferece um rico conjunto de operações de banco de dados. Essas operações incluem consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e gatilhos, todos operando nos documentos dentro de uma coleção de banco de dados. O custo associado a cada uma dessas operações varia dependendo da CPU, IO e memória necessária para concluir a operação. Em vez de pensar e gerenciar recursos de hardware, você pode pensar em uma Unidade de Solicitação (RU) como uma única medida para os recursos necessários para executar várias operações de banco de dados e atender a uma solicitação de aplicativo.

O throughput é provisionado com base no número de Unidades de [Solicitação definidas](request-units.md) para cada contêiner. Solicitação O consumo da unidade é avaliado como uma taxa por segundo. As aplicações que excedam a taxa de Unidade de Solicitação provisionada para o seu contêiner são limitadas até que a taxa caia abaixo do nível provisionado para o contêiner. Se a sua aplicação exigir um nível mais alto de rendimento, você pode aumentar o seu rendimento provisionando Unidades de Solicitação adicionais.

A complexidade de uma consulta afeta quantas Unidades de Solicitação são consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e o tamanho do conjunto de dados de origem influenciam o custo das operações de consulta.

Para medir a sobrecarga de qualquer operação (criar, atualizar ou excluir), inspecione o `RequestCharge` cabeçalho [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (ou a propriedade equivalente dentro `ResourceResponse\<T>` ou `FeedResponse\<T>` no SDK .NET) para medir o número de Unidades de Solicitação consumidas pelas operações:

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

A taxa de solicitação devolvida neste cabeçalho é uma fração do seu throughput provisionado (ou seja, 2.000 RUs/segundo). Por exemplo, se a consulta anterior retornar 1.000 documentos de 1 KB, o custo da operação será de 1.000. Assim, dentro de um segundo, o servidor honra apenas duas solicitações desse tipo antes de limitar as solicitações posteriores. Para obter mais informações, consulte [Unidades de Solicitação](request-units.md) e a calculadora unidade de [solicitação.](https://www.documentdb.com/capacityplanner)
<a id="429"></a>

**Lidar com uma limitação da taxa/taxa de solicitação muito grande**

Quando um cliente tenta exceder o rendimento reservado para uma conta, não há degradação de desempenho no servidor e nenhum uso da capacidade de throughput além do nível reservado. O servidor encerrará preventivamente a solicitação com RequestRateTooLarge (código de status HTTP 429). Ele retornará um [cabeçalho x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) que indica a quantidade de tempo, em milissegundos, que o usuário deve esperar antes de tentar a solicitação novamente.

    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Os SDKs irão capturar implicitamente essa resposta, respeitarão o cabeçalho server-specified retry-after e repetirão a solicitação. A menos que sua conta esteja sendo acessada simultaneamente por vários clientes, a próxima tentativa será bem-sucedida.

Se você tiver mais de um cliente operando de forma consistente acima da taxa de solicitação, a contagem de repetições padrão atualmente definida como 9 internamente pelo cliente pode não ser suficiente. Neste caso, o cliente joga um DocumentClientException com o código de status 429 para o aplicativo. 

Você pode alterar a contagem `RetryOptions` de `ConnectionPolicy` repetições padrão definindo a ocorrência. Por padrão, a DocumentClientException com o código de status 429 será retornada após uma espera cumulativa de 30 segundos se a solicitação continuar a operar acima da taxa de solicitação. Esse erro retorna mesmo quando a contagem de repetições atual é menor do que a contagem máxima de repetição, se o valor atual é o padrão de 9 ou um valor definido pelo usuário.

O comportamento automatizado de repetição ajuda a melhorar a resiliência e a usabilidade para a maioria dos aplicativos. Mas pode não ser o melhor comportamento quando você está fazendo benchmarks de desempenho, especialmente quando você está medindo latência. A latência observada pelo cliente terá um pico se o teste atingir a limitação do servidor e fizer com que o SDK do cliente repita silenciosamente. Para evitar picos de latência durante os testes de desempenho, meça o custo retornado por cada operação e verifique se as solicitações estão operando abaixo da taxa de solicitação reservada. Para obter mais informações, consulte [Unidades de solicitação](request-units.md).

**Para maior rendimento, design para documentos menores**

A taxa de solicitação (ou seja, o custo de processamento de solicitação) de uma determinada operação se correlaciona diretamente com o tamanho do documento. Operações em grandes documentos custam mais do que operações em pequenos documentos.

## <a name="next-steps"></a>Próximas etapas
Para obter um aplicativo de exemplo usado para avaliar o Azure Cosmos DB para cenários de alto desempenho em algumas máquinas clientes, consulte [testes de desempenho e escala com o Azure Cosmos DB](performance-testing.md).

Para saber mais sobre como projetar seu aplicativo para escala e alto desempenho, consulte [Particionamento e escala no Azure Cosmos DB](partition-data.md).
