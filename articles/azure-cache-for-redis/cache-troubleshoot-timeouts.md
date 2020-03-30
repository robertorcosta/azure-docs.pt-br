---
title: Solucionar problemas de tempo limite do Cache do Azure para Redis
description: Saiba como resolver problemas comuns de tempo comum com o Azure Cache for Redis, como patches de servidor redis e exceções de tempo do StackExchange.Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 4b8cfed883ffef780de2e82e3f309e97bcb5515c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278240"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Solucionar problemas de tempo limite do Cache do Azure para Redis

Esta seção discute problemas de tempo de solução de problemas que ocorrem ao conectar-se ao Cache do Azure para Redis.

- [Patching do servidor Redis](#redis-server-patching)
- [Exceções de tempo limite do StackExchange.Redis](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> Várias das etapas de solução de problemas neste guia incluem instruções para executar comandos do Redis e monitorar diversas métricas de desempenho. Para obter mais informações, consulte os artigos na seção [Informações adicionais](#additional-information) .
>

## <a name="redis-server-patching"></a>Patching do servidor Redis

O Azure Cache for Redis atualiza regularmente seu software de servidor como parte da funcionalidade de serviço gerenciado que ele fornece. Esta atividade [de correção](cache-failover.md) ocorre em grande parte atrás da cena. Durante os failovers quando os nós do servidor Redis estão sendo corrigidos, os clientes Redis conectados a esses nós podem experimentar intervalos temporários à medida que as conexões são alternadas entre esses nós. Veja [como um failover afeta meu aplicativo cliente](cache-failover.md#how-does-a-failover-affect-my-client-application) para obter mais informações sobre o que o patch de efeitos colaterais pode ter em seu aplicativo e como você pode melhorar seu manuseio de eventos de patches.

## <a name="stackexchangeredis-timeout-exceptions"></a>Exceções de tempo limite do StackExchange.Redis

StackExchange.Redis usa uma `synctimeout` configuração nomeada para operações síncronas com um valor padrão de 1000 ms. Se uma chamada síncrona não for concluída nesse tempo, o cliente StackExchange.Redis lançará um erro de tempo definido semelhante ao seguinte exemplo:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Essa mensagem de erro contém métricas que podem ajudar a indicar a causa e a possível resolução do problema. A tabela a seguir contém detalhes sobre as métricas da mensagem de erro.

| Métrica da mensagem de erro | Detalhes |
| --- | --- |
| inst |Na última fração de tempo: 0 comandos foram emitidos |
| mgr |O gerente do `socket.select`soquete está fazendo, o que significa que está pedindo ao Sistema Operacional para indicar um soquete que tem algo a fazer. O leitor não está lendo ativamente da rede porque não acha que há nada a fazer |
| fila |Existem 73 operações em andamento no total |
| qu |6 das operações em andamento estão na fila de não enviados e ainda não foram escritas para a rede de saída |
| qs |67 das operações em andamento foram enviadas ao servidor, mas uma resposta ainda não está disponível. A resposta pode ser `Not yet sent by the server` ou `sent by the server but not yet processed by the client.` |
| qc |0 das operações em andamento viram respostas, mas ainda não foram marcadas como completas porque estão esperando o loop de conclusão |
| wr |Há um escritor ativo (o que significa que os 6 pedidos não enviados não estão sendo ignorados) bytes/activewriters |
| in |Não há nenhum leitor ativo e zero bytes estão disponíveis para serem lidos no NIC bytes/activereaders |

Você pode usar as seguintes etapas para investigar possíveis causas básicas.

1. Como uma prática recomendada, certifique-se de que você está usando o seguinte padrão para se conectar ao usar o cliente StackExchange.Redis.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    Para saber mais, confira [Conectar-se ao cache usando StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Certifique-se de que seu servidor e o aplicativo cliente estejam na mesma região do Azure. Por exemplo, você pode estar recebendo tempo outs quando seu cache está no Leste dos `synctimeout` EUA, mas o cliente está no Oeste dos EUA e a solicitação não é concluída dentro do intervalo ou você pode estar recebendo tempo outs quando você está depurando de sua máquina de desenvolvimento local. 

    É altamente recomendável ter o cache e o cliente na mesma região do Azure. Se você tem um cenário que inclui chamadas entre regiões, é necessário definir o intervalo de `synctimeout` como um valor maior que o intervalo padrão de 1.000 ms, incluindo uma propriedade `synctimeout` na cadeia de conexão. O exemplo a seguir mostra um trecho de uma seqüência de conexão para StackExchange.Redis fornecido pelo Azure Cache para Redis com um `synctimeout` de 2000 ms.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Certifique-se de estar usando a versão mais recente do [Pacote NuGet do StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Constantemente são corrigidos bugs no código para torná-lo mais robusto com relação a tempos limite, de modo que ter a versão mais recente é importante.
1. Se suas solicitações estiverem vinculadas a limitações de largura de banda no servidor ou cliente, leva mais tempo para que elas sejam concluídas e podem causar intervalos. Para ver se o tempo hábil é devido à largura de banda da rede no servidor, consulte [limitação de largura de banda do lado do servidor](cache-troubleshoot-server.md#server-side-bandwidth-limitation). Para ver se o tempo hábil é devido à largura de banda da rede do cliente, consulte [a limitação de largura de banda do lado do cliente](cache-troubleshoot-client.md#client-side-bandwidth-limitation).
1. Você está sendo limitado à CPU no servidor ou no cliente?

   - Verifique se você está ficando preso pela CPU em seu cliente. A CPU alta pode fazer com que `synctimeout` a solicitação não seja processada dentro do intervalo e causar uma solicitação de tempo. Mudar para um tamanho maior do cliente ou distribuir a carga pode ajudar a controlar esse problema.
   - Verifique se você está recebendo CPU vinculada no servidor monitorando a métrica de desempenho do [cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)da CPU . As solicitações que chegam enquanto redis está vinculado à CPU podem fazer com que essas solicitações desemtempo. Para resolver essa condição, você pode distribuir a carga em vários fragmentos em um cache premium ou atualizar para um tamanho maior ou um nível de preço. Para obter mais informações, consulte [a limitação de largura de banda do lado do servidor](cache-troubleshoot-server.md#server-side-bandwidth-limitation).
1. Há comandos que levam muito tempo para serem processados no servidor? Comandos de longa duração que estão demorando muito para processar no servidor redis podem causar intervalos de tempo. Para obter mais informações sobre comandos de longa duração, consulte [comandos de longo prazo](cache-troubleshoot-server.md#long-running-commands). Você pode se conectar ao seu Cache Azure para ocorrência Redis usando o cliente redis-cli ou o [Console Redis](cache-configure.md#redis-console). Em seguida, execute o comando [SLOWLOG](https://redis.io/commands/slowlog) para ver se há solicitações mais lentas do que o esperado. O Servidor do Redis e o StackExchange.Redis são otimizados para várias solicitações pequenas, em vez de menos solicitações grandes. Dividir os dados em partes menores pode melhorar as coisa.

    Para obter informações sobre como se conectar ao ponto final SSL do cache usando redis-cli e stunnel, consulte o post do blog [Anunciando ASP.NET Provedor de Estado de Sessão para Liberação de Visualização Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).
1. Uma carga alta no servidor Redis pode resultar em tempos limite. Você pode monitorar a carga do servidor monitorando a  [métrica de desempenho de cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)`Redis Server Load`. Uma carga de servidor de 100 (valor máximo) significa que o servidor Redis está ocupado, sem tempo ocioso, processando de solicitações. Para ver se determinadas solicitações estão consumindo toda a capacidade do servidor, execute o comando SlowLog, conforme descrito no parágrafo anterior. Para obter mais informações, confira Alto nível de uso da CPU/carga de servidor.
1. Houve qualquer outro evento no lado do cliente que possa ter causado um problema na rede? Eventos comuns incluem: dimensionamento do número de instâncias do cliente para cima ou para baixo, implantação de uma nova versão do cliente ou escala automática ativada. Em nossos testes, descobrimos que a escala automática ou a escala para cima/para baixo podem fazer com que a conectividade de rede de saída seja perdida por vários segundos. O código do StackExchange.Redis é resiliente a tais eventos e se reconectará. Durante a reconexão, qualquer solicitação na fila pode ficar sem tempo.
1. Houve uma grande solicitação precedendo várias pequenas solicitações para o cache que se esvaiu? O parâmetro `qs` na mensagem de erro informa quantas solicitações foram enviadas do cliente para o servidor, mas não processou uma resposta. Esse valor pode continuar crescendo porque o StackExchange.Redis usa uma única conexão TCP e só pode ler uma resposta por vez. Mesmo que a primeira operação tenha sido cronometrada, ela não impede que mais dados sejam enviados para ou do servidor. Outras solicitações serão bloqueadas até que a grande solicitação seja concluída e possam causar intervalos de tempo. Uma solução é minimizar a chance de tempos limite, garantindo que o cache seja grande o suficiente para sua carga de trabalho e dividindo valores grandes em partes menores. Outra solução possível é usar um pool de objetos `ConnectionMultiplexer` no seu cliente e escolher o `ConnectionMultiplexer` menos carregado ao enviar uma nova solicitação. O carregamento em vários objetos de conexão deve impedir que um único intervalo de outras solicitações também desempume o tempo.
1. Se estiver usando, `RedisSessionStateProvider`certifique-se de definir o tempo de repetição corretamente. `retryTimeoutInMilliseconds` deve ser maior do que `operationTimeoutInMilliseconds`; caso contrário, não há novas tentativas. No exemplo a seguir, `retryTimeoutInMilliseconds` é definido como 3000. Para obter mais informações, consulte [provedor de estado de sessão ASP.NET para Cache do Azure para Redis](cache-aspnet-session-state-provider.md) e [Como usar os parâmetros de configuração do provedor de estado de sessão e do provedor de cache de saída](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. Verifique o uso de memória no servidor do Cache do Azure para Redis [monitorando](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` e `Used Memory`. Se uma política de remoção estiver em vigor, o Redis começará a remover chaves quando `Used_Memory` atingir o tamanho do cache. Idealmente, `Used Memory RSS` deve ser apenas um pouco maior do que `Used memory`. Uma grande diferença significa que há fragmentação da memória (interna ou externa). Quando `Used Memory RSS` é menor que `Used Memory`, significa que parte da memória cache foi trocada pelo sistema operacional. Se a troca ocorrer, você poderá esperar que haja algumas latências significativas. Como redis não tem controle sobre como suas alocações são `Used Memory RSS` mapeadas em páginas de memória, alta é muitas vezes o resultado de um pico no uso da memória. Quando o servidor Redis libera a memória, o alocador tira a memória, mas pode ou não devolver a memória ao sistema. Pode haver uma discrepância entre o valor de `Used Memory` e o consumo de memória, conforme relatado pelo sistema operacional. A memória pode ter sido usada e liberada por Redis, mas não redada ao sistema. Para ajudar a mitigar problemas de memória, você pode fazer as seguintes etapas:

   - Atualize o cache para um tamanho maior para que você não esteja executando contra as limitações de memória no sistema.
   - Definir tempos de expiração das chaves para que valores mais antigos sejam removidos de forma proativa.
   - Monitorar a métrica de cache `used_memory_rss`. Quando esse valor se aproxima do tamanho do cache deles, é provável que você comece a ver problemas de desempenho. Distribua os dados em vários fragmentos se estiver usando um cache premium ou atualize para um tamanho de cache maior.

   Para obter mais informações, consulte [A pressão de memória no servidor Redis](cache-troubleshoot-server.md#memory-pressure-on-redis-server).

## <a name="additional-information"></a>Informações adicionais

- [Solucionar problemas no lado do cliente do Cache do Azure para Redis](cache-troubleshoot-client.md)
- [Solucionar problemas no lado do servidor do Cache do Azure para Redis](cache-troubleshoot-server.md)
- [Como medir e testar o desempenho do meu cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Como monitorar o Cache do Azure para Redis](cache-how-to-monitor.md)
