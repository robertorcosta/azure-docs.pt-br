---
title: Diagnosticar e solucionar problemas Azure Cosmos DB SDK do Java v4
description: Use recursos como registro em log do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e solucionar problemas de Azure Cosmos DB no SDK do Java v4.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/08/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.openlocfilehash: bdec785ccec2c388eb737da3ec494b525941e2a6
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982591"
---
# <a name="troubleshoot-issues-when-you-use-azure-cosmos-db-java-sdk-v4-with-sql-api-accounts"></a>Solucionar problemas ao usar Azure Cosmos DB SDK do Java V4 com contas da API do SQL

> [!div class="op_single_selector"]
> * [SDK do Java v4](troubleshoot-java-sdk-v4-sql.md)
> * [SDK do Java Assíncrono v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> Este artigo aborda a solução de problemas para Azure Cosmos DB apenas o SDK do Java v4. Consulte as notas de versão do Azure Cosmos DB SDK do Java v4, [repositório Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos)e [dicas de desempenho](performance-tips-java-sdk-v4-sql.md) para obter mais informações. Se você estiver usando uma versão mais antiga do que a V4, consulte o guia [migrar para Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) para obter ajuda para atualizar para v4.
>

Este artigo aborda problemas comuns, soluções alternativas, etapas de diagnóstico e ferramentas quando você usa Azure Cosmos DB SDK do Java V4 com Azure Cosmos DB contas da API do SQL.
Azure Cosmos DB SDK do Java v4 fornece a representação lógica do lado do cliente para acessar a API do SQL do Azure Cosmos DB. Este artigo descreve as ferramentas e as abordagens para ajudá-lo se você tiver algum problema.

Comece com esta lista:

* Dê uma olhada na seção [Problemas comuns e soluções alternativas] neste artigo.
* Examine o SDK do Java no repositório central Azure Cosmos DB, que está disponível [em código aberto no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos). Ele tem um [seção de problemas](https://github.com/Azure/azure-sdk-for-java/issues) que está sendo ativamente monitorada. Verifique se você encontrar algum problema semelhante com uma solução alternativa já arquivada. Uma dica útil é filtrar problemas pela marca *Cosmos: v4-item* .
* Examine as [dicas de desempenho](performance-tips-java-sdk-v4-sql.md) para Azure Cosmos DB SDK do Java v4 e siga as práticas sugeridas.
* Se você não encontrar uma solução, leia o restante deste artigo. Em seguida, arquive um [problema do GitHub](https://github.com/Azure/azure-sdk-for-java/issues). Se houver uma opção para adicionar marcas ao seu problema do GitHub, adicione uma marca *Cosmos: v4-item* .

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Problemas comuns e soluções alternativas

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problemas de rede, falha de tempo limite do Netty, baixa taxa de transferência, alta latência de leitura

#### <a name="general-suggestions"></a>Sugestões gerais
Para obter o melhor desempenho:
* Verifique se o aplicativo está em execução na mesma região que sua conta do Azure Cosmos DB. 
* Verifique o uso da CPU no host em que o aplicativo está sendo executado. Se o uso da CPU for de 50% ou mais, execute seu aplicativo em um host com uma configuração mais alta. Ou você pode distribuir a carga em mais computadores.
    * Se você estiver executando seu aplicativo no serviço kubernetes do Azure, poderá [usar Azure monitor para monitorar a utilização da CPU](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-analyze).

#### <a name="connection-throttling"></a>Limitação de conexão
A limitação de conexão pode ocorrer devido a um [limite de conexão no computador host] ou a [Esgotamento de porta do Azure SNAT (PAT)].

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Limite de conexão no computador host
Alguns sistemas Linux, como Red Hat, têm um limite superior para o número total de arquivos abertos. Soquetes no Linux são implementados como arquivos, portanto, esse número limita o número total de conexões também.
Execute o comando a seguir.

```bash
ulimit -a
```
O número de arquivos abertos máximos permitos, que são identificados como "nofile", devem ser pelo menos duas vezes o tamanho do pool de conexão. Para obter mais informações, consulte as [dicas de desempenho](performance-tips-java-sdk-v4-sql.md)do Azure Cosmos DB SDK do Java v4.

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Esgotamento da porta SNAT (PAT) do Azure

Se o seu aplicativo for desenvolvido nas Máquinas Virtuais do Microsoft Azure sem um endereço IP público, por padrão as [portas do Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) estabelecem conexões a qualquer ponto de extremidade fora da sua VM. O número de conexões permitidas da VM para o ponto de extremidade do Azure Cosmos DB é limitado pela [configuração do Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 As portas Azure SNAT são usadas somente quando sua VM do Azure tiver um endereço IP privado e um processo da VM tenta estabelecer uma conexão com um endereço IP público. Há duas soluções alternativas para evitar a limitação do Azure SNAT:

* Adicione seu ponto de extremidade de serviço do Azure Cosmos DB para a sub-rede da sua rede virtual de Máquinas Virtuais do Azure. Para saber mais, consulte [pontos de extremidade de serviço de Rede Virtual do Microsoft Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Quando o ponto de extremidade for habilitado, as solicitações não são mais enviadas de um IP público para o Azure Cosmos DB. Em vez disso, a rede virtual e a identidade de sub-rede são enviadas. Essa alteração poderá resultar em quedas de firewall se apenas IPs públicos forem permitidos. Se você usar um firewall, quando você habilitar o ponto de extremidade de serviço, adicione uma sub-rede para o firewall usando as [ACLs de Rede Virtual](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Atribua um IP público à sua VM do Azure.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>Não é possível acessar o serviço-Firewall
``ConnectTimeoutException``indica que o SDK não pode acessar o serviço.
Você pode obter uma falha semelhante à seguinte ao usar o modo direto:
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Se você tiver um firewall em execução em seu computador de aplicativo, abra o intervalo de portas 10.000 a 20.000 que são usadas pelo modo direto.
Além disso, siga o [limite de conexão em um computador host](#connection-limit-on-host).

#### <a name="http-proxy"></a>Proxy HTTP

Se você usar um proxy HTTP, certifique-se que pode suportar o número de conexões configuradas no SDK `ConnectionPolicy`.
Caso contrário, você enfrentará problemas de conexão.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Padrão de codificação inválido: bloqueio do thread de E/S do Netty

O SDK usa a biblioteca de E/S [Netty](https://netty.io/) biblioteca para se comunicar com o Azure Cosmos DB. O SDK tem uma API assíncrona e usa APIs de e/s sem bloqueio de sub-rede. O trabalho de E/S do SDK é executado em threads de Netty de E/S. O número de threads de E/S Netty está configurado para ser igual ao número de núcleos da CPU no computador do aplicativo. 

Os threads de E/S Netty destinam-se somente a serem usados para o trabalho de E/S Netty sem bloqueio. O SDK retorna o resultado da invocação de API em um dos threads de E/S Netty para o código de aplicativos. Se o aplicativo executa uma operação de longa duração, depois de receber os resultados no thread de Netty, o SDK pode não ter threads de E/S suficientes para realizar seu trabalho de E/S interno. Essa codificação de aplicativo pode resultar em baixa taxa de transferência, latência alta e `io.netty.handler.timeout.ReadTimeoutException` falhas. A solução alternativa é mudar o thread quando você sabe que a operação levará tempo.

Por exemplo, observe o trecho de código a seguir, que adiciona itens a um contêiner (veja [aqui](create-sql-api-java.md) as orientações sobre como configurar o banco de dados e o contêiner). Você pode executar trabalho de longa duração que leva mais de alguns milissegundos no thread de rede. Nesse caso, você, eventualmente, pode colocar em um estado em que nenhum thread de e/s do Netty está presente para processar o trabalho de e/s. Como resultado, você obterá uma falha de ReadTimeoutException.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-readtimeout"></a>API assíncrona do SDK do Java V4 (Maven com. Azure:: Azure-Cosmos)

```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
  int requestTimeoutInSeconds = 10;
  ConnectionPolicy policy = new ConnectionPolicy();
  policy.setRequestTimeout(Duration.ofMillis(requestTimeoutInSeconds * 1000));
  AtomicInteger failureCount = new AtomicInteger();
  // Max number of concurrent item inserts is # CPU cores + 1
  Flux<Family> familyPub = 
      Flux.just(Families.getAndersenFamilyItem(), Families.getWitherspoonFamilyItem(), Families.getCarltonFamilyItem());
  familyPub.flatMap(family -> {
      return container.createItem(family);
  }).flatMap(r -> {
      try {
          // Time-consuming work is, for example,
          // writing to a file, computationally heavy work, or just sleep.
          // Basically, it's anything that takes more than a few milliseconds.
          // Doing such operations on the IO Netty thread
          // without a proper scheduler will cause problems.
          // The subscriber will get a ReadTimeoutException failure.
          TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
      } catch (Exception e) {
      }
      return Mono.empty();
  }).doOnError(Exception.class, exception -> {
      failureCount.incrementAndGet();
  }).blockLast();
  assert(failureCount.get() > 0);
}
```

A solução alternativa é alterar o thread em que você executa o trabalho demorado. Defina uma instância singleton do agendador para seu aplicativo.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>API assíncrona do SDK do Java V4 (Maven com. Azure:: Azure-Cosmos)

```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = Schedulers.fromExecutor(ex);
```
Você talvez precise fazer o trabalho que leva tempo, por exemplo, recursos computacionais pesados corporativos ou bloqueio de e/s. Nesse caso, alterne o thread para um trabalhador fornecido pelo seu `customScheduler` usando a `.publishOn(customScheduler)` API.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-apply-custom-scheduler"></a>API assíncrona do SDK do Java V4 (Maven com. Azure:: Azure-Cosmos)

```java
container.createItem(family)
    .publishOn(customScheduler) // Switches the thread.
    .subscribe(
        // ...
    );
```
Usando `publishOn(customScheduler)`, você libera o thread de E/S Netty e muda para seu próprio thread personalizado fornecido pelo agendador personalizado. Essa modificação resolve o problema. Você não obterá uma `io.netty.handler.timeout.ReadTimeoutException` falha mais.

### <a name="request-rate-too-large"></a>Taxa de solicitação grande demais
Essa falha é uma falha no lado do servidor. Indica que você consumiu sua taxa de transferência provisionada. Tente novamente mais tarde. Se você recebe essa falha com frequência, considere aumentar a taxa de transferência da coleção.

* **Implementar a retirada em intervalos de getRetryAfterInMilliseconds**

    Durante o teste de desempenho, você deve aumentar a carga até que uma pequena taxa de solicitações seja restringida. Se restringida, o aplicativo cliente deve retirar a limitação para a nova tentativa do servidor especificado. Respeitar a retirada garante que você perca o mínimo de tempo de espera entre as tentativas.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Falha ao conectar-se ao emulador do Azure Cosmos DB

O certificado HTTPS do emulador do Azure Cosmos DB é autoassinado. Para o SDK funcionar com o emulador, importe o certificado do emulador para um Java TrustStore. Para obter mais informações, consulte [Exportar certificados do emulador do Azure Cosmos DB](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Problemas de conflito de dependência

O SDK do Java Azure Cosmos DB recebe uma série de dependências; em termos gerais, se a árvore de dependência do projeto incluir uma versão mais antiga de um artefato para o qual Azure Cosmos DB SDK do Java depende, isso poderá resultar na geração de erros inesperados quando você executar o aplicativo. Se você estiver Depurando por que seu aplicativo lança uma exceção inesperadamente, é uma boa ideia verificar se a sua árvore de dependência não está puxando acidentalmente uma versão mais antiga de uma ou mais das dependências do SDK do Java Azure Cosmos DB.

A solução alternativa para esse problema é identificar qual das dependências do projeto traz a versão antiga e excluir a dependência transitiva nessa versão mais antiga e permitir que Azure Cosmos DB SDK de Java traga a versão mais recente.

Para identificar qual das dependências do projeto traz uma versão mais antiga de algo que Azure Cosmos DB SDK do Java depende, execute o seguinte comando em seu arquivo pom. XML do projeto:
```bash
mvn dependency:tree
```
Para obter mais informações, consulte o [Guia de árvore de dependência do Maven](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

Depois de saber qual dependência do seu projeto depende de uma versão mais antiga, você pode modificar a dependência da lib em seu arquivo POM e excluir a dependência transitiva, seguindo o exemplo abaixo (o que pressupõe que *reator-Core* é a dependência desatualizada):

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-reactor}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-reactor}</artifactId>
  <version>${version-of-lib-which-brings-in-reactor}</version>
  <exclusions>
    <exclusion>
      <groupId>io.projectreactor</groupId>
      <artifactId>reactor-core</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Para obter mais informações, consulte o [guia Excluir dependência transitiva](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Habilitar o log do SDK do cliente

Azure Cosmos DB SDK do Java v4 usa SLF4j como a fachada de log que dá suporte ao logon em estruturas de log populares, como Log4J e logback.

Por exemplo, se você quiser usar log4j como a estrutura de registros, adicione estas bibliotecas ao seu classpath de Java.

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Adicione também uma configuração de log4j.
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Para obter mais informações, consulte o [manual de log sfl4j](https://www.slf4j.org/manual.html).

## <a name="os-network-statistics"></a><a name="netstats"></a>Estatísticas de rede do sistema operacional
Execute o comando netstat para ter uma noção de quantas conexões estão nos estados como `ESTABLISHED` e `CLOSE_WAIT`.

No Linux, você pode executar o comando a seguir.
```bash
netstat -nap
```

No Windows, você pode executar o mesmo comando com sinalizadores de argumento diferentes:
```bash
netstat -abn
```

Filtre o resultado somente para conexões ao ponto de extremidade do Azure Cosmos DB.

O número de conexões ao ponto de extremidade do Azure Cosmos DB no `ESTABLISHED` estado não deve ser maior que o tamanho do pool de conexão configurado.

Muitas conexões para o ponto de extremidade do Azure Cosmos DB podem estar no `CLOSE_WAIT` estado. Pode haver mais de 1.000. Um número tão alto indica que as conexões são estabelecidas e interrompidas rapidamente. Essa situação potencialmente causa problemas. Para obter mais informações, consulte a seção [Problemas comuns e soluções alternativas].

 <!--Anchors-->
[Problemas comuns e soluções alternativas]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limite de conexão em um computador host]: #connection-limit-on-host
[Esgotamento de porta de SNAT do Azure (PAT)]: #snat


