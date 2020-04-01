---
title: Melhores práticas para o Cache do Azure para Redis
description: Aprenda a usar seu Cache Azure para Redis de forma eficaz seguindo essas práticas recomendadas.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 105a3996753a1d1c2d71846cc8bad574e4498acf
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478614"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Melhores práticas para o Cache do Azure para Redis 
Ao seguir essas práticas recomendadas, você pode ajudar a maximizar o desempenho e o uso econômico do seu Cache Azure para a instância Redis.

## <a name="configuration-and-concepts"></a>Configuração e conceitos
 * **Use o nível Standard ou Premium para sistemas de produção.**  O nível Basic é um sistema de nó único sem replicação de dados e sem SLA. Além disso, use pelo menos um cache C1.  Os caches C0 são destinados a cenários simples de desenvolvimento/teste, uma vez que eles têm um núcleo de CPU compartilhado, pouca memória e são propensos a problemas de "vizinho barulhento".

 * **Lembre-se que Redis é um armazenamento de dados na memória.**  [Este artigo](cache-troubleshoot-data-loss.md) descreve alguns cenários onde a perda de dados pode ocorrer.

 * **Desenvolva seu sistema de forma que ele possa lidar com falhas** de conexão por causa de patches e [failover](cache-failover.md).

 * **Configure a [configuração maxmemory reservada](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para melhorar a capacidade de resposta do sistema** em condições de pressão de memória.  Uma configuração de reserva suficiente é especialmente importante para cargas de trabalho pesadas de gravação ou se você estiver armazenando valores maiores (100 KB ou mais) em Redis. Você deve começar com 10% do tamanho do seu cache e aumentar essa porcentagem se você tiver cargas pesadas de gravação.

 * **Redis funciona melhor com valores menores,** então considere cortar dados maiores em várias chaves.  [Nesta discussão redis,](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)algumas considerações são listadas que você deve considerar cuidadosamente.  Leia [este artigo](cache-troubleshoot-client.md#large-request-or-response-size) para obter um problema de exemplo que pode ser causado por valores grandes.

 * **Localize sua instância de cache e seu aplicativo na mesma região.**  Conectar-se a um cache em uma região diferente pode aumentar significativamente a latência e reduzir a confiabilidade.  Embora você possa se conectar de fora do Azure, ele não é recomendado *especialmente ao usar Redis como cache*.  Se você estiver usando redis como apenas uma loja de chaves/valor, a latência pode não ser a principal preocupação. 

 * **Reutilizar conexões.**  Criar novas conexões é caro e aumenta a latência, por isso reutilize as conexões o máximo possível. Se você optar por criar novas conexões, certifique-se de fechar as conexões antigas antes de liberá-las (mesmo em linguagem de memória gerenciada como .NET ou Java).

 * **Configure sua biblioteca cliente para usar um tempo limite de *conexão* de pelo menos 15 segundos,** dando ao sistema tempo para se conectar mesmo em condições mais altas da CPU.  Um pequeno valor de tempo de tempo de conexão não garante que a conexão seja estabelecida nesse período de tempo.  Se algo der errado (CPU de alto cliente, CPU de servidor alto e assim por diante), então um curto valor de tempo de conexão fará com que a tentativa de conexão falhe. Esse comportamento muitas vezes piora a situação.  Em vez de ajudar, os tempos mais curtos agravam o problema forçando o sistema a reiniciar o processo de reconexão, o que pode levar a um loop *de conexão -> falha - > loop de repetição.* Geralmente recomendamos que você deixe seu tempo limite de conexão em 15 segundos ou mais. É melhor deixar sua tentativa de conexão ter sucesso após 15 ou 20 segundos do que fazê-la falhar rapidamente apenas para tentar novamente. Esse loop de repetição pode fazer com que sua paralisação dure mais do que se você deixar o sistema demorar mais inicialmente.  
     > [!NOTE]
     > Esta orientação é específica para a *tentativa de conexão* e não está relacionada ao tempo que você está disposto a esperar por uma *operação* como GET ou SET para ser concluída.
 
 * **Evite operações caras** - Algumas operações Redis, como o comando [KEYS,](https://redis.io/commands/keys) são *muito* caras e devem ser evitadas.  Para obter mais informações, consulte algumas considerações em torno [de comandos de longa duração](cache-troubleshoot-server.md#long-running-commands)

 * **Use criptografia TLS** - O Cache do Azure para Redis requer comunicações criptografadas TLS por padrão.  As versões TLS 1.0, 1.1 e 1.2 são suportadas atualmente.  No entanto, o TLS 1.0 e o 1.1 estão no caminho para a depreciação em todo o setor, por isso use o TLS 1.2, se possível.  Se a sua biblioteca ou ferramenta do cliente não suportar o TLS, então a ativação de conexões não criptografadas pode ser feita [através do portal do Azure](cache-configure.md#access-ports) ou das [APIs de gerenciamento](https://docs.microsoft.com/rest/api/redis/redis/update).  Nesses casos em que conexões criptografadas não são possíveis, seria recomendado colocar seu cache e aplicativo cliente em uma rede virtual.  Para obter mais informações sobre quais portas são usadas no cenário de cache de rede virtual, consulte esta [tabela](cache-how-to-premium-vnet.md#outbound-port-requirements).
 
## <a name="memory-management"></a>Gerenciamento de memória
Existem várias coisas relacionadas ao uso da memória dentro da instância do servidor Redis que você pode querer considerar.  Aqui estão algumas:

 * **Escolha uma [política de despejo](https://redis.io/topics/lru-cache) que funcione para sua aplicação.**  A política padrão do Azure Redis é *volátil-lru,* o que significa que apenas as chaves que possuem um conjunto de valor TTL serão elegíveis para despejo.  Se nenhuma tecna tiver um valor TTL, o sistema não despejará nenhuma teca.  Se você quiser que o sistema permita que qualquer chave seja despejada se estiver sob pressão de memória, então você pode querer considerar a política *allkeys-lru.*

 * **Defina um valor de expiração em suas chaves.**  Uma expiração removerá as chaves proativamente em vez de esperar até que haja pressão de memória.  Quando o despejo entra em ação por causa da pressão da memória, pode causar carga adicional no servidor.  Para obter mais informações, consulte a documentação dos comandos [EXPIRE](https://redis.io/commands/expire) e [EXPIREAT.](https://redis.io/commands/expireat)
 
## <a name="client-library-specific-guidance"></a>Orientação específica da biblioteca do cliente
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java - Qual cliente devo usar?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Alface (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Provedor de Estado de Sessão de Asp.Net](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Quando é seguro tentar novamente?
Infelizmente, não há resposta fácil.  Cada aplicativo precisa decidir quais operações podem ser repetidas e quais não podem.  Cada operação tem requisitos diferentes e dependências inter-chave.  Aqui estão algumas coisas que você pode considerar:

 * Você pode obter erros do lado do cliente, mesmo que Redis tenha executado com sucesso o comando que você pediu para executar.  Por exemplo: 
     - Os tempos outs são um conceito do lado do cliente.  Se a operação chegar ao servidor, o servidor executará o comando mesmo que o cliente desaguarde a espera.  
     - Quando ocorre um erro na conexão do soquete, não é possível saber se a operação realmente foi operacional no servidor.  Por exemplo, o erro de conexão pode acontecer depois que o servidor processou a solicitação, mas antes que o cliente receba a resposta.
 *  Como meu aplicativo reage se eu acidentalmente executar a mesma operação duas vezes?  Por exemplo, e se eu incrementar um inteiro duas vezes em vez de uma vez?  Minha aplicação está escrita para a mesma chave de vários lugares?  E se minha lógica de reavaliação sobrepor um valor definido por alguma outra parte do meu aplicativo?

Se você quiser testar como seu código funciona em condições de erro, considere usar o [recurso Reinicialização](cache-administration.md#reboot). A reinicialização permite que você veja como os blips de conexão afetam seu aplicativo.

## <a name="performance-testing"></a>Testes de desempenho
 * **Comece usando `redis-benchmark.exe` ** para ter uma noção de possível throughput/latência antes de escrever seus próprios testes perf.  A documentação redis-benchmark pode ser [encontrada aqui](https://redis.io/topics/benchmarks).  Observe que o redis-benchmark não suporta TLS, então você terá que [ativar a porta Non-TLS através do Portal](cache-configure.md#access-ports) antes de executar o teste.  [Uma versão compatível com windows do redis-benchmark.exe pode ser encontrada aqui](https://github.com/MSOpenTech/redis/releases)
 * A VM do cliente usada para testes deve estar **na mesma região que a** instância de cache Redis.
 * **Recomendamos o uso da Série Dv2 VM** para o seu cliente, pois eles têm melhor hardware e darão os melhores resultados.
 * Certifique-se de que o VM do cliente que você usa tem **pelo menos tanto computação e largura de banda* quanto o cache que está sendo testado. 
 * **Habilite o VRSS** na máquina cliente se você estiver no Windows.  [Consulte aqui para obter detalhes](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Exemplo de script powershell:
     >PowerShell -ExecutionPolicy Unrestricted Enable-NetAdaptrRSS -Name (Get-NetAdapter). Nome 
     
 * **Considere usar instâncias Premium tier Redis**.  Esses tamanhos de cache terão melhor latência e throughput de rede porque estão executando em hardware melhor tanto para CPU quanto para rede.
 
     > [!NOTE]
     > Nossos resultados de desempenho observados são [publicados aqui](cache-faq.md#azure-cache-for-redis-performance) para sua referência.   Além disso, esteja ciente de que o SSL/TLS adiciona algumas despesas gerais, para que você possa obter diferentes latências e/ou throughput se estiver usando criptografia de transporte.
 
### <a name="redis-benchmark-examples"></a>Exemplos de Redis-Benchmark
**Configuração pré-teste**: Prepare a instância de cache com os dados necessários para os comandos de latência e teste de throughput listados abaixo.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10-d 1024 

**Para testar a latência**: Teste OBTER solicitações usando uma carga útil de 1k.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**Para testar o throughput:** Solicitações GET pipelined com 1k de carga útil.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -n 1000000 -d 1024 -P 50 -c 50
