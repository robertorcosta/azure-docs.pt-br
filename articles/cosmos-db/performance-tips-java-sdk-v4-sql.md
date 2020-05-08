---
title: Dicas de desempenho para Azure Cosmos DB SDK do Java v4
description: Aprenda as opções de configuração do cliente para melhorar o desempenho do banco de dados Cosmos do Azure para Java SDK v4
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: anfeldma
ms.openlocfilehash: ce4e4d11ead41ee8cc4a4bd1d85f1fbad2af4b07
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982523"
---
# <a name="performance-tips-for-azure-cosmos-db-java-sdk-v4"></a>Dicas de desempenho para Azure Cosmos DB SDK do Java v4

> [!div class="op_single_selector"]
> * [SDK do Java v4](performance-tips-java-sdk-v4-sql.md)
> * [SDK do Java Assíncrono v2](performance-tips-async-java.md)
> * [SDK do Java Síncrono v2](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

> [!IMPORTANT]  
> As dicas de desempenho neste artigo são apenas para Azure Cosmos DB SDK do Java v4. Veja as notas de versão do Azure Cosmos DB SDK do Java v4, [repositório Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos)e Azure Cosmos DB guia de [solução de problemas](troubleshoot-java-sdk-v4-sql.md) do SDK do Java v4 para obter mais informações. Se você estiver usando uma versão mais antiga do que a V4, consulte o guia [migrar para Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) para obter ajuda para atualizar para v4.
>

O Azure Cosmos DB é um banco de dados distribuído rápido e flexível que pode ser dimensionado perfeitamente com garantia de latência e produtividade. Você não precisa fazer alterações importantes de arquitetura nem escrever um código complexo para dimensionar seu banco de dados com o Azure Cosmos DB. Aumentar e reduzir é tão fácil quanto fazer uma única chamada à API ou uma chamada ao método do SDK. No entanto, como Azure Cosmos DB é acessado por meio de chamadas de rede, há otimizações do lado do cliente que você pode fazer para obter o máximo de desempenho ao usar Azure Cosmos DB SDK do Java v4.

Assim, se você estiver se perguntando "Como posso melhorar o desempenho do meu banco de dados?" considere as seguintes opções:

## <a name="networking"></a>Rede

* **Modo de conexão: usar o modo direto**
<a id="direct-connection"></a>
    
    Como um cliente se conecta a Azure Cosmos DB tem implicações importantes no desempenho, especialmente em termos de latência do lado do cliente. *ConnectionMode* é uma configuração de chave de configuração disponível para configurar o cliente *ConnectionPolicy*. Para Azure Cosmos DB SDK do Java v4, os dois *ConnectionMode*s disponíveis são:  
      
    * [Gateway (padrão)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
    * [Direto](/java/api/com.microsoft.azure.cosmosdb.connectionmode)

    Essencialmente, esses *ConnectionMode*condicionam a rota que as solicitações levam do computador cliente a partições no back-end de Azure Cosmos DB. Geralmente, o modo direto é a opção preferencial para o melhor desempenho-ele permite que o cliente abra conexões TCP diretamente em partições nas Azure Cosmos DB back-end e solicitações de envio *diretos*, sem intermediário. Por outro lado, no modo de gateway, as solicitações feitas pelo cliente são roteadas para um servidor chamado "gateway" no front-end de Azure Cosmos DB, que, por sua vez, faz o ventilador de suas solicitações para as partições apropriadas no back-end de Azure Cosmos DB. Se seu aplicativo for executado em uma rede corporativa com restrições de firewall estritas, o modo de gateway será a melhor opção, pois ele usa a porta HTTPS padrão e um único ponto de extremidade. No entanto, a compensação de desempenho é que o modo de gateway envolve um salto de rede adicional (cliente para gateway mais gateway para partição) sempre que os dados são lidos ou gravados em Azure Cosmos DB. Por isso, o modo direto oferece melhor desempenho devido a menos saltos de rede.

    O *ConnectionMode* é configurado durante a construção da instância de cliente do Azure Cosmos DB com o parâmetro *ConnectionPolicy* :
    
   #### <a name="async"></a>[Async](#tab/api-async)

   ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>API assíncrona do SDK do Java V4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    CosmosAsyncClient client = new CosmosClientBuilder()
        .setEndpoint(HOST)
        .setKey(MASTER)
        .setConnectionPolicy(connectionPolicy)
        .buildAsyncClient();
    ```

    #### <a name="sync"></a>[Sincronização](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-connection-policy-sync"></a>API de sincronização do SDK do Java V4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    CosmosClient client = new CosmosClientBuilder()
        .setEndpoint(HOST)
        .setKey(MASTER)
        .setConnectionPolicy(connectionPolicy)
        .buildClient();
    ```

    --- 

<a name="collocate-clients"></a>
* Colocar **clientes na mesma região do Azure para desempenho**<a id="same-region"></a>

    Quando possível, coloque todos os aplicativos que chamam Azure Cosmos DB na mesma região que o banco de dados Cosmos do Azure. Para obter uma comparação aproximada, as chamadas para o Azure Cosmos DB na mesma região são concluídas de 1 a 2 ms, mas a latência entre a Costa Leste e a Oeste dos EUA é maior que 50 ms. Provavelmente, essa latência pode variar entre as solicitações dependendo da rota seguida pela solicitação conforme ela passa do cliente para o limite de datacenter do Azure. A menor latência possível é alcançada garantindo que o aplicativo de chamada está localizado na mesma região do Azure do ponto de extremidade do Azure Cosmos DB provisionado. Para obter uma lista de regiões disponíveis, consulte [regiões do Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustração da política de conexão do Azure Cosmos DB](./media/performance-tips/same-region.png)

    Um aplicativo que interage com uma conta de Azure Cosmos DB de várias regiões precisa configurar [locais preferenciais]() para garantir que as solicitações vão para uma região posicionada.

* **Habilite a rede acelerada na sua VM do Azure para uma latência mais baixa.**

É recomendável que você siga as instruções para habilitar a rede acelerada em seu [Windows (clique para obter instruções)](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) ou [Linux (clique para obter instruções)](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) VM do Azure, para maximizar o desempenho.

Sem a rede acelerada, a e/s que transita entre a VM do Azure e outros recursos do Azure pode ser desnecessariamente roteada por meio de um host e um comutador virtual situados entre a VM e sua placa de rede. Ter o host e o comutador virtual embutidos no caminho de computador não apenas aumenta a latência e a tremulação no canal de comunicação, ele também rouba ciclos de CPU da VM. Com a rede acelerada, a VM interage diretamente com a NIC sem intermediários; todos os detalhes da diretiva de rede que estavam sendo manipulados pelo host e pelo comutador virtual agora são manipulados no hardware na NIC; o host e o comutador virtual são ignorados. Geralmente, você pode esperar uma latência mais baixa e uma taxa de transferência mais alta, bem como latência mais *consistente* e menor utilização da CPU ao habilitar a rede acelerada.

Limitações: a rede acelerada deve ter suporte no sistema operacional da VM e só pode ser habilitada quando a VM é interrompida e desalocada. A VM não pode ser implantada com Azure Resource Manager.

Consulte as instruções do [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) e do [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) para obter mais detalhes.

## <a name="sdk-usage"></a>Uso do SDK
* **Instalar o SDK mais recente**

    Os SDKs do Azure Cosmos DB estão constantemente sendo aprimorados para fornecer o melhor desempenho. Consulte as páginas do [SDK do Azure Cosmos DB](sql-api-sdk-async-java.md) para determinar o SDK mais recente e examinar as melhorias.

* **Usar um cliente Azure Cosmos DB singleton durante o tempo de vida do seu aplicativo**

    Cada instância de cliente do Azure Cosmos DB é thread-safe e executa o gerenciamento de conexão e o cache de endereços eficientes. Para permitir um gerenciamento de conexão eficiente e melhor desempenho pelo cliente do Azure Cosmos DB, é recomendável usar uma única instância do cliente Azure Cosmos DB por AppDomain durante o tempo de vida do aplicativo.

   <a id="max-connection"></a>

* **Use o nível de consistência mais baixo necessário para seu aplicativo**

    Quando você cria um *CosmosClient*, a consistência padrão usada se não definido explicitamente é *Session*. Se a consistência da *sessão* não for exigida pela lógica do aplicativo, defina a *consistência* como *eventual*. Observação: é recomendável usar pelo menos a consistência de *sessão* em aplicativos que empregam o processador Azure Cosmos DB feed de alterações.

* **Usar a API assíncrona para obter a taxa de transferência máxima provisionada**

    O Azure Cosmos DB SDK do Java v4 agrupa duas APIs, Sync e Async. Em termos gerais, a API assíncrona implementa a funcionalidade do SDK, enquanto que a API de sincronização é um wrapper fino que faz chamadas de bloqueio para a API assíncrona. Isso significa que, em contraste com o Azure Cosmos DB antigo SDK de Java assíncrono v2, que era somente Async e para o SDK do Java de sincronização mais antigo do Azure Cosmos DB, que era somente sincronização e tinha uma implementação completamente separada. 
    
    A escolha da API é determinada durante a inicialização do cliente; um *CosmosAsyncClient* dá suporte à API assíncrona enquanto um *CosmosClient* dá suporte à API de sincronização. 
    
    A API assíncrona implementa e/s sem bloqueio e é a melhor opção se sua meta for obter a taxa de transferência máxima ao emitir solicitações para Azure Cosmos DB. 
    
    O uso da API de sincronização pode ser a escolha certa se você quiser ou precisar de uma API que bloqueie a resposta a cada solicitação, ou se a operação síncrona for o paradigma dominante em seu aplicativo. Por exemplo, você pode querer a API de sincronização quando estiver mantendo dados para Azure Cosmos DB em um aplicativo de microserviço, desde que a taxa de transferência fornecida não seja crítica.  
    
    Apenas lembre-se de que a taxa de transferência da API de sincronização é reduzida com tempo de resposta de solicitação crescente, enquanto a API assíncrona pode saturar os recursos completos de largura de banda do seu hardware. 
    
    A colocação geográfica pode fornecer uma taxa de transferência maior e mais consistente ao usar a API de sincronização (consulte colocar [clientes na mesma região do Azure para desempenho](#collocate-clients)), mas ainda não se espera que exceda a taxa de transferência ATINGÍVEL da API assíncrona.

    Alguns usuários também podem não estar familiarizados com o [reator do projeto](https://projectreactor.io/), a estrutura de fluxos reativos usada para implementar Azure Cosmos DB API assíncrona do SDK do Java v4. Se isso for uma preocupação, recomendamos que você leia nosso [Guia de padrões de reator](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) introdutório e dê uma olhada nesta [introdução à programação reativa](https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro) para se familiarizar. Se você já tiver usado Azure Cosmos DB com uma interface assíncrona e o SDK usado foi Azure Cosmos DB SDK Java v2, talvez você esteja familiarizado com[RxJava](https://github.com/ReactiveX/RxJava) [reactivex](http://reactivex.io/)/, mas não saberá o que mudou no reator do projeto. Nesse caso, dê uma olhada em nosso guia de [reator versus RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) para se familiarizar.

    Os trechos de código a seguir mostram como inicializar seu cliente do Azure Cosmos DB para a API assíncrona ou a operação de API de sincronização, respectivamente:

    #### <a name="async"></a>[Async](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-async-client"></a>API assíncrona do SDK do Java V4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    CosmosAsyncClient client = new CosmosClientBuilder()
        .setEndpoint(HOSTNAME)
        .setKey(MASTERKEY)
        .setConnectionPolicy(CONNECTIONPOLICY)
        .setConsistencyLevel(CONSISTENCY)
        .buildAsyncClient();
    ```

    #### <a name="sync"></a>[Sincronização](#tab/api-sync)
 
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-sync-client"></a>API de sincronização do SDK do Java V4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    CosmosClient client = new CosmosClientBuilder()
        .setEndpoint(HOSTNAME)
        .setKey(MASTERKEY)
        .setConnectionPolicy(CONNECTIONPOLICY)
        .setConsistencyLevel(CONSISTENCY)
        .buildClient();
    ```    

    ---

* **Ajustar ConnectionPolicy**

    Por padrão, as solicitações de Cosmos DB de modo direto são feitas por TCP ao usar Azure Cosmos DB SDK do Java v4. Internamente, o SDK usa uma arquitetura especial de modo direto para gerenciar dinamicamente os recursos de rede e obter o melhor desempenho.

    No SDK do Java Azure Cosmos DB v4, o modo direto é a melhor opção para melhorar o desempenho do banco de dados com a maioria das cargas de trabalho. 

    * ***Visão geral do modo direto***

        ![Ilustração da arquitetura do modo direto](./media/performance-tips-async-java/rntbdtransportclient.png)

        A arquitetura do lado do cliente empregada no modo direto permite a utilização de rede previsível e o acesso multiplexado a réplicas de Azure Cosmos DB. O diagrama acima mostra como o modo direto roteia solicitações de cliente para réplicas no back-end de Cosmos DB. A arquitetura do modo direto aloca até 10 **canais** no lado do cliente por réplica de BD. Um canal é uma conexão TCP precedida por um buffer de solicitação, que é de 30 solicitações de profundidade. Os canais que pertencem a uma réplica são alocados dinamicamente conforme necessário pelo **ponto de extremidade de serviço**da réplica. Quando o usuário emite uma solicitação no modo direto, o **TransportClient** roteia a solicitação para o ponto de extremidade de serviço apropriado com base na chave de partição. Os buffers de **fila de solicitação** solicitam antes do ponto de extremidade de serviço.

    * ***Opções de configuração do ConnectionPolicy para o modo direto***

        Esses parâmetros de configuração controlam o comportamento da arquitetura RNTBD que governa o comportamento do SDK do modo direto.
        
        Como uma primeira etapa, use as seguintes definições de configuração recomendadas abaixo. Essas opções de *ConnectionPolicy* são definições avançadas de configuração que podem afetar o desempenho do SDK de maneiras inesperadas; Recomendamos que os usuários evitem modificá-los, a menos que se sintam muito à vontade em entender as compensações e é absolutamente necessário. Entre em contato com a [equipe de Azure Cosmos DB](mailto:CosmosDBPerformanceSupport@service.microsoft.com) se você encontrar problemas neste tópico específico.

        Se você estiver usando Azure Cosmos DB como um banco de dados de referência (ou seja, o banco de dados é usado para muitas operações de leitura de ponto e poucas operações de gravação), pode ser aceitável definir *idleEndpointTimeout* como 0 (ou seja, sem tempo limite).


        | Opções de configuração       | Padrão    |
        | :------------------:       | :-----:    |
        | bufferPageSize             | 8192       |
        | connectionTimeout          | "PT1M"     |
        | idleChannelTimeout         | PT0S     |
        | idleEndpointTimeout        | "PT1M10S"  |
        | maxBufferCapacity          | 8388608    |
        | maxChannelsPerEndpoint     | 10         |
        | maxRequestsPerChannel      | 30         |
        | receiveHangDetectionTime   | "PT1M5S"   |
        | requestExpiryInterval      | PT5S     |
        | requestTimeout             | "PT1M"     |
        | requestTimerResolution     | "PT de 0,5 S"   |
        | sendHangDetectionTime      | "PT10S"    |
        | shutdownTimeout            | PT15S    |

* **Ajustar consultas paralelas para coleções particionadas**

    Azure Cosmos DB SDK do Java v4 dá suporte a consultas paralelas, que permitem consultar uma coleção particionada em paralelo. Para obter mais informações, consulte [exemplos de código](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) relacionados ao trabalho com Azure Cosmos DB SDK do Java v4. Consultas paralelas são projetadas para melhorar a latência da consulta e a produtividade em relação à contraparte serial.

    * ***Ajustando setMaxDegreeOfParallelism\:***
    
        As consultas paralelas funcionam consultando várias partições em paralelo. No entanto, os dados de uma coleção particionada individual são buscados em série com relação à consulta. Por isso, use setMaxDegreeOfParallelism para definir o número de partições que representa o máximo de chance de conseguir uma consulta com o melhor desempenho, desde que todas as outras condições do sistema permaneçam as mesmas. Se você não souber o número de partições, poderá usar setMaxDegreeOfParallelism para definir um número alto, e o sistema escolherá o mínimo (número de partições, entrada fornecida pelo usuário) como o grau máximo de paralelismo.

        É importante observar que as consultas paralelas produzirão os melhores benefícios se os dados forem distribuídos uniformemente em todas as partições com relação à consulta. Se a coleção particionada for particionada de uma forma que todos ou a maioria dos dados retornados por uma consulta ficarem concentrados em algumas partições (uma partição, na pior das hipóteses), o desempenho da consulta seria um gargalo dessas partições.

    * ***Ajustando setMaxBufferedItemCount\:***
    
        A consulta paralela foi projetada para buscar antecipadamente resultados enquanto o lote atual de resultados está sendo processado pelo cliente. A busca prévia ajuda a melhorar a latência geral de uma consulta. setMaxBufferedItemCount limita o número de resultados pré-buscados. Definir setMaxBufferedItemCount para o número esperado de resultados retornados (ou um número mais alto) permite que a consulta receba o benefício máximo da busca prévia.

        A busca prévia funciona da mesma forma independentemente do MaxDegreeOfParallelism, e há um único buffer para os dados de todas as partições.

* **Escalar horizontalmente sua carga de trabalho do cliente**

    Se você estiver testando em níveis de taxa de transferência alta, o aplicativo cliente poderá se tornar o afunilamento devido à máquina que está capping a utilização da CPU ou da rede. Se você chegar a este ponto, poderá continuar aumentando a conta do Azure Cosmos DB ainda mais distribuindo seus aplicativos cliente entre vários servidores.

    Uma boa regra geral é não exceder >a utilização de CPU de 50% em qualquer servidor determinado, para manter a latência baixa.

   <a id="tune-page-size"></a>

* **Ajustar o tamanho da página para os feeds de leitura/consultas para ter o melhor desempenho**

    Ao executar uma leitura em massa de documentos usando a funcionalidade de feed de leitura (por exemplo, *readItems*) ou ao emitir uma consulta SQL (*queryItems*), os resultados serão retornados de uma maneira segmentada se o conjunto de resultados for muito grande. Por padrão, os resultados são retornados em blocos de 100 itens ou 1 MB, o limite que for atingido primeiro.

    Suponha que seu aplicativo emita uma consulta para Azure Cosmos DB e suponha que seu aplicativo exija o conjunto completo de resultados da consulta para concluir sua tarefa. Para reduzir o número de viagens de ida e volta da rede necessárias para recuperar todos os resultados aplicáveis, você pode aumentar o tamanho da página ajustando o campo de cabeçalho de solicitação [x-MS-Max-item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) . 

    * Para consultas de partição única, o ajuste do valor do campo [x-MS-Max-item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para-1 (sem limite no tamanho da página) maximiza a latência minimizando o número de páginas de resposta de consulta: o conjunto de resultados completo retornará em uma única página ou, se a consulta demorar mais do que o intervalo de tempo limite, o conjunto de resultados completo será retornado no número mínimo de páginas possível. Isso economiza em múltiplos do tempo de ida e volta da solicitação.
    
    * Para consultas entre partições, definir o campo [x-MS-Max-item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) como-1 e remover os riscos de limite de tamanho de página que sobrecarregam o SDK com tamanhos de página não gerenciáveis. No caso entre partições, é recomendável aumentar o limite de tamanho de página para um valor grande, mas finito, talvez 1000, para reduzir a latência. 
    
    Em alguns aplicativos, talvez você não precise do conjunto completo de resultados da consulta. Nos casos em que você precisa exibir apenas alguns resultados, por exemplo, se a interface do usuário ou a API do aplicativo retornar apenas 10 resultados por vez, você também poderá diminuir o tamanho da página para 10 para reduzir a taxa de transferência consumida para leituras e consultas.

    Você também pode definir o argumento de tamanho de página preferencial do método *byPage* , em vez de modificar o campo de cabeçalho REST diretamente. Tenha em mente que [x-MS-Max-item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) ou o argumento de tamanho de página preferencial de *byPage* está definindo apenas um limite superior no tamanho da página, não um requisito absoluto; Portanto, por uma variedade de motivos, você pode ver Azure Cosmos DB retornar páginas que são menores do que o tamanho de sua página preferencial. 

* **Usar o Agendador Apropriado (Evitar roubo de threads Netty de E/S Eventloop)**

    A funcionalidade assíncrona do SDK do Java Azure Cosmos DB é baseada em e/s sem bloqueio de [sub-rede](https://netty.io/) . O SDK usa um número fixo de threads de eventloop netty de E/S (como muitos núcleos de CPU que seu computador possui) para executar operações de E/S. O fluxo retornado pela API emite o resultado em um dos threads de sub-rede do loop de evento de e/s compartilhado. Portanto, é importante não bloquear os threads de netty eventloop de E/S compartilhados. Fazer o trabalho intensivo de CPU ou bloquear operação no thread de netty eventloop de E/S pode causar deadlock ou reduzir significativamente a taxa de transferência de SDK.

    Por exemplo, o código a seguir executa um trabalho intensivo de CPU no thread de netty eventloop de E/S:
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-noscheduler"></a>API assíncrona do SDK do Java V4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    Mono<CosmosAsyncItemResponse<CustomPOJO>> createItemPub = asyncContainer.createItem(item);
    createItemPub.subscribe(
        itemResponse -> {
            //this is executed on eventloop IO netty thread.
            //the eventloop thread is shared and is meant to return back quickly.
            //
            // DON'T do this on eventloop IO netty thread.
            veryCpuIntensiveWork();                
        });
    ```

    Quando um resultado é recebido, se desejar fazer trabalho intensivo de CPU no resultado, você deve evitar fazer isso em thread de netty eventloop de E/S. Em vez disso, você pode fornecer seu próprio Agendador para fornecer seu próprio thread para executar seu trabalho, conforme mostrado abaixo.

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>API assíncrona do SDK do Java V4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    import reactor.core.scheduler.Schedulers;
    Mono<CosmosAsyncItemResponse<CustomPOJO>> createItemPub = asyncContainer.createItem(item);
    createItemPub
        .subscribeOn(Schedulers.elastic())
        .subscribe(
        itemResponse -> {
            //this is executed on eventloop IO netty thread.
            //the eventloop thread is shared and is meant to return back quickly.
            //
            // DON'T do this on eventloop IO netty thread.
            veryCpuIntensiveWork();                
        });
    ```

    Com base no tipo de seu trabalho, você deve usar o Agendador de reator existente apropriado para seu trabalho. Leia aqui [``Schedulers``](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html).

    Para obter mais informações sobre Azure Cosmos DB SDK do Java v4, consulte o [diretório Cosmos DB do SDK do Azure para Java monorepositório no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos).

* **Otimizar as configurações de log em seu aplicativo**

    Por vários motivos, talvez você queira ou precise adicionar o registro em log em um thread que está gerando alta taxa de transferência de solicitação. Se seu objetivo é saturar completamente a taxa de transferência provisionada de um contêiner com solicitações geradas por esse thread, as otimizações de log podem melhorar significativamente o desempenho.

    * ***Configurar um agente assíncrono***

        A latência de um agente síncrono é necessariamente fatores no cálculo de latência geral de seu thread de geração de solicitação. Um agente assíncrono, como [log4j2](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flogging.apache.org%2Flog4j%2Flog4j-2.3%2Fmanual%2Fasync.html&data=02%7C01%7CCosmosDBPerformanceInternal%40service.microsoft.com%7C36fd15dea8384bfe9b6b08d7c0cf2113%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637189868158267433&sdata=%2B9xfJ%2BWE%2F0CyKRPu9AmXkUrT3d3uNA9GdmwvalV3EOg%3D&reserved=0) , é recomendado para desacoplar a sobrecarga de log de seus threads de aplicativo de alto desempenho.

    * ***Desabilitar o registro em log da sub-rede***

        O log da biblioteca de uma sub-rede é informal e precisa ser desativado (suprimir o logon na configuração pode não ser suficiente) para evitar custos adicionais de CPU. Se você não estiver no modo de depuração, desabilite o registro em log do netty completamente. Portanto, se estiver usando log4j para remover os custos adicionais de CPU devidos por ``org.apache.log4j.Category.callAppenders()`` do netty, adicione a seguinte linha à sua base de código:

        ```java
        org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
        ```

 * **Limite de recursos de arquivos abertos do so**
 
    Alguns sistemas Linux (como o Red Hat) têm um limite superior do número de arquivos abertos e, portanto, o número total de conexões. Execute o seguinte para exibir os limites atuais:

    ```bash
    ulimit -a
    ```

    O número de arquivos abertos (nofile) deve ser grande o suficiente para ter espaço suficiente para o tamanho do pool de conexão configurada e outros arquivos abertos pelo SO. Isso pode ser modificado para permitir um maior tamanho de pool de conexão.

    Abra o arquivo limits.conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Adicione/modifique as linhas a seguir:

    ```
    * - nofile 100000
    ```

* **Especificar chave de partição em gravações de ponto**

    Para melhorar o desempenho das gravações de ponto, especifique a chave de partição do item na chamada à API de gravação de ponto, conforme mostrado abaixo:

    #### <a name="async"></a>[Async](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-createitem-good-async"></a>API assíncrona do SDK do Java V4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    asyncContainer.createItem(item,new PartitionKey(pk),new CosmosItemRequestOptions()).block();
    ```

    #### <a name="sync"></a>[Sincronização](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-createitem-good-sync"></a>API de sincronização do SDK do Java V4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    syncContainer.createItem(item,new PartitionKey(pk),new CosmosItemRequestOptions());
    ```

    ---

    em vez de fornecer apenas a instância do item, conforme mostrado abaixo:

    #### <a name="async"></a>[Async](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-createitem-bad-async"></a>API assíncrona do SDK do Java V4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    asyncContainer.createItem(item).block();
    ```

    #### <a name="sync"></a>[Sincronização](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-createitem-bad-sync"></a>API de sincronização do SDK do Java V4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    syncContainer.createItem(item);
    ```

    ---

    Há suporte para o último, mas isso adicionará latência ao seu aplicativo; o SDK deve analisar o item e extrair a chave de partição.

## <a name="indexing-policy"></a>Política de indexação
 
* **Excluir caminhos não utilizados da indexação para gravações mais rápidas**

    A política de indexação do Azure Cosmos DB permite que você especifique quais caminhos de documento serão incluídos ou excluídos da indexação, aproveitando os Caminhos de Indexação (setIncludedPaths e setExcludedPaths). O uso dos caminhos de indexação pode oferecer um melhor desempenho de gravação e menor armazenamento de índices para os cenários nos quais os padrões da consulta são conhecidos com antecedência, pois os custos da indexação estão correlacionados diretamente com o número de caminhos exclusivos indexados. Por exemplo, o código a seguir mostra como excluir uma seção inteira dos documentos (também conhecido como subárvore) da indexação usando o curinga "*".

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-indexing"></a>Java SDK V4 (Maven com. Azure:: Azure-Cosmos)
    ```java
    Index numberIndex = Index.Range(DataType.Number);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);        
    containerProperties.setIndexingPolicy(indexingPolicy);
    ``` 

    Para obter mais informações, consulte [Políticas de indexação do Azure Cosmos DB](indexing-policies.md).

## <a name="throughput"></a>Produtividade
<a id="measure-rus"></a>

* **Medir e ajustar para o uso mais baixo de unidades/segundo da solicitação**

    O Azure Cosmos DB oferece um conjunto avançado de operações do banco de dados, incluindo consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e gatilhos – todos operando nos documentos em uma coleção de banco de dados. O custo associado a cada uma dessas operações varia com base na CPU, E/S e memória necessárias para concluir a operação. Em vez de pensar em e gerenciar recursos de hardware, você pode pensar em uma RU (unidade de solicitação) como uma medida única para os recursos necessários para realizar várias operações de bancos de dados e atender a uma solicitação do aplicativo.

    A taxa de transferência é provisionada com base no número de [unidades de solicitação](request-units.md) definidas para cada contêiner. O consumo da unidade de solicitação é avaliado em termos de taxa por segundo. Os aplicativos que excedem a taxa das unidades de solicitação provisionada para seu contêiner serão limitados até que a taxa fique abaixo do nível reservado para o contêiner. Caso o aplicativo exija um nível mais alto de taxa de transferência, é possível aumentar a taxa de transferência provisionando unidades de solicitação adicionais.

    A complexidade de uma consulta afeta a quantidade de unidades de solicitação consumida para uma operação. O número de predicados, natureza dos predicados, número de UDFs e tamanho do conjunto de dados de origem influenciam o custo das operações de consulta.

    Para medir a sobrecarga de qualquer operação (criar, atualizar ou excluir), examine o cabeçalho [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para medir o número de unidades de solicitação consumidas por essas operações. Você também pode examinar a propriedade RequestCharge equivalente em ResourceResponse\<t> ou FeedResponse\<t>.

    #### <a name="async"></a>[Async](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-request-charge-async"></a>API assíncrona do SDK do Java V4 (Maven com. Azure:: Azure-Cosmos)

    ```java
    CosmosAsyncItemResponse<CustomPOJO> response = asyncContainer.createItem(item).block();

    response.getRequestCharge();
    ```     

    #### <a name="sync"></a>[Sincronização](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-request-charge-sync"></a>API de sincronização do SDK do Java V4 (Maven com. Azure:: Azure-Cosmos)    

    ```java
    CosmosItemResponse<CustomPOJO> response = syncContainer.createItem(item);

    response.getRequestCharge();
    ```     

    ---

    A carga de solicitação retornada nesse cabeçalho é uma fração de sua taxa de transferência provisionada. Por exemplo, se você tem 2000 RUs/s provisionados e se a consulta anterior retornar 1000 documentos de 1 KB, o custo da operação será 1000. Assim, em um segundo, o servidor mantém apenas duas dessas solicitações antes de limitar as solicitações subsequentes. Para saber mais, consulte [Unidades de solicitação](request-units.md) e a [calculadora das unidades de solicitação](https://www.documentdb.com/capacityplanner).

<a id="429"></a>
* **Lidar com uma limitação da taxa/taxa de solicitação muito grande**

    Quando um cliente tentar exceder a taxa de transferência reservada para uma conta, não haverá nenhuma degradação de desempenho no servidor e nenhum uso da capacidade da taxa além do nível reservado. O servidor encerrará de forma preventiva a solicitação com RequestRateTooLarge (código de status HTTP 429) e retornará o cabeçalho [x-MS-Retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) , indicando o tempo, em milissegundos, que o usuário deve aguardar antes de tentar novamente a solicitação.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Os SDKs irão capturar implicitamente essa resposta, respeitarão o cabeçalho server-specified retry-after e repetirão a solicitação. A menos que sua conta esteja sendo acessada simultaneamente por vários clientes, a próxima tentativa será bem-sucedida.

    Se você tiver mais de um cliente operando de forma cumulativa consistentemente acima da taxa de solicitação, a contagem de repetição padrão definida atualmente como 9 internamente pelo cliente pode não ser suficiente; Nesse caso, o cliente gera um *CosmosClientException* com o código de status 429 para o aplicativo. A contagem de repetição padrão pode ser alterada usando setRetryOptions na instância ConnectionPolicy. Por padrão, o *CosmosClientException* com o código de status 429 é retornado após um tempo de espera cumulativo de 30 segundos se a solicitação continuar a operar acima da taxa de solicitação. Isso ocorre mesmo quando a contagem de repetição atual é menor que a contagem de repetição máxima, seja o padrão 9 seja um valor definido pelo usuário.

    Embora o comportamento de repetição automática ajude a melhorar a resiliência e a utilidade da maioria dos aplicativos, ela pode entrar em conflito ao fazer comparações de desempenho, especialmente ao medir a latência. A latência observada pelo cliente terá um pico se o teste atingir a limitação do servidor e fizer com que o SDK do cliente repita silenciosamente. Para evitar picos de latência durante os testes de desempenho, meça o custo retornado por cada operação e verifique se as solicitações estão operando abaixo da taxa de solicitação reservada. Para saber mais, consulte [Unidades de solicitação](request-units.md).

* **Projetar documentos menores para uma maior taxa de transferência**

    O custo da solicitação (o custo de processamento da solicitação) de uma determinada operação está correlacionado diretamente com o tamanho do documento. As operações em documentos grandes custam mais que as operações de documentos pequenos. O ideal é arquitetar seu aplicativo e fluxos de trabalho para que o tamanho do item seja ~ 1 KB ou uma ordem ou magnitude semelhante. Para aplicativos com detecção de latência, itens grandes devem ser evitados-documentos de vários MB tornarão o seu aplicativo mais lento.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como projetar seu aplicativo para escala e alto desempenho, consulte [Particionamento e escala no Azure Cosmos DB](partition-data.md).
